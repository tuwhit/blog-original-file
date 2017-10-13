---
title: 개복치같은 서버를 살리기위한 삽질기(ing)
date: 2017-10-13 20:15:21
photos: 
- /images/img_20141201101744_2ad2a8da.jpg
tags: 
 - Server
 - Lambda
 - node.js
 - UptimeRobot
 - Monitoring
---
------------------
작년에 신규 프로젝트를 진행하면서 기존 ruby 서버가 아닌 python 으로 별도의 서버를 띄웠다. Java로 된 별도의 암호화모듈을 사용해야해서 Py4J을 이용했다. 그런데 이 라이브러리의 문제인지 메모리 누수 때문인지 원인은 모르겠지만 2-3주정도 주기로 암호화모듈쪽에서 에러가 나기 시작하면서 그 이후의 모든 요청에서 같은 에러가 발생했다. 기존에 url lib 를 썼던것을 requests 를 사용하도록 수정했지만 에러 발생주기가 조금 길어졌을 뿐 해결이 되지않았다. 일단 원인을 찾는것은 뒤로 미루고 Lambda와 Cloud Watch를 이용해서 매일 새벽2시에 인스턴스를 리붓시키도록 했다.

```javascript
var AWS = require('aws-sdk');

exports.handler = (event, context, callback) => {
    var ec2 = new AWS.EC2({region: 'ap-northeast-2'});
    
    ec2.rebootInstances({InstanceIds : ['instance-ID'] },function (err, data) {
        if (err) console.log(err.stack);
        else console.log(data);
        context.done(err,data);
    });
};
```

이후 몇달간 서버에 이상이 없는듯 하더니 어느날 아예 500에러가 나기시작했다. -_- 원인을 찾아보려 서버에 접속해봤더니 컨테이너가 아예 없었다. 리붓하면서 컨테이너가 제대로 생성되지 않은것 같았다. Docker 관련 지식이 얕아 원인을 알수가 없어서 일단 인스턴스를 새로 생성하는 것으로 해결을 했다. 그리고 UptimeRobot을 이용해 5분에 한번 서버에 request를 보내고, 문제가 있으면 slack으로 alert message를 남기도록 했다.

![UptimeRobot](/images/KakaoTalk_2017-10-13-20-16-05_Photo_18.png)

<center style='color: grey; font-size: 14px;'>이걸 5분만에 설정할수있다니 21세기 스고이</center>



그런데 깜빡하고 Lambda 코드에 바뀐 인스턴스 ID로 수정하지 않아서(아오) 3주뒤 또 암호화 모듈 에러가 발생했다. 이런 휴먼에러를 발생시키지 않기위해 코드를 몇줄 더 추가했다. ㅠㅠ
```javascript
var AWS = require('aws-sdk');
var https = require('https');
var util = require('util');

var POST_OPTIONS = {
    hostname: 'hooks.slack.com',
    path: 'slack web-hook url',
    method: 'POST',
};

exports.handler = (event, context, callback) => {
    var ec2 = new AWS.EC2({region: 'ap-northeast-2'});
    const failed_message = {
        channel: 'service-alerts',
        text: 'Instace Reboot Failed'
    };
    
    // check if instance exists
    ec2.describeInstances({InstanceIds : ['instance-ID']}, function (err, data){
        if (err) {  // if doesn't exist, send slack alert
            var r = https.request(POST_OPTIONS, function(res) {
                        res.setEncoding('utf8');
                        res.on('data', function (data) {
                            context.succeed("Message Sent: " + data);
                     });
            }).on("error", function(e) {context.fail("Failed: " + e);} );
            r.write(util.format("%j", failed_message));
            r.end();
        } else {    // exist
            // reboot instance
            ec2.rebootInstances({InstanceIds : ['instance-ID'] },function (err, data) {
            if (err) {  // send slack alert
                var r = https.request(POST_OPTIONS, function(res) {
                            res.setEncoding('utf8');
                            res.on('data', function (data) {
                                context.succeed("Message Sent: " + data);
                         });
                }).on("error", function(e) {context.fail("Failed: " + e);} );
                r.write(util.format("%j", failed_message));
                r.end();
            } else console.log(data);
                context.done(err,data);
            });
        }
    });
};
```
리붓시킬 인스턴스를 먼저 찾고 없으면 슬랙으로 alert message를 보내고, 있으면 해당 인스턴스를 리붓시킨다. 리붓시 에러가 나도 슬랙으로 alert message를 보낸다.

애초에 에러의 원인을 찾아서 해결했어야하는데 다른일들로 시간적 여유가 없다보니 그때그때 서버만 살리고 뒷전으로 미뤄뒀던 것에 반성을… 틈틈히 시간내서 원인파악을 해야겠다.