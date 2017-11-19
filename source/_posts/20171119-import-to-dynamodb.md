---
title: Data Pipeline로 S3에서 DynamoDB로 데이터 import 시키기
date: 2017-11-19 17:57:45
photos:
 - /images/Cb272ufUYAAgr6i.jpg
tags:
 - DynamoDB
 - Data Pipeline
 - S3
 - AWS
---
-----------
이번엔 Data Pipeline을 이용해서 S3에서 DynamoDB로 데이터를 import 시키는 방법을 정리해본다.

DynamoDB에 item을 생성하는데(이틀간 삽질하며) 아래의 방식들을 써봤다.

1. AWS console에서 직접 생성
2. ruby SDK를 이용해서 csv 파일을 한줄한줄 읽어서 item 생성
3. aws dynamodb batch-write-item --request-items file://파일명 명령어를 이용(형식 맞춰줘야함)
4. DataPipeline을 이용해서 S3의 파일을 import

1억건의 데이터를 넣는데는 1, 2번으론 무리가 있었고 3번도 파일 용량제한이 있는지 에러를 내뱉었다. 결국 Data Pipeline으로...

Data Pipeline 을 생성하면 DynamoDB 템플릿이 존재한다. **Import DynamoDB backup data from S3** 을 선택하고 생성해준다. 그리고 Parameter 들만 잘 설정해주면 됨. 유의해야할것은 Data Pipeline을 생성한 리전과 S3 bucket, DynamoDB의 리전이 같아야한다. 안그러면 에러를 뿜뿜함.

![parameter 설정](/images/datapipeline_setting.png)

<center style='color: grey; font-size: 14px;'>아니 리전 입력하라고 해놓고 왜 에러나는지 모를...</center>


파일형식도 맞춰야한다. 열심히 구글링을 해봐도 없어서 새로 Data Pipeline을 생성해서 반대로 Export 시켜봤다 ㅠㅠ

```javascript
{"amount":{"s":"8"},"shop_id":{"s":"100000"},"gender":{"s":"1"},"Id":{"n":"1"},"user_id":{"s":"70167727"},"date":{"s":"2017-01-22"},"birth_year":{"s":"1955"}}
{"amount":{"s":"17"},"shop_id":{"s":"100001"},"gender":{"s":"0"},"Id":{"n":"2"},"user_id":{"s":"29015489"},"date":{"s":"2017-07-05"},"birth_year":{"s":"2010"}}
{"amount":{"s":"11"},"shop_id":{"s":"100001"},"gender":{"s":"1"},"Id":{"n":"4"},"user_id":{"s":"90567446"},"date":{"s":"2017-01-14"},"birth_year":{"s":"1953"}}
```

 그 결과, item별로 {필드1: {데이터 형식: 값}, 필드2: {데이터 형식: 값}} 요런 형태로 파일이 만들어졌다. 그래서 기존의 csv 파일을 위 형태로 컨버팅후(스크립트 만들어서 돌림ㅠㅠ) Data Pipeline을 실행시켜보니 잘 들어감!

그런데 write 속도가 너무 느려서 DynamoDB의 write capacity 값을 올려줬는데도 일정값 이상으로 안올라오길래 왜그런가 했더니 Data Pipeline 설정에 myDDBWriteThroughputRatio 필드가 있었다.(0 ~ 1 사이의 값으로 입력가능) 1으로 수정해주니 입력값으로 잘 올라옴.

다른건 몰라도 이제 DynamoDB에 데이터 입력하는덴 전문가된듯. -_-
