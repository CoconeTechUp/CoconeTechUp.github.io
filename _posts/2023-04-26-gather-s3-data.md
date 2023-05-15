---
layout: post
title:  "분리된 망 환경에서의 데이터 수집 & 합산"
comments: true   // 댓글 허용 여부
excerpt: "분리된 망 환경에서의 데이터 수집 & 합산"
date: 2023-05-15 // 글 작성일
tags: [aws, s3, spring-batch, lambda] // 포스트에 달 태그
thumbnail: https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/architecture.png // 포스트 썸네일
---

MLD 에서는 일본, 대만, 한국, 북미 지역에 서비스를 운영하고 있습니다. 각 국가/문화 별 특성으로 인해 서비스 데이터 분리가 필요했고, 지역별로 별도로 서비스를 운영하고 있습니다. 그러는 와중에 신규 기능 중 전 국가 유저들에게 투표를 받아 진행하는 컨텐츠 개발 요구사항이 들어왔습니다 :grimacing:

이 때, 가장 큰 문제는 각 지역의 망이 분리 되있어서 상호 통신이 불가능한 상태입니다.

### 분리된 망 환경에서의 데이터 수집 & 계산

운영되는 망 환경이 전부 통일되있는게 가장 좋은 상황이지만, ~~어른의 사정~~으로 인해 망 통합은 어려운 상황입니다.
이런 경우에 각 지역에서 데이터를 pull하는 방식이 아닌 push하는 방식으로 구조를 설계하였습니다.

- 서비스 구조

아래 도식은 JP 와 기타 국가로 나뉘어진 망에서의 서비스 구조를 나타내고 있습니다.

![architecture](https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/architecture.png)

서비스의 흐름은 다음과 같이 이뤄집니다.

1. jenkins에서 spring batch job을 주기적으로 실행
2. batch job 이 redis에서 데이터를 읽어와 s3로 csv파일 업로드
3,4. lambda에서 각 국의 데이터를 읽어들여 정규화 후 합산한 결과를 저장
5. batch job 에서 주기적으로 s3를 읽어들여 redis 에 글로벌 데이터 업로드
6. API 서버에서 redis에서 필요한 데이터를 조회하여 사용

### Spring batch 구현

각 국가별 redis에서 투표 결과를 읽어들여 sorted set으로 저장하고 있습니다. 국가별로 오픈된 시점이 다르기 때문에 사용자 수가 큰 차이가 나고 취향도 다르기 때문에  고려해야될 사항이 있습니다.

- 각 국가별로 사용자 수, 취향이 차이 나므로 공평한 투표를 위해 데이터 정규화
- 기간 내의 투표이므로 대세가 정해지는 추이면 업데이트를 자주 할 필요가 없음
- 업데이트 시 매우 소량의 비용(lambda 실행 비용) 발생

 위 특성을 고려하였을 때, batch에서 파일이 업로드 될 때마다 AWS lambda를 호출하는 과정이 있으므로 적절하게 배치 주기를 조절해줄 필요가 있습니다. jenkins에 cronjob을 설정하여 주기를 조절할 수 있게 구성하였습니다.

- spring batch upload task 구현 : 투표 결과를 csv로 변환하여 s3로 업로드 하고 있습니다.

