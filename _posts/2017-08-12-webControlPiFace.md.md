---
layout: post
title: "Web远程控制树莓派PiFace继电器"
date: 2017-08-12   
tag: 树莓派
---
树莓派3+PiFace完成Web控制继电器
# 1.准备硬件材料
- 树莓派3
- PiFace Digital 2

# 2.更新软件源和程序
确保更新到新版本程序，可以使用国内的软件源：
## 2.1 备份

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo cp /etc/apt/sources.list.d/raspi.list /etc/apt/sources.list.d/raspi.list.bak
```

## 2.2编辑文件

```
sudo nano /etc/apt/sources.list
```

注释第一行在后面添加

```
#deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspbian.org/raspbian/ jessie main contrib non-free rpi
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi
```

然后使用 Ctrl+O 回车后保存文件，Ctrl+X 退出编辑器。
同样，编辑文件

```
sudo nano /etc/apt/sources.list.d/raspi.list
```

注释第一行在后面添加

```
#deb http://archive.raspberrypi.org/debian/ jessie main ui
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspberrypi.org/debian/ jessie main ui
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ jessie main ui
```

然后使用 Ctrl+O 回车后保存文件，Ctrl+X 退出编辑器。
最后执行  更新系统，更新软件包

```
sudo apt-get update && apt-get upgrade -y
```

## 3.安装软件包

```
$ sudo apt-get install python3-pifacedigitalio
$ sudo easy_install -U bottle
```

## 4.编写网页模板
模板文件名index.tpl
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LED Control</title>
<link href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet" media="screen">
<script src="http://code.jquery.com/jquery.js"></script>
<style type="text/css">
#up {
margin-left: 55px;
margin-bottom: 3px;
}
#down {
margin-top: 3px;
margin-left: 55px;
}
</style>
<script>
$(function(){
$("button").click(function(){
$.post("/cmd",this.id,function(data,status){});
});
});
</script>
</head>
<body>
<div id="container" class="container">
<div>
<button id="00" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="01" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="10" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="11" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>
<div>
<button id="20" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="21" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="30" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="31" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="40" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="41" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="50" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="51" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="60" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="61" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="70" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="71" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-up"></button>
</div>

<div>
<button id="alloff" class="btn btn-lg btn-primary glyphicon glyphicon-stop"></button>
<button id="allon" class="btn btn-lg btn-primary glyphicon glyphicon-circle-arrow-down"></button>
</div>
</div>
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
</body>
</html>

```
## 5. 编写python 网页服务器

以下内容另存为 webcontrol.py
```
#!/usr/bin/env python3
from bottle import get,post,run,request,template
import pifacedigitalio
pifacedigital = pifacedigitalio.PiFaceDigital()
up = "up"
down="down"
left = "left"
right = "right"
stop = "stop"
@get("/")
def index():
	return template("index")
@post("/cmd")
def cmd():
	button = request.body.read().decode()
	print("press:"+button)
	if button == "allon":
		for i in range(0,8):
			pifacedigital.leds[i].value = 1
	elif button == "alloff":
		for i in range(0,8):
			pifacedigital.leds[i].value = 0
	elif int(button)>=0:
		ledIndex = int(int(button)/10);
		ledVaule = int(button)%10;
		pifacedigital.leds[ledIndex].value = ledVaule

	return "OK"
run(host="0.0.0.0")

```
## 6.执行脚本

```
$ chmod +x webcontrol.py
$ ./webcontrol.py
```
## 7.浏览器访问
使用浏览器访问 http://ip:8080,就可以通过网页控制PiFace的2个继电器和8个LED了。

<br>

转载请注明：[李俊达的博客](http://wisnic.com) » [点击阅读原文](http://wisnic.com/2017/08/webControlPiFace/)    
