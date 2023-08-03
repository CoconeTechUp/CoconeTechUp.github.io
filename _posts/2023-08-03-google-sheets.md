layout: post
title:  "구글 시트를 이용하여 데이터 입력하기"
comments: true   
excerpt: "Google API를 활용한 데이터 입력 개선기"
date:   2023-08-03 
tags: [Library, Google API, Data] 
thumbnail: 2023-08-03-google-sheets/Untitled.png

# 구글 시트를 이용하여 데이터 입력하기

![Google Sheets](2023-08-03-google-sheets/Untitled.png)

안녕하세요!

Cocone M 마이리틀돌 개발팀입니다.

이번에는 마이리틀돌 팀에서 구글 문서를 이용하여 데이터를 관리하고 입력하는 방법에 대해서 정리해보려 합니다.

---

## 기존의 데이터 관리 방법

마이리틀돌 팀에서는 데이터를 관리할 때 대부분의 데이터를 구글 문서로 정리하고 관리를 하고 있습니다.

그러나 데이터를 입력하는 것은 별도의 CMS 페이지에서 시트를 보고 직접 손으로 입력을 하고 있었습니다.

팝업에 데이터를 하나씩 입력해서 데이터 하나를 추가하고, 이런 데이터를 필요한 만큼 추가합니다. (보통 가챠 하나에 10개 항목을 수십개에서 100개 사이로 넣는 것으로 알고 있습니다)

이런 방식으로 입력하기에는 휴먼 미스가 발생할 수 있는 요소가 많을 수 밖에 없으며, 그 만큼 발생하는 이슈도 많아졌었습니다.

기존에 이 부분을 해결하기 위해서 시트 파일을 csv 파일로 옮겨서 입력하는 방법도 사용하긴 했습니다만, 이 방법은 숫자같은 걸 사용할 때 형식때문에 문제가 생기는 경우도 있고, 파일로 저장하는 부분이 번거롭다는 단점이 있었습니다.

마이리틀돌 팀에서는 이 부분을 구글 시트 API를 이용하여, 시트 URL 하나만 있으면 데이터를 불러올 수 있도록 수정을 했었습니다.

---

## 구글 시트 API를 사용하는 법

우선 구글 API를 사용하여 데이터를 받아오는 법 부터 정리해겠습니다.

서버에서 시트의 데이터를 받아오려면 API를 사용할 구글 클라우드 계정과 해당 계정의 토큰 값 등이 필요합니다.

저 같은 경우에는 마이리틀돌 프로젝트용 구글 클라우드 계정이 이미 있어 해당 계정을 사용하였으며, 계정 역시 전달받아서 사용하긴 하였지만, 직접 만들면서 정리하겠습니다.

![New Project](2023-08-03-google-sheets/Untitled%201.png)

