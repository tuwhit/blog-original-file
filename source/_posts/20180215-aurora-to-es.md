---
title: Lambda, Kinesis Firehose 를 이용해서 AuroraDB에 추가된 데이터를 실시간으로 ES에 저장하기
date: 2018-02-15 13:41:08
photos:
- /images/diagram-kinesis-firehose-s3-redshift-elasticsearch.png
tags:
- Lambda
- AWS
- KinesisFirehose
- Elasticsearch
- AuroraDB
---
------------------
이번엔 Kinesis Firehose 의 목적지를 Elasticsearch로 설정해서 테스트해봤다. Elasticsearch 새 도메인 올리는데 시간이 좀 걸리니 미리 만들어 두는게 좋음.

1. [Lambda, Kinesis Firehose 를 이용해서 AuroraDB에 추가된 데이터 실시간으로 캡쳐하기](http://tuwhit.github.io/2018/01/23/aurora-data-streaming/) 문서 참고해서 AuroraDB, Lambda 설정까지 완료
2. 새 Elasticsearch domain을 생성
 - kinesis firehose가 elasticsearch 6.0은 지원을 안하니 그 아래 버전으로 생성할것. Elasticsearch 6.0 is not currently supported by Kinesis Firehose. Contact AWS Support for more information. 에러가 뜬다... 따흐흑...
3. 위 domain에 index, type을 생성
```json
    {
        "properties": {
            "ItemID": {"type": "integer"},
            "Category": {"type": "text"},
            "Price": {"type": "float"},
            "Quantity": {"type": "integer"},
            "OrderDate":  {
              "type": "date",
              "format": "strict_date_optional_time||epoch_millis"
            },
            "DestinationState": {"type": "text" },
            "ShippingType": {"type": "text"},
            "Referral": {"type": "text"}
        }
    }
```
4. 새 Kinesis Firehose 를 생성
 - destination을 Amazon Elasticsearch service 선택
 - Amazon Elasticsearch Service destination에 위에서 만든 elasticsearch domain, index, type을 입력
 - IAM에 ES에 대한 권한이 제대로 명시돼 있는지 확인 (알아서 만들어줌)
5. Lambda code 수정
```python
    import boto3
    import json
    import logging

    firehose = boto3.client('firehose', region_name='리전이름')
    stream_name = 'delivery stream 이름'

    def lambda_handler(event, context):
        # for ES
        firehose_data = {
            "ItemID": event['ItemID'],
            "Category": event['Category'],
            "Price": event['Price'],
            "Quantity": event['Quantity'],
            "OrderDate": event['OrderDate'].split()[0] + "T" + event['OrderDate'].split()[1],
            "DestinationState": event['DestinationState'],
            "ShippingType": event['ShippingType'],
            "Referral": event['Referral']
        }

        firehose_data = {'Data': json.dumps(firehose_data)}
        logging.info(json.dumps(firehose_data))

        result = firehose.put_record(DeliveryStreamName=stream_name,Record=firehose_data)
```

6. AuroraDB 테이블에 데이터 추가
7. 얼마후에 ES에 _search 쿼리 날려보면 데이터가 추가된것을 확인할수 있음



---------
 - 기타 참고
  - kinesis 에서 데이터가 잘 넘어가는지 CloudWatch에서 확인
    실패시 S3에 로그저장됨
  - Datetime 포맷은 ES의 Date 포맷에 맞춰서 넣을것
