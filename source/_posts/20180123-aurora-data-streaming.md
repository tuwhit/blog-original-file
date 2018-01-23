---
title: Lambda, Kinesis Firehose 를 이용해서 AuroraDB에 추가된 데이터 실시간으로 캡쳐하기
date: 2018-01-23 20:23:50
photos:
 - /images/Workflow.jpg
tags:
 - Lambda
 - AWS
 - Kinesis Firehose
 - S3
 - AuroraDB
---
------------------

![Workflow](/images/Workflow.jpg)


데이터 스트리밍 관련 리서치를 하다가 [이런](https://aws.amazon.com/ko/blogs/database/capturing-data-changes-in-amazon-aurora-using-aws-lambda/) 문서가 있길래 직접 해봤다.(AuroraStream이 따로 없고 DynamoStream만 있는듯) 회사에선 AuroraDB를 써서 일단 요 실습을 따라해보는걸로.

사실 자세한건 위 링크에 다 나와있다...ㅎㅎ;; 간단히 요약하면서 중간중간 삽질한것만 추가로 기록해봤음.

1. Kinesis 에서새로운 delivery stream을 생성하고 Destination을 S3로 선택해준뒤, 원하는 버켓을 지정해줌
2. AuroraDB 와 같은 리전에서 새 Lambda function 을 생성
 - Lambda 코드에서 stream_name 만 1번에서 만든 stream 이름으로 변경
 - 혹시 firehose 가 다른 리전에 있다면 firehose = boto3.client('firehose', region_name='리전 이름') 이렇게 뒤에 리전 이름 추가해줌
3. AuroraDB에서 원하는 테이블에 프로시저, 트리거 생성
 - 프로시저에서 2에서 생성해준 Lambda function의 arn 으로 설정해줌
4. AuroraDB parameter group에서 aws_default_lambda_role 의 value 를 lambda 를 실행시킬수있는 role의 arn으로 설정
 - 안해주면 Missing Credentials: Cannot instrantiate Lambda Client 에러남
5. 테이블에 데이터를 추가
 - Lambda에서 예외처리를 따로 안해줘서 필드가 null 일땐 에러나는듯
6. 좀있다가 S3 버킷 확인해보면 요렇게 파일이 생겨있음
![S3에 생긴 파일을 확인해보면!](/images/AuroraToS3.png)
7. 그래도 에러나면 IAM 제대로 설정돼있는지 확인 ㄱㄱ
