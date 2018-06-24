---
title: DynamoDB Stream
date: 2018-06-24 20:55:31
photos:
 - /images/dynamodb_stream.jpg
tags:
 - DynamoDB
 - AWS
 - Stream
---
--------------------

DynamoDB 스트림은 DynamoDB 테이블 항목의 변경 정보에 대한 흐름을 나타내준다.
스트림 이벤트는 INSERT, MODIFY, DELETE 세가지로 나눠진다.

![Manage Stream](/images/manage_stream.png)
DDB 테이블의 overview 탭에 보면 Stream Detail 항목이 있는데 여기서 뷰 타입을 설정 가능하다.
이벤트가 발생한 아이템의 키만 보여주던지, 새 이미지만 보여주던지, 기존 이미지만 보여주던지, 둘다 보여주던지 총 네가지 타입이 있다.
물론 스트림을 비활성화하는것도 가능하다.

![Trigger 추가](/images/add_trigger.png)
Trigger 탭에서 스트림에 대한 Lambda 트리거를 쉽게 추가할 수 있다.
새 Lambda function을 추가할수도 있고, 존재하는 function을 추가할 수도 있다.
function을 추가하면서 batch size도 설정 가능한데 나중에 설정해둔 사이즈를 확인할 수 있는 곳을 못찾아서 한참 헤맸다;
해당 Lambda function에 들어가서 Designer 항목에서 DynamoDB를 선택하면 아래에 정보를 띄워준다.
이걸 몇달 후에나 알았음 -.-

수백만건의 데이터를 DynamoDB에 때려넣다가 에러는 안나는데 결과 데이터의 오차율이 너무 커서 꽤 오래 고생을 했는데 원인은 단순했다.
DynamoDB Stream은 최대 24시간동안만 저장한다는 사실...
트리거 function이 단순한 로직이 아니라면 스트림이 쌓여있다가 트리거가 돌기전에 휘발될수 있으니 주의하자 ㅠㅠ 