우선, [구글 클라우드 콘솔](https://console.cloud.google.com/)에  접속하여 프로젝트를 생성합니다. 이미 만들어져 있는 프로젝트가 있으시다면 해당 프로젝트를 사용해주시면 됩니다.

![Google Sheets API 1](2023-08-03-google-sheets/Untitled%202.png)

![Google Sheets API 2](2023-08-03-google-sheets/Untitled%203.png)

![Google Sheets API 3](2023-08-03-google-sheets/Untitled%204.png)

그 후, 프로젝트에서 [Google Sheets API](https://console.cloud.google.com/marketplace/product/google/sheets.googleapis.com)를 추가해주셔야 합니다.

Google Sheets API는 콘솔 좌측 하단의 제품 더 보기에서 Marketplace에서 찾아보실 수 있으며, 검색창에 간단히 Sheets 정도만 입력해주셔도 바로 나타나며, 사용을 눌러주시면 됩니다.

![New System User 1](2023-08-03-google-sheets/Untitled%205.png)

그 후 이런 식으로 사용자 인증 정보가 필요하다고 나오는데, API를 사용하려면 반드시 필요합니다. 이미 있으시다면 해당 인증 정보를 사용해주시면 됩니다. 없으시다면 만들기 버튼을 눌러 만들어 줍니다. 

![New System User 2](2023-08-03-google-sheets/Untitled%206.png)

저는 이 정도로 간단하게 만들어보겠습니다. 이후에 계정 이름같은 부분은 크게 중요하진 않고, 역할은 시트를 읽어올 수 있는 정도면 충분합니다. 저는 귀찮으니 소유자로 설정했습니다..

여기서 서비스 계정의 이메일은 나중에 시트에 공유해야하니 기억해두시면 좋습니다.

![Get Key 1](2023-08-03-google-sheets/Untitled%207.png)

![Get Key 2](2023-08-03-google-sheets/Untitled%208.png)

![Get Key 3](2023-08-03-google-sheets/Untitled%209.png)

만들어진 서비스 계정의 키는 IAM 및 관리자 - 서비스 계정 에 있는 계정을 선택하셔서 키를 받으셔서 사용하시면 됩니다.

파일 형식은 저는 일단 JSON 형식으로 받아왔습니다. 아마 대부분의 환경에서는 기본적으로 JSON 형식 사용하도록 되어있을 겁니다.

이제 해당 키를 서비스에서 사용할 수 있도록 세팅해주시고, 서비스에 API 라이브러리 등을 이용하여 추가해 주신 뒤, 해당 키를 연결해주시면 됩니다.

자세한 방법은 [공식 가이드](https://developers.google.com/sheets/api/guides/concepts?hl=ko)에 잘 정리되어있으니 참고해주시면 될 것 같습니다.

---

## 시트에서 데이터를 받는 법

시트에 정리되어있는 데이터 중 일부는 API를 이용해서 받아오기에 형태가 복잡한 것도 있습니다.

그렇다고 그대로 받아와서 서버에서 처리하기에는 매번 추가할 때 마다 서버 수정이 필요해 번거로운 부분도 많을 것 같아, 같은 시트에서 서버에서 받아오기 편한 방식으로 한 번 변환하여 가져오는 방법을 사용하였습니다.

저희 서버같은 경우에는 MongoDB를 사용하고 있어, 그에 맞는 형식으로 데이터를 만들어서 임포트 하도록 했습니다.

Array, Object와 같은 부분은 JSON 형식으로 변환하여 넘겨주고, 서버에서는 JSON을 해석해서 데이터를 입력하는 방식으로 만들어 두었습니다.

시트 함수를 이것저것 사용하니 생각보다 쉽게 정리할 수 있었습니다.

작업을 하면서 정말 유용하게 썼던 함수가 있는데요, 바로 **ARRAYFORUMULA** 함수입니다.

간단하게 설명하면, 지정한 범위만큼 내용을 반복해주는 함수라고 봐주시면 좋을 것 같습니다.

데이터 입력은 드래그 하는거랑 크게 다를 건 없지만, 중간에 원본 데이터가 드래그 되어도 수식이 망가지지 않아서 편했습니다. 귀찮게 드래그 끝까지 하지 않아도 되는것도 편했구요.

활용하는 방법이 정말 많아 반복 작업을 많이 줄여주어 시간을 많이 아낄 수 있었습니다. 좀 더 자세한 내용은 다른 글에서 확인해주세요…

![Temp Functions](2023-08-03-google-sheets/Untitled%2010.png)

덤으로, 작업 하면서 개인적으로 제일 애 먹었던 수식입니다.. ARRAYFORMULA가 없었으면 셀 하나로 정리가 불가능 했었는데, 지금 생각해도 이 정도로 해결되어서 다행이었다고 생각합니다..

---

## 서버에서 데이터를 변환하는 법

이제 데이터 정리도 끝났고, 받아오기만 하면 됩니다.

크게 할 부분은 이미 시트에서 끝나있고, 서버에서는 데이터를 받아와서 JSON 파싱해서 데이터 입력만 하면 크게 문제는 없습니다.

```java
// 시트 데이터 불러오기
List<List<Object>> dataList = GoogleSheetsUtil.getSheetData(sheetUrl, sheetName);

// 저장할 데이터 형식 가져오기
Map<Integer, Field> fieldMap = new HashMap<>();

// 첫 줄은 데이터 키
boolean isFirst = true;

for(int rowIndex = 0; rowIndex < dataList.size(); rowIndex++ ) {

    List<Object> rows = dataList.get(rowIndex);

		// 첫줄일 경우 키 세팅
    if (isFirst) {
				// 시트의 키 리스트
				List<String> sheetFieldList = rows.stream().map(Object::toString).collect(Collectors.toList());

				// 클래스의 모든 필드
				Field[] fieldList  = EVENT_SHEET.getClass().getDeclaredFields();

				// 시트에 클래스의 필드의 키가 있을 경우 초기화
        for(int index = 0; index < sheetFieldList.size(); index++) {
            String sheetField = sheetFieldList.get(index);

            for(Field field : fieldList) {
                if(sheetField.equals(field.getName())) {
                    field.setAccessible(true);
                    fieldMap.put(index, field);
                    break;
                }
            }

        }
        isFirst = false;
        continue;
    }

		// 실제 데이터
    EVENT_SHEET mongoRowData = null;

    try {
				// 신규 객체 생성
        mongoRowData = EVENT_SHEET.getClass().newInstance();
    } catch (Exception e) {
        StringBuilder builder = new StringBuilder();

        builder.append(tabNameBuilder.toString())
                .append("의 객체 생성에 실패하였습니다. 서버팀에 문의 부탁드립니다.");

        result.setErrorMessage(builder.toString());
    }

		// 데이터 입력
    for (int index = 0; index < rows.size(); index++) {
				// 필드 정보 가져오기
        Field field = fieldMap.get(index);
        if (field == null) {
            StringBuilder builder = new StringBuilder();

            builder.append(tabNameBuilder.toString())
                    .append("의 ")
                    .append(index)
                    .append("필드가 데이터베이스와 이름이 맞지 않습니다. 확인 부탁드립니다.");

            result.setErrorMessage(builder.toString());

            return result;
        }

				// 시트 데이터 가져오기
        Object fieldValue = rows.get(index);

        if (fieldValue == null) {
            continue;
        }

        try {
						// 시트 데이터가 JSON 형식일 경우 변환
            Object objectValue = JavaTypeConverterUtil.convertToJavaValue(field, fieldValue.toString().trim());

            if(objectValue != null) {
                field.set(mongoRowData, objectValue);
            }
        } catch (Exception e) {
            StringBuilder errorMessegeBuilder = new StringBuilder();

            errorMessegeBuilder.append(tabNameBuilder.toString())
                    .append("의 ")
                    .append(field.getName())
                    .append("가 잘못되었습니다. 입력 값 : ")
                    .append(rows.get(index))
                    .append("\n확인 부탁드립니다.");

            result.setErrorMessage(errorMessegeBuilder.toString());

            e.printStackTrace();

            return result;
        }
    }
		// 입력된 데이터 추가
    mongoRowDataList.add(mongoRowData);
}
```

이렇게 해서 구글 시트를 이용하여 데이터 입력 개선을 했었습니다.

현재는 만들어진지 얼마 되지 않아 아직 사용되고 있는 곳은 많지 않지만, 조금씩 모든 곳에서 사용할 수 있도록 하여, 구글 시트로 모든 데이터 입력을 해결할 수 있도록 할 생각입니다.