```java
    @Bean
    @StepScope
    public Tasklet uploadTask(@Value("#{jobParameters['evno']}") Integer evno) {
        return (stepContribution, chunkContext) -> {

            Map<Triple, Integer> data = getKeywordScores(evno);

            ByteArrayOutputStream outputStream = new ByteArrayOutputStream();

            try (CSVWriter csvWriter = new CSVWriter(new OutputStreamWriter(outputStream))) {
                String[] header = {"kseq", "score"};
                csvWriter.writeNext(header);

                for (Map.Entry<Triple, Integer> entry : data.entrySet()) {
                    String[] row = {String.valueOf(entry.getKey()), String.valueOf(entry.getValue())};
                    csvWriter.writeNext(row);
                }
            }

            String formattedDate = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyyMMddHHmm"));
            String dataFileKey = String.format("/data/%d/%s-%s.csv", evno,
                    COUNTRY.toLowerCase(),
                    formattedDate);

            PutObjectRequest putObjectRequest = PutObjectRequest.builder()
                    .bucket(bucket)
                    .key(dataFileKey)
                    .build();

            PutObjectResponse resp = Mono.fromFuture(s3AsyncClient.putObject(putObjectRequest, AsyncRequestBody.fromBytes(outputStream.toByteArray())))
                    .subscribeOn(Schedulers.boundedElastic())
                    .block();

            return resp.sdkHttpResponse().isSuccessful() ? RepeatStatus.FINISHED : RepeatStatus.CONTINUABLE;

        };
    }
```

- spring batch redis에 저장하는 task 구현 : csv 파일을 다운받은 후 redis에 저장한 결과를 slack으로 보내주고 있습니다.

```java
    @Bean
    @StepScope
    public Tasklet saveGlobalResultTask(@Value("#{jobParameters['evno']}") Integer evno) {
        return (stepContribution, chunkContext) -> {
            Map<Triple<Integer, Integer, Integer>, Double> scoreMap = getFileContent(bucket, "togetherpick/result/" + evno + "/result-global.csv")
                    .map(data -> convertCsvData2Map(data))
                    .block();

            Set<DefaultTypedTuple<Triple<Integer, Integer, Integer>>> tuples = scoreMap.entrySet().stream()
                    .map(entry -> new DefaultTypedTuple<>(entry.getKey(), entry.getValue()))
                    .collect(Collectors.toSet());

            redisClusterTemplate.opsForZSet().add(RANK_KEY + evno + GLOBAL_KEY, tuples);

            slackService.sendTogetherpickDataUpdateNoti(evno, scoreMap);

            return RepeatStatus.FINISHED;
        };
    }
```

### lambda 구현

Spring batch에서 s3에 csv파일을 업로드하면 lambda로 이벤트를 전달시키기 위해 lambda에 s3 이벤트를 설정해줍니다.
**접두사를 명확히 지정하지 않는 경우 s3 <-> lambda 간 호출이 무한정 발생할 수 있으므로 주의해야합니다.**


![s3trigger](https://s3.ap-northeast-1.amazonaws.com/tane.coconeimg.jp/img/s3-trigger.png)

위 설정이 완료되었으면 아래 lambda code에서 s3 파일 생성 이벤트가 넘어오는 것을 확인할 수 있습니다.

```python
def lambda_handler(event, context):
    
    # s3 업로드시 넘어온 bucket과 key정보
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    
    # key({이벤트 번호}/{국가}-{환경}-{날짜}.csv}) 파싱
    evno, locale, stage = parse_path(key)
    
    # 가장 최신 파일 조회
    latest_file = get_latest_file(bucket, evno, locale)
    
     # 데이터 정규화
    result_df = get_normalized_result(bucket, latest_file)

    # s3에 최신 데이터 결과 업로드
    upload_s3_result(bucket, result_df, evno, locale)
    
    # 합산한 global 데이터 업로
    upload_global_result(bucket, evno)
    
    return {
        'statusCode': 200,
        'body': { 'bucket' : bucket, 'evno' : evno, 'locale': locale, 'stage' : stage}
    }
```

### 후기

망이 분리된 상황에서 데이터의 쓰기&읽기를 분리하여 이벤트 기반으로 처리하는 서비스를 개발하였습니다. AWS를 사용한 만큼 인프라에 대한 문제를 쉽게 해결할 수 있었는데요. 반면에 관리 포인트가 늘어나는 문제가 있으므로 s3의 만료시간이라던가, lambda의 비용 관리 등 사전에 설정을 잘 정의하여 최대한 관리 포인트를 줄이도록 하는 것이 중요할 것 같습니다. 