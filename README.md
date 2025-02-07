# Open-IM-Server Call

### 说明:
```
由于OpenIMSDK 的视频通话功能是闭源的,阻挡了想了解视频通话功能的伙伴。
通过分析OpenIMSDK后,给出视频通话功能的原理和服务器代码。
客户端的实现,参考livekit。
本功能只用于学习交流。 

官方 Open-IM-SDK-Core (客户端sdk的核心部分) 已经包含了音视频通信部分，
但客户端demo是闭源的，所以客户端可以基于livekit-sdk自行实现视音频部分(参考livekit,livekit都是开源的)
```
### 功能:
实现 [OpenIMSDK](https://github.com/OpenIMSDK/Open-IM-Server) 的视频通话功能。

### 特点:
跟OpenIMSDK 官方一样基于 开源的视频服务[livekit](https://livekit.io/) 实现。
兼容 OpenIMSDK 官方的demo。


### 原理:
IM只做用户信息,好友管理,消息的透传等。视频通话是通过livekit实现(webrtc)。

步骤
1. A发起视频:A发送SignalReq_Invite 信令(包含房间号,被邀请人)
2. 服务器接受信令,创建livekit的token,将token返回给发起方,同时透传发起方的消息给接受方。
3. 接收方收到OnReceiveNewInvitation回调
4. 接收方接受视频,向服务器发送SignalReq_Accept 信令。
5. 服务器接受信令,创建livekit的token,将token返回给接收方,同时透传接收方的消息给接发送方。
6. 发起方收到OnInviteeAccepted回调。
7. 接收方和发起方通过各自的token进入视频聊天房间(此过程是见livekit/webrtc)


### 使用说明
[直接使用Open-IM-Server + Call 整合好的分支](https://github.com/shoyu666/Open-IM-Server)

##### [安装livekit](https://docs.livekit.io/guides/getting-started/)
```
linux
curl -sSL https://get.livekit.io | bash
livekit-server --dev

注意：必须要有https域名和证书
```


##### 修改配置
```
rtc:
  signalTimeout: 35
  #livekit的服务地址
  liveURL: wss://www.xxxx.xx:7890
  #livekit的key(部署livekit时自定义)
  key: xxx
  #livekit的secret(部署livekit时自定义)
  secret: xxx
 
```
##### 代码目录
和openim service目录一致
如:cmd\rpc\open_im_real_time_comm

##### 运行
运行 cmd/rpc/open_im_real_time_comm/main.go
运行后会注册服务名为：RealTimeComm 的rpc服务。
修改 OpenIM demo的服务器地址,发起视频通话。


#### 问题记录
##### 问题：missing SignalGetRooms method https://github.com/shoyu666/Open-IM-Server-Call/issues/2#issue-1399210934
```
解决方案：您可以使用 Open_IM-Server 老的版本

原因：Open_IM-Server 源分支在 9月28号提交的代码，增加了 SignalGetRooms方法 
[提交记录](https://github.com/OpenIMSDK/Open-IM-Server/commit/249d5e27887391253547519cd177d766e77a7f00#diff-33db5c101b755c6d95d5eb12faa1165ea82556dbe0d5d969ad73f87a6c7eceb7)

目前Open_IM-Server版本  SignalGetRooms 方法实际没有用到,可能后续版本这个方法会被用到。

也可以使用 Open-IM-Server + Call 整合好的分支 https://github.com/shoyu666/Open-IM-Server
```

![image](https://github.com/shoyu666/Open-IM-Server-Call/blob/7787f14fd1e1c91b488b5e3a83e058a86dd7e4bb/preview.gif)
