---
layout: post
title:  "apps script 업무 활용 이야기"
comments: true
excerpt: "apps script 사용기"
date:   2023-02-23
tags: [appsscript, 자동화]
thumbnail: https://user-images.githubusercontent.com/64643665/220688437-b1ee3c10-3ac8-4d32-9e97-37ef55615022.png
---

안녕하세요 SYF 컴퍼니 infra팀의 신입 DBA 입니다.

업무 관련 스프레드 시트 문서 정리를 위해 apps script를 활용했던 사례를 공유드리고자 합니다.

## 배경
최근 업무 중 MongoDB의 인덱스를 뽑아 정리하는 작업을 진행했습니다.   

![image](https://user-images.githubusercontent.com/64643665/220705315-afd97c2b-2ea3-4479-a3ba-cfe49f26caf7.png)

위와 같이 스크립트를 작성하여 MongoDB의 콜렉션 별로 인덱스를 추출하여 csv 형식으로 저장하는 과정 까지는 성공했으나, 하나의 콜렉션에 다수의 인덱스가 존재하는 경우 다소 보기 불편하다는 피드백을 받았습니다.   

<img width="1185" alt="image" src="https://user-images.githubusercontent.com/64643665/220706106-dd4ee79f-ab32-48fe-8b6a-b35cfdb4d859.png">

같은 콜렉션끼리 셀 병합을 하여 위와 같이보기 좋은 시트를 작성할 수 있었으나, 느린 저의 손으로는 수백개의 콜렉션을 다 그룹핑 하는 데 하루를 꼬박 써야 할 위기에 처했습니다. 반드시 필요한 작업이 아닌데도 불구하구요.

![image](https://user-images.githubusercontent.com/64643665/220687603-cc4ccacf-b7e5-4d5a-8646-9209f3d073ec.png)

고민과 구글링 끝에 구글 스프레드 시트에도 엑셀의 매크로(VBA)와 같은 기능을 제공한다는 것을 알게 되었습니다. 그리고 다행히도 apps script를 사용하면 셀 병합을 자동화 할 수 있다는 사실도 알게되었습니다.

## apps script란?
![image](https://user-images.githubusercontent.com/64643665/220688437-b1ee3c10-3ac8-4d32-9e97-37ef55615022.png)

apps script는 Google 제품 전반에서 작업을 자동화 할 수 있도록 하는 클라우드 기반 자바스크립트 플랫폼 입니다.   
저는 apps script를 Google 스프레드 시트의 커스텀 함수를 작성하는데만 사용했으나, 그 외에 아래와 같은 작업을 할 수도 있습니다.
- Google Docs, Sheets, Forms에 맞춤 메뉴, 대화상자 및 사이드바 추가
- Google Sheets 커스텀 함수, 매크로 작성
- 독립형 혹은 Google Sites에 삽입된 웹 앱 게시
- 애드센스, 애널리틱스, Calender, Drive, Gmail, 지도 등 Google 서비스와 상호작용
- 부가기능 빌드하고 Google Workspace Marketplace에 게시

## apps script를 스프레드 시트에서 사용하는 방법
스프레드 시트에서 apps script를 생성하고, 실행하는 방법에 대해 설명드리겠습니다.

1. 확장프로그램 > Apps Script 를 선택합니다.   
<img width="656" alt="image" src="https://user-images.githubusercontent.com/64643665/220692015-d267b7cc-df3f-4cdc-9022-3a0c4fa004c8.png">

2. 함수 부분에 원하는 기능의 소스코드를 입력합니다.     
<img width="812" alt="image" src="https://user-images.githubusercontent.com/64643665/220693578-e51b3390-e3d5-475a-b791-ac8f9d56b6fb.png">

우선 예시를 위해 간단히 첫 행을 고정하는 기능을 작성해보았습니다.
<img width="808" alt="image" src="https://user-images.githubusercontent.com/64643665/220694775-44ac5afd-b87c-424b-b2bc-437e2651c4c4.png">

여기서 `SpredsheetApp` 은 Google Sheets 파일에 액세스 할 수 있도록 하는 클래스이며, `getActiveSheet()` 를 통해 스프레드시트 UI에 표시되고 있는 시트(현재 사용자가 보고 있는 시트)를 가져오고 `setFrozenRows(rows)` 로 rows행을 고정시켜줍니다.    

스프레드 시트에서 사용하는 apps script의 다양한 클래스와 매소드에 대한 더 많은 정보는 [공식 문서](https://developers.google.com/apps-script/reference/spreadsheet?hl=ko)에서 확인 가능합니다.


3. 프로젝트 저장     
<img width="802" alt="image" src="https://user-images.githubusercontent.com/64643665/220698082-bb894a7f-f481-4295-8e4c-e95f8bac2a65.png">

4. 실행 및 접근 권한 부여     
<img width="811" alt="image" src="https://user-images.githubusercontent.com/64643665/220698392-c147d698-8c74-4cb2-9339-ccec026ae28c.png">

실행을 누르면, 아래와 같이 앱스 스크립트가 스프레드 시트에 접근하기 위한 권한을 부여하는 과정을 거치게 됩니다.

<img width="460" alt="image" src="https://user-images.githubusercontent.com/64643665/220698517-2c2802f8-7538-4b3e-9e98-6decda1a71ea.png">

권한 승인을 위해 계정에 로그인 한 뒤, 액세스 요청을 허용 해 줍니다.

![image](https://user-images.githubusercontent.com/64643665/220699448-b97ecaab-4d7c-4d18-b46f-58d16cb7c555.png)   

<img width="631" alt="image" src="https://user-images.githubusercontent.com/64643665/220699795-2d8b4481-2d1c-4dae-b5d7-9a883ec8dad5.png">

<img width="504" alt="image" src="https://user-images.githubusercontent.com/64643665/220700073-0688f273-7349-4088-bee2-5a8d067fe01b.png">


5. 실행 결과 확인

위 과정들이 정상적으로 완료 되면, 아래와 같은 실행 로그가 남게 됩니다.    
<img width="835" alt="image" src="https://user-images.githubusercontent.com/64643665/220700397-e8ffd043-dd14-441c-9c5a-89891c063eb9.png">

스프레드 시트에서도 아래와 같이 작성한 소스코드(1행 고정) 명령이 완수된 것을 확인할 수 있습니다.

<img width="1091" alt="image" src="https://user-images.githubusercontent.com/64643665/220706359-1d39364f-418b-4614-8f90-19e6affb3fb1.png">



## 내가 사용한 apps script
이제 본격적으로 이번 업무에 활용한 apps script를 소개드리겠습니다.

```javascript
  // 현재 UI의 Sheet 가져오기
  var sheet = SpreadsheetApp.getActiveSheet();
```
먼저 현재 사용자가 확인중인 페이지의 시트를 가져옵니다.    

```javascript
  // 1행 고정
  sheet.setFrozenRows(1);
```
스크롤을 내려도 컬럼명을 확인할 수 있도록 첫번째 행을 고정시켜주는 작업을 진행합니다.

```javascript
  var start = 2; // 작업 시작할 row

  // A열의 모든 값(collection name)을 가져오기
  var data = sheet.getRange(start, 1, sheet.getLastRow(), 1).getValues().filter(String);
```
`.getRange(startRow, startColumn, numRows, numColumns)` 는 (startRow, startColumn) 위치의 객체부터, numRows 행과 numColumns 열 만큼 반환합니다. 위 코드에서는 2행 1열부터, 마지막 행(`sheet.getLastRow()`) 1열 까지 데이터를 가져오게 됩니다.

```javascript
  var count = {};
  // 중복되는 collection name 만큼 cnt
  // ex) [[collname1], [collname1], [collname2]] => {'collname1': 2, 'collname2': 1,}
  data.forEach(function (e) { count[e[0]] = count[e[0]] ? count[e[0]] + 1 : 1; });
```
앞선 과정에서 가져온 2행 1열~마지막행 1열 까지의 데이터(=collection name 데이터)를 바탕으로 count dict를 만들어줍니다. count dict에 collection name(=`e[0]`) key가 존재하면 value에 +1을, 없으면 `e[0] : 1` key-value 쌍을 생성해줍니다.

```javascript
  var collname = "";
  // Merge cells.
  data.forEach(function (e) {
    // 새로운 collection name일 경우
    if (collname != e[0]) {
      // 현재 검사중인 row(start+offset)부터 중복되는 collection name cnt 개수만큼을 병합
      sheet.getRange(start + offset, 1, count[e[0]], 1).merge();
      // count 열도 병합
      sheet.getRange(start + offset, 4, count[e[0]], 1).merge();
      sheet.getRange(start + offset, 7, count[e[0]], 1).merge();
      // 병합한 개수(중복되는값 cnt)만큼을 offset에 더해줌
      offset += count[e[0]];
    }
    collname = e[0];
  });
```
최종적으로 count dict를 활용해 셀을 병합 해 줍니다.     

새롭게 검사하는 collection name(`e[0]`)이 직전값과 다른 경우, 현재 검사중인 row 부터 collection name 개수만큼 더한 row까지 병합을 진행합니다(`.merge()`). **데이터가 collectoin name 기준으로 정렬되어 있다**는 것을 전제 조건으로 두고 작업을 진행했습니다.

병합 이후, 병합한 만큼 offset을 더해 다음 작업 시 `start+offset` 행 부터 작업을 할 수 있도록 작성합니다.


위 내용을 합한 전체 코드 입니다.
```javascript
function mergeCell() {
  // 현재 UI의 Sheet 가져오기
  var sheet = SpreadsheetApp.getActiveSheet();

  var start = 2; // 작업 시작할 row
  var offset = 0;

  // 중복값 체크를 위한 dict, str 선언
  var count = {};
  var collname = "";

  // 1행 고정
  sheet.setFrozenRows(1);

  // A열의 모든 값(collection name)을 가져오기
  var data = sheet.getRange(start, 1, sheet.getLastRow(), 1).getValues().filter(String);

  // 중복되는 collection name 만큼 cnt
  // ex) [[collname1], [collname1], [collname2]] => {'collname1': 2, 'collname2': 1,}
  data.forEach(function (e) { count[e[0]] = count[e[0]] ? count[e[0]] + 1 : 1; });

  // Merge cells.
  data.forEach(function (e) {
    // 새로운 collection name일 경우
    if (collname != e[0]) {
      // 현재 검사중인 row(start+offset)부터 중복되는 collection name cnt 개수만큼을 병합
      sheet.getRange(start + offset, 1, count[e[0]], 1).merge();
      // count 열도 병합
      sheet.getRange(start + offset, 4, count[e[0]], 1).merge();
      sheet.getRange(start + offset, 7, count[e[0]], 1).merge();
      // 병합한 개수(중복되는값 cnt)만큼을 offset에 더해줌
      offset += count[e[0]];
    }
    collname = e[0];
  });
}
```


## 글을 마치며
google apps script와 이를 문서 업무에서 활용했던 내용을 공유드렸습니다.   
사실 테크 블로그 주제로 삼기에는 살짝 거리가 먼 내용일 수 있지만, 문서화 작업이 필요한 크루에게 도움이 되고자 적어보았습니다.

누군가에게는 도움이 되었기를 바라며, 이상 글을 마칩니다.   
읽어주셔서 감사합니다~ ^_^

## Reference
- https://developers.google.com/apps-script/overview?hl=ko
- https://stackoverflow.com/questions/49212749/merge-cells-with-same-words 
