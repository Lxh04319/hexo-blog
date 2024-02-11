---
title: 关于Redis配置及使用的种种坑
date: 2024-2-11
tags:
  - java
description: 配置以及使用redis时踩过的种种坑🥺TAT
abbrlink: 10004
categories: 
  - Learn
  - Redis
---

### 写在前面

* 谨以此篇记录本🐭在学习redis时遇到的坑坑坑坑坑
* 想到哪就更哪 可能不够全面，有点回忆不起来了
* 如有错误欢迎指正

---

**redis建议安装linux版本(官方)，虽然微软弄出来了windows版本，不过据说在一些方面比如io多路复用等有差别，建议linux**

### Linux安装

1. **VMware虚拟机**
**官方正版**：不免费，仅30天试用期，曾经为了完成学校课程把30天免费全耗尽
**解决方法**：下载vmware player 官方个人免费版，据说更新到现在功能也还算是完善，支持建虚拟机，暂时不知道后续使用有无需求，也许以后还是回归正版

2. **Linux**
linux有很多版本 这里选了ubuntu 可视化
主要就是下载镜像文件(清华大学开源镜像站)，新建虚拟机 balabala 不多赘述，有很多教程
**注意**：安在一个空间较大的磁盘，别都挤在C盘里(没错，第一次安虚拟机不知道安了什么乱七八糟，最后C盘仅剩8G，这次长个教训😎)

3. **Ubuntu**
   1. ubuntu里采用的是apt-get安装，并非yum，如果很不幸的采用了yum安装，必然喜提报错``E:Unable to locate package yum``
   **解决方法**：不使用yum
   2. ubuntu的bug问题 输入密码的时候光标不动，也不显示输入的密码或者隐式密码
   **解决方法**：实际上已经输进去了，输完直接enter就好
   3. redis基于C语言编写，编译需要有gcc，正常已经安装(build-essential)，如果没有gcc还需自行安装
   4. 修改配置文件 由于没有vim 故采用vi命令
      保存文件并退出 ``:wq!`` 后来发现ubuntu里面有test.editor，直接用很方便
   5. 解压文件 tar -zxvf 这个出现问题的话可能是别的问题，比如压缩包，解压命令问题概率很小

### redis安装

据说用docker很方便，没试过

#### 下载

##### 方式一

官网 https://redis.io 下载安装包(建议稳定版)

**缺点**：直接下载到win系统，需要把压缩包上传到linux，很麻烦
**解决方法**：这里选取的是FileZilla工具，这之中需要连接虚拟机，可能会产生一些报错(忘记什么报错了，依稀记得是虚拟机连接不上)

##### 方式二

直接在linux里安装，很快很爽

命令:
```
wget http://download.redis.io/releases/redis-7.2.4.tar.gz
```

#### 解压及安装

解压：
```
tar -zxvf redis-7.2.4.tar.gz
```

安装：
先切换到刚刚安的redis-7.2.4目录下
```
make
make install
```
可能出现权限问题 前面加上sudo即可

#### 运行

##### 前台运行

``sudo redis-server``

* 报错一：运行后出现Failed listening on port 6379(TCP)
说明6379端口被占用问题
**解决方法**：查看redis端口情况``ps -ef | grep redis``
发现有两行，将第一行进程杀掉``sudo kill -9 (PID)``
* 报错二：kill后重新查看端口发现还是有两行
**解决方法**：百度了一下是redis保护问题，即使杀掉又回有一个新的出来，直接采取命令``sudo /etc/init.d/redis-server stop``，再查看发现杀掉了，再次启动成功
  
可能出现

##### 后台运行

修改redis.conf配置文件(修改最终想指定执行的那个文件，不要修改错文件了)

主要以下四条：
```
daemonize yes 允许后台运行
protected-mode no 关闭守护模式，允许除了本机以外的其他连接
# bind 127.0.0.1 注释掉
requirepass 666(密码设置)
```

* 报错一 ``set-pro-title yes``balabala(后面忘了)
**解决方法**：多半是redis配置问题，或者安装路径混了，找不到文件，为了省事---重新下载redis，一定一定注意下载、安装目录等
* 报错二 重安了一次，没有报错一了(果然遇到困难解决不了的方法就是重头再来！！！)，但这次有了新的报错``You need tcl 8.5 or ...``
**解决方法**：安！tcl 8.5+版本 不多赘述，直接在linux里下载即可
* 报错三 终于终于解决了报错一二，又出现了三🙄，果然人生充满起起落落，这次是``Memory overcommit must be enabled!``
**解决方法**：后面给出了解决方法,输入命令``sudo sysctl vm.overcommit_memory=1``
**注意**：这个是每次都要重新声明，很麻烦(唉...)
可以采取修改配置文件的方式，一劳永逸(自行百度，忘了)

报错解决后
``sudo redis-server /启动的配置文件路径(redis.conf)``
启动成功

#### redis连接

此时此刻终于可以启动redis了

##### redis-cli客户端连接

``redis-cli``进入交互控制台
输入ping 回应pong说明redis已启动
``redis-cli shutdown``
取消连接

常用指令
- ``-h 127.0.0.1``：指定要连接的redis节点的IP地址，默认是127.0.0.1
- ``-p 6379``：指定要连接的redis节点的端口，默认是6379
- ``-a 666``：指定redis的访问密码 

##### RESP.APP可视化redis

卡了我好久的麻烦来了

首先安装包
https://github.com/lework/RedisDesktopManager-Windows/releases

很好安 然后点击连接到redis服务器

**！！！报错：连接不上**

127.0.0.1连接不上
改成虚拟机ip也连接不上

查阅了各大佬的解决方案，操作如下

* 查看虚拟机ip ``ifconfig``
* win上cmd查看是否能与虚拟机联通  --发现能ping通
* 连接规则桥接模式  --应该跟这个没啥关系，寄
* 检查redis.conf配置文件  --没啥毛病
* 查看linux防火墙是否开放6379端口  --发现根本没有firewalld。。。遂安装
  * 开放6379端口 命令自行百度   --开放了但还是寄
  * 重载防火墙，查看开放的端口(后来又添加了3306端口)
* 检查windows防火墙入站规则
* 重新加载redis
* 命令 ``redis-cli -h 虚拟机ip -p 6379`` 如果出现 虚拟机ip:6379>即为连接成功

**总结** 
继防火墙端口开放，win防火墙新建入站规则、更改保存redis.conf后还是寄
最终猜测我的成功路径应该是先杀掉占用6379的进程(...stop)-->重新启动redis-server-->redis-cli -h ip -p 6379

**成功啦！**

### redis的java客户端

这里先采取jredis简单测试了一下

test成功

如果RESP连接不上，那jredis多半连接凉凉
本质应该是一样的，都是在win上连接linux当中运行的redis
