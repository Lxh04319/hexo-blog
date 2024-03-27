---
title: 使用docker安装elasticsearch kibana ik分词器以及踩雷
date: 2024-3-20
tags:
  - java
description: 使用docker安装elasticsearch,以及kibana、ik中文分词器插件
abbrlink: 10007
categories: 
  - Learn
  - 微服务
  - Docker
  - elasticsearch
---

**写在前面**
docker是在win11子系统Linux下安装的(因为前段时间redis在Linux虚拟机里使用的，现在看着虚拟机就烦🙄)
由于是初学，并不是很了解其中的原理
各种问题的解决方式仅代表个人出错以及成功解决案例

docker安装省略,这里选择的是7.12.1版本(比较久远，可以去官网查找更新版本😎)

## elasticsearch安装

### 创建网络

后续要与kibana连接
``docker network create es-net``

### 拉取镜像

``docker pull elasticsearch:7.12.1``

### 创建容器、部署单点

```
docker run -d --name es --network es-net -p 9200:9200 -p 9300:9300 -v /usr/local/es/data:/usr/share/elasticsearch/data -v /usr/local/es/plugins:/usr/share/elasticsearch/plugins -e "discovery.type=single-node" -e "ES_JAVA_OPTS=-Xms512m -Xmx512m"
elasticsearch:7.12.1
```

### 重启es容器

``docker restart es``

### 测试

访问``http://localhost:9200``
正常会显示 如图所示
![alt text](https://pic.imgdb.cn/item/65fb0c309f345e8d03b6fcb3.png)

* **错误一：localhost拒绝访问**
  看有些解决方法说是修改elasticsearch.yml文件，因为开启了ssl认证
  在终端执行``curl localhost:9200``也可以查看返回信息，有显示上图即成功
* **错误二：localhost未发送任何数据**
  es启动有点慢，需要等一等，可能是还没加载出来

## kibana安装

### 拉取镜像

``docker pull kibana:7.12.1`` 
**注意** 版本要与elasticsearch一致

### 创建容器、部署kibana

```
docker run -d --name kibana --network es-net -p 5601:5601 -e ELASTICSEARCH_HOSTS=http://es:9200 
kibana:7.12.1
```

### 测试是否成功

访问``http://localhost:5601``
**注意** kibana与es连接，一定在es启动成功后再启动kibana，否则报错

**正常访问如图**
![alt text](https://pic.imgdb.cn/item/65fb934b9f345e8d0306743f.png)

* **错误：``kibana server is not ready yet``**
  查看日志
  ``docker logs -f kibana``查看报错信息
  多半是与es没连接成功
  这里有个坑！
  需要将创建容器时``es``改为电脑的私有ip

  ```
  docker run -d --name kibana --network es-net -p 5601:5601 -e ELASTICSEARCH_HOSTS=http://私有ip:9200 
  kibana:7.12.1
  ```

  并将配置文件kibana.yml中elasticsearch.url链接也要改为私有ip

  可以采用数据卷挂载、copy到本地等方式修改/config/kibana.yml

### 查找ip

* **方法一：cmd打开终端命令行**
  输入``ipconfig``
  ![alt text](https://pic.imgdb.cn/item/65fb98989f345e8d0318fe6e.png)
  es改为IPv4地址
* **方法二：搜索**
  控制面板->查看网络状态和任务
  ![alt text](https://pic.imgdb.cn/item/65fb99a99f345e8d031d4234.png)
  连接WIFI->详细信息->IPv4地址

## ik分词器安装

### 在线安装

#### 进入容器

``docker exec -it es bash``

#### 下载

``./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip``

* **错误：下载失败**
  ![alt text](https://pic.imgdb.cn/item/65fb9d6f9f345e8d032bdaf8.png)
  ``Exception in thread "main" java.net.ConnectException: Connection refused``
  没找到好的解决办法，遂手动下载安装

### 手动下载安装

**注意版本一致**

#### 下载ik分词器

打开官网``https://github.com/medcl/elasticsearch-analysis-ik``查找与elasticsearch一致的版本下载zip文件，并解压到一个不含中文和空格的目录下

之后重命名为``ik``

#### 将``ik``转移到docker容器里

``docker cp ik文件的路径 elasticsearch:/usr/share/elasticsearch/plugins``

#### 重启容器

``docker restart es``

**注意！** 尽量不采用此种方式(操控容器)

[ik分词器安装参考解决方案以及尽量不采用此种方式操控容器的解释](https://juejin.cn/post/6915355569679400974?searchId=20240320191417641463D0E23F12114A2A)

#### 测试

![alt text](https://pic.imgdb.cn/item/65fba22f9f345e8d033ec548.png)

**解决成功就可以愉快的在项目中使用elasticsearch结合数据库来进行搜索啦😉**