---
title: Facebook Webhooks API로 lead ads와 CRM 통합
date: 2018-07-30 21:14:40
photos:
 - /images/webhook.png
tags:
 - Facebook
 - LeadAds
 - AWS
 - Lambda
 - APIGateway
---
-------------------------
이 페이지는 [이 문서](https://developers.facebook.com/docs/marketing-api/guides/lead-ads/quickstart/webhooks-integration)를 따라 테스트해보면서 추가적인 내용을 적어둔것이니 문서를 먼저 읽는것이 좋음!

#### 미리 설정해두면 좋은것들
- App 설정
- Page 설정 및 Lead Ad 설정
- App, Page 관리자 권한 얻기

-----------------------
![](/images/webhook.png)

테스트를 하면서 알았는데 페이스북 페이지에서 바로 webhook 설정을 할수 있는게 아니었다;
Lead ad는 페이스북 페이지에서, webhook은 페이스북 앱에서 각각 설정해줘야하고, 페이스북 페이지를 앱이 구독할수 있도록 승인이 필요하다.
즉 Lead ad의 실시간 업데이트가 앱으로 전달되고, 앱에 설정된 webhook을 통해 Lead ad로 받은 고객정보를 처리할수 있게된다.

문서를 보면 웹서버에 php 파일을 생성하라고 나와있는데, 대신 AWS Lambda와 API Gateway를 사용해서 테스트했다. 로컬서버로 테스트해보려다 잘안돼서 빠른 포기 -.-;;
API Gateway에서 메소드 생성시 GET, POST 둘다 생성해줘야한다. (아마 페이지 구독 설정시에는 GET, 실제 lead ad payload 전송시엔 POST 로 보내는듯)

각 메소드의 Integration Request에서 Use lambda proxy integration에 체크하면 Lambda 함수에 대한 입력을 요청 헤더, 경로 변수, 쿼리 문자열 파라미터 및 본문의 조합으로 표현할 수 있다.
해당 API로 들어오는 리퀘스트의 내용을 그대로 Lambda 함수로 전달하고 이것을 event로 쉽게 핸들링할수 있다.

```python
# 구독설정시 lambda 함수 예제
def lambda_handler(event, context):
    challenge = event['queryStringParameters']['hub.challenge']
    verify_token = event['queryStringParameters']['hub.verify_token']

    # if verify_token == 'test_token':
    #     print(challenge)

    response = {
        "isBase64Encoded": False,
        "statusCode": 200,
        "headers": {},
        "body": challenge
    }

    return response
```

대신 response 형식도 아래와 같이 맞춰줘야한다.
```json
{
    "isBase64Encoded": true|false,
    "statusCode": httpStatusCode,
    "headers": { "headerName": "headerValue", ... },
    "body": "..."
}
```

모든 설정을 완료한 후에 https://developers.facebook.com/tools/lead-ads-testing 에서 테스트를 해보면 제대로 동작해보는지 확인할수 있다.
앱 관리 페이지에서 webhook - leadgen Test 에서도 테스트 가능하다.

body에 유저가 입력한 form data가 같이 넘어오는줄 알았는데 그게 아니었음... 실시간 업데이트에 대한 데이터가 아래처럼 넘어온다. ㅠㅠ
```json
array(
  "object" => "page",
  "entry" => array(
    "0" => array(
      "id" => 153125381133,
      "time" => 1438292065,
      "changes" => array(
        "0" => array(
          "field" => "leadgen",
          "value" => array(
            "leadgen_id" => 123123123123,
            "page_id" => 123123123,
            "form_id" => 12312312312,
            "adgroup_id" => 12312312312,
            "ad_id" => 12312312312,
            "created_time" => 1440120384
          )
        ),
        "1" => array(
          "field" => "leadgen",
          "value" => array(
            "leadgen_id" => 123123123124,
            "page_id" => 123123123,
            "form_id" => 12312312312,
            "adgroup_id" => 12312312312,
            "ad_id" => 12312312312,
            "created_time" => 1440120384
          )
        )
      )
    )
  )
)
```
그래서 넘어온 id를 가지고 Facebook Ads API를 이용해서 데이터를 받아와야한다 ㅠㅠ 페이스북놈들...
```python
# Python Business SDK
from facebookads.adobjects.lead import Lead
from facebookads.api import FacebookAdsApi

access_token = '<ACCESS_TOKEN>'
app_secret = '<APP_SECRET>'
app_id = '<APP_ID>'
id = '<ID>'
FacebookAdsApi.init(access_token=access_token)

fields = [
]
params = {
}
print Lead(id).get(
  fields=fields,
  params=params,
)
```
```json
# response
{
  "created_time": "2015-02-28T08:49:14+0000",
  "id": "<LEAD_ID>",
  "ad_id": "<AD_ID>",
  "form_id": "<FORM_ID>",
  "field_data": [{
    "name": "car_make",
    "values": [
      "Honda"
    ]
  },
  {
    "name": "full_name",
    "values": [
      "Joe Example"
    ]
  },
  {
    "name": "email",
    "values": [
      "joe@example.com"
    ]
  },
  {
    "name": "selected_dealer",
    "values": [
      "99213450"
    ]
  }],
  ...
}
```
