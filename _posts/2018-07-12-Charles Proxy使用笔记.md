---
layout: post
title: Charles Proxy使用笔记
categories: tech
description:  Charles Proxy使用笔记
keywords:  Charles 抓包 代理
---       

开这篇博文想要记录一下Charles的日常使用。

##  Charles 介绍
在App开发与后台联调过程中，经常会遇到后台数据返回不全，请求参数需要修改，请求需要重发等情况。如果每次都在app端修改，或者自己造数据，会增加工作量，而且每次要重新运行app，浪费时间。
Charles 是一款Mac上的HTTP代理服务器、HTTP监视器、反向代理服务器，可以让开发者监视查看修改所有连接互联网的HTTP通信，包括请求，响应和HTTP头信息等等，俗称“抓包”工具，对于前端和Web开发人员来说是一款很有价值的辅助工具，具有Firefox,Chrome插件，非常不错！
## Charles 安装

官网地址:[https://www.charlesproxy.com](https://www.charlesproxy.com)
请支持正版!
## 使用
### 证书安装
1. 安装电脑证书
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313639882479.png)
注意要在钥匙串中找到对应的证书，设置信任
2. 配置相关环境
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313641079389.png)

    1. SSL Proxying Settings
    ![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313662795265.jpg)
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313641652524.png)
  1. 设置可用状态以及抓取端口信息
  ![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313642351905.png)

3. 设置手机证书
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313640315170.png)
Help-->SSL Proxying
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313640796091.png)
在相关的手机中打开Safari软件,输入图片中默认的地址，手机会自动跳转到证书下载界面，按照提示安装即可.   
在手机wifi http代理中输入服务器地址和端口号
4. 设置chrome代理
chrome浏览器请下载 SwitchyOmega ,代理服务器和端口同上

### 修改request/response
[https://www.mocky.io/](https://www.mocky.io/)这个网站的作用就是利用你专属URL生成特定的返回值。这里我的链接为[https://www.mocky.io/v2/5185415ba171ea3a00704eed](https://www.mocky.io/v2/5185415ba171ea3a00704eed)，得到的返回值为
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313651978753.jpg)
1. 设置断点
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313653236419.jpg)
2. 修改请求参数
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313654451059.jpg)
3. 修改response
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313655006399.jpg)   
4. 返回的修改后的结果
 ![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313655333545.jpg)


### Map Remote
1. 访问[zoolar.tech](zoolar.tech)  正常情况下返回结果为
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313658316083.jpg)
然后我们修改 Map Remote, 直接右键在相应的URL上进行如下配置
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313659622139.jpg)
打开Tools->Map Remote...，启动
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313660307611.jpg)   
返回结果   
![](http://p1b3ifuoy.bkt.clouddn.com/2018-07-12-15313660665710.jpg)




### 文末福利:好礼相送,低调使用(不得用作商业用途)
参考链接:[https://zhile.io](https://zhile.io)
```
// Charles Proxy License
// 适用于Charles任意版本的注册码，谁还会想要使用破解版呢。
// Charles 4.2目前是最新版，可用。
Registered Name: 	https://zhile.io
License Key: 		48891cf209c6d32bf4
```

