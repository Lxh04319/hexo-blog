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
* 想到哪就更哪

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

