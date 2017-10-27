---
title: AWS 도커 컨테이너 배포 자동화 실습
date: 2017-10-27 21:51:05
photos:
 - /images/Gaming_on_AWS_KR_Banner.jpg
tags:
 - deploy
 - AWS
 - CodePipeline
---
------------------
3일전 Gaming on AWS에 참석해서 '도커 컨테이너 배포 자동화 실습'을 진행했다. 회사에서도 도커 컨테이너를 이용해서 배포를 하고있어서(수동이지만) 적용해볼 수 있을것 같다.

당일에는 실습자료를 보고 따라하기 급급했어서 다시 한번 정리해봤다.

![실습 아키텍쳐](/images/architecture.png)

>CodePipeline으로 배포 프로세스를 구성하고 S3에 저장된 소스코드를 CodeBuild를 통해 컴파일 및 컨테이너 이미지 생성한다. 그리고 CloudFormation을 이용해 ECS에 배포한다.

1. CodePipeline 생성
- Source provider는 소스코드가 저장된 S3 버킷으로 설정하고 CodeBuild를 build provider로 선택
- 코드가 build되어 생성된 컨테이너 이미지는 ECR repository에 저장
- 환경 설정 파일도 S3에 저장하고 CodePipeline 에서 해당 파일을 사용하는 Source 액션 추가
- Dockerize 하는 액션 추가 (여기까지가 Build 스테이지)
- Deploy 스테이지 추가
- CloudFormation으로 환경설정 파일을 참조하여 deploy

설정 완료 후 S3에 환경 설정 파일, 어플리케이션을 업로드하면 CodePipeline 에서 변경사항을 감지하여 배포작업을 수행한다. 왕신기. 추가적으로 Lambda를 이용해서 Blue/Green 배포도 가능하다. 현재 리얼 환경이 아닌 곳으로 배포하고 수동 승인과정을 거치면 환경을 Swap 하도록 할 수 있다.

간단히 정리해놔서 그렇지 실제로 콘솔에서 설정해줘야 하는 것들이 굉장히 많았다. 그 하나하나를 이해하기는 좀 어려워서 따로 공부가 필요할 것 같다. 그리고 실습에서 제공된 환경설정파일들도 실제 서비스 환경설정파일들이랑 같이 보면 좋을듯.
