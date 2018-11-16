---
layout: post
title: "Ubuntu Unison手动配置示例"
date: 2017-07-18   
tag: 工具
---

# Ubuntu Unison手动配置示例
## 1. unison的安装

```
sudo apt-get install unison
```

## 2. ssh互信的配置
2.1 需要互信的服务器上都要安装openssh-client

```
sudo apt-get install openssh-client
```

2.2 设置ssh服务互信
1.在node0上创建key并配置node1的信任

```
[unison@node0 ~]$ ssh-keygen -t rsa
```

#在提示保存私钥（key）和公钥（public key）的位置时，使用默认值；  
#在提示是否需要私钥密码（passphrase）时，直接敲回车，即不使用私钥密码。  
#之后，将生成一对密钥，id_rsa（私钥文件）和id_rsa.pub（公钥文件），保存在/root/.ssh/目录下。


```
[unison@node0 ~]$ssh node1 cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

#将文件上传到node1,并将公钥添加到node1的 authorized_keys 文件中2.同理，执行以下步骤在node1上创建key并配置node0的信任

```
[unison@node1 ~]$ ssh-keygen -t rsa
[unison@node1 ~]$ssh node0 cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

## 
3.验证信任成功

```
[unison@node1 ~]$ssh node0 cat ~/.ssh/authorized_keys
```
能够正常访问不需要密码则设置成功
## 3. unison配置文件的修改

```
sudo nano [用户目录]/.unison/default.prf
```


修改成如下内容：
log = true
logfile = [用户目录]/.unison/.unison.log
root = [用户目录]/[需要同步的目录]
root = ssh://[备份机的用户名]@[备份机ip]//[备份机用户目录]//[备份目录]
#全自动模式batch = true
#强制以指定目录为标准force = [需要备份文件的原始目录] 

## 4. crontab定时任务的设置
sudo crontab -e

添加新的cron

```
* * * * * /usr/bin/unison
```


其中前面的*为自动运行的时间参数，依次为分，小时，月份中的日期，月份，星期中的日期
[/usr/bin/unison]是unison的命令所在位置
5. 注意：
1.涉及到的目录和命令需要更改为当前用户可访问
2. ssh中的主机ip和目录间要加//

<br>

转载请注明：[李俊达的博客](http://wisnic.com) » [点击阅读原文](http://wisnic.com/2017/07/UnisonConfigExample/)
