---
title: Mac上supervisor的配置安装及其使用
date: 2018-06-14 16:04:43
tags:
---


## Supervisor

Supervisor (http://supervisord.org) 是一个用 Python 写的进程管理工具，可以很方便的用来启动、重启、关闭进程（不仅仅是 Python 进程）。除了对单个进程的控制，还可以同时启动、关闭多个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor 同时启动所有应用程序而不是一个一个地敲命令启动。

## 安装

```py
brew install supervisor
```

## 开机自启动配置

装完之后会有如下提示

```py
==> Caveats
To have launchd start supervisor now and restart at login:
  brew services start supervisor
Or, if you don't want/need a background service you can just run:
  supervisord -c /usr/local/etc/supervisord.ini
==> Summary
🍺  /usr/local/Cellar/supervisor/3.3.4: 526 files, 6.5MB
```

所以执行如下指令开启开机自启

```py
brew services start supervisor
```

执行完成后可以重启电脑，通过执行

```py
ps -e | grep supervisor
```

检查是否真的启动

![](/images/superior-01.jpeg)

启动信息中

```py
/usr/local/Cellar/supervisor/3.3.4/libexec/bin/python
/usr/local/opt/supervisor/bin/supervisord -c /usr/local/etc/supervisord.ini --nodaemon
```

分别包含了Python的路径，supervisord的路径，第三个，-c设定config路径，第四是supervisor配置文件config路径

此时查看一下config配置文件的内容，可以通过

```py
cat /usr/local/etc/supervisord.ini
```

查看文件内容，看到最后一行

```
[include]
files = /usr/local/etc/supervisor.d/*.ini
```

实际上这个需要我们自己建立，先建立文件夹

```
mkdir /usr/local/etc/supervisor.d/
```

准备配置文件

```
 vim /usr/local/etc/supervisor.d/weather.ini
```

输入i进入编辑模式，添加如下内容

```
[program:weather]
directory = /Users/xueliyan/Desktop/PythonEngineering/day-08
command = /usr/local/bin/python3 main.py
autostart = true
startsecs = 5
autorestart = true
startretries = 3
user = xueliyan
redirect_stderr = true
stdout_logfile_backups = 20
stdout_logfile=/usr/local/var/log/weather.log
stdout_logfile_maxbytes=10MB
stderr_logfile=/usr/local/var/log/weather-err.log
stderr_logfile_maxbytes=10MB
```

其中

```
directory = /Users/xueliyan/Desktop/PythonEngineering/day-08   
```

为执行源代码路径，user改为自己账号

redirect_stderr为接收错误输出，分别用下面的文件接收。

编辑完整之后，按ESC，然后 `:wq`保存退出

最后我们执行

```
brew services restart supervisor
```

重启supervisor，就可以执行main.py文件了



