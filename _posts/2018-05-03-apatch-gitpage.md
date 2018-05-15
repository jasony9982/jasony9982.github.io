---
layout: post
title: GitPage, apatch 
categories: tech
description: gitPage 初始化，一些细节的备份
keywords: Gitpage, apatch
---   

> 记录一些常见问题的细节备份   

### 遇到恼人的权限安装或者删除的权限问题的时候在命令中添加`-n /usr/local/bin` 即可执行
```
sudo gem install -n /usr/local/bin cocoapods
```
### GitPage 本地调试
```
//首次本地安装jelly
sudo gem install -n /usr/local/bin jekyll bundler
// 启动本地jeklly 环境
jeklly serve
// 访问
localhost:4000 
//停止jeklly服务
ctrl + c
```

### mac 本地Apatch服务开启
```
// 启动Apache服务
sudo apachectl start
// 重启Apache服务
sudo apachectl restart
// 停止Apache服务
sudo apachectl stop
// 查看Apache版本
httpd -v
```
####  step 1
```
#LoadModule rewrite_module libexec/apache2/mod_rewrite.so
// uncommet this line
LoadModule php7_module libexec/apache2/libphp7.so
#LoadModule perl_module libexec/apache2/mod_perl.so
```
####  step 2

```
#DocumentRoot "/Library/WebServer/Documents"
#<Directory "/Library/WebServer/Documents">
DocumentRoot "/Users/jason/Sites"
<Directory "/Users/jason/Sites">
```

#### step 3
```
// Options FollowSymLinks Multiviews
 Options Indexes FollowSymLinks Multiviews
```


### 和谐版的Alfred 3 在每次开机后，都会提示“是否允许访问通讯录”的弹窗

> sudo codesign -f -d -s - /Applications/Alfred\ 3.app/Contents/Frameworks/Alfred\ Framework.framework/Versions/A/Alfred\ Framework


