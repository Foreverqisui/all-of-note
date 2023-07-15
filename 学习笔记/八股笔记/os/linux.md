# Linux使用合集

## 启动命令

### docker

```java
systemctl start docker
```

### redis

```java
//初始化
docker run -itd --name redis-test -p 6379:6379 redis
```

```java
//进入redis
docker exec -it redis-test /bin/bash
```

### mysql

```java
docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

```java
docker run -it mysql /bin/bash
```

### rabbitmq

```java
docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5673:5672 rabbitmq
```

```java
//进入
docker exec -it 0a70354a0bda /bin/bash
```

```java
//运行
rabbitmq-plugins enable rabbitmq_management
```

```java
//默认账户
账户：guest
密码：guest
```

```java
//访问地址
http://192.168.16.131:15672/#/
```

### es

```java
sudo docker pull elasticsearch:7.12.0
```

```java
sudo mkdir -p /opt/elasticsearch/config
sudo mkdir -p /opt/elasticsearch/data
sudo mkdir -p /opt/elasticsearch/plugins
```

```java
echo "http.host: 0.0.0.0" >> /opt/elasticsearch/config/elasticsearch.yml
```

```java
sudo docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms84m -Xmx512m" \
-v /opt/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /opt/elasticsearch/data:/usr/share/elasticsearch/data \
-v /opt/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.12.0
```

```java
docker exec -it elasticsearch bash
```

> 由于不是所有用户都是可读写的，导致启动失败，需要切换到（cd /opt/elasticsearch）目录，将所有改成可读写

```java
chmod -R 777 /opt/elasticsearch/
```

### kibana

```java
docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.32.129:9200 -p 5601:5601 -d kibana:7.6.2
```

### 改变gcc版本

```java
yum -y install centos-release-scl
yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils

#临时修改gcc版本
scl enable devtoolset-9 bash
#永久修改gcc版本
echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
```

### 当docker无法运行容器时

```java
docker ps -aq | xargs -I {} docker start {}
```

```
sudo docker run --name elasticsearch -p 9200:9200  -p 9300:9300 \
 -e "discovery.type=single-node" \
 -e ES_JAVA_OPTS="-Xms84m -Xmx512m" \
 -v /opt/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
 -v /opt/elasticsearch/data:/usr/share/elasticsearch/data \
 -v /opt/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
 -d elasticsearch:7.12.0
```
