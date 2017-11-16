---
title: S3에 있는 csv 파일을 AuroraDB로 import 시키기
date: 2017-11-16 23:14:10
photos:
 - /images/aurora.png
tags:
 - AuroraDB
 - S3
 - Database
 - AWS
 - RDS
---
------------------
데이터 분석 관련 프로젝트를 진행중이라 간만에 포스팅을 한다. 아마 당분간 프로젝트를 진행하며 겪었던 삽질기를 계속 포스팅하지 않을까 싶음... 처음 해보는것들 투성이라 팀원들이 모두 프로야근러가 되었다. 흑흑ㅠㅠ 오늘은 문서가 잘 정리돼있어서 꽤 쉽게 클리어했던 S3에 있는 csv 파일을 AuroraDB에 import 시키는 방법을 정리해본다.

AuroraDB instance를 생성하고 IAM role(case는 물론 RDS로 선택)을 만들어준다. 생성후 permission 탭에서 Attach policy를 클릭하여 S3에 Access할수 있도록 **AmazonS3FullAccess** 를 추가해준다.

그리고 다시 RDS로 돌아가 Parameter group을 생성해준다. 여기서 Type은  **DB Cluster Parameter Group** 으로 설정한다. 생성후에 **aurora_load_from_s3_role** 의 value 값을 위에서 만든 IAM role ARN로 입력해준다.

cluster 메뉴에서 해당 cluster 수정 페이지로 이동하여 DB Cluster Parameter Group을 위에서 생성해준 걸로 설정해준다. 다음 페이지로 넘어가서 Apply Immediately 선택후 완료. 다시 cluster 메뉴로 돌아와 해당 cluster 선택 후 Manage IAM roles 페이지로 이동하여 위에서 만들어준 IAM role을 추가해준다.

여기까지가 데이터를 로드하기위한 준비 끝.

```linux
mysql -h [DB Endpoint] -P [port] -u [user name] -p
```
이후 패스워드를 입력하면 해당 AuroraDB로 접속하게된다.

```mysql
load data from s3 's3-[region]://[bucket name]/[file name]'
into table [table name]
fields terminated by ',';
```

이렇게 하면 s3에 있는 csv 파일의 데이터를 지정한 테이블로 로드하게 된다. 1억 row 짜리 데이터 올리는데 한시간가량 걸렸던것 같음.

위 명령어만으로 로드하면 필드명으로 구분이 안되고 파일에 입력된 순서대로 DB에 저장되니 유의하자. 로드할때 여러가지 옵션이 있는데 [여기](http://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/Aurora.LoadFromS3.html)에서 확인할 수 있다.
