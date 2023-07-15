# MSF使用命令

## 初始化数据库

```java
//开启数据库
service postgresql start
//初始化数据库--第一次使用需要
sudo msfdb init
//打开数据库
msfconsole
//连接网站
connect 123.56.156.8 80
//展示所有模块
show all
//查询mysql
search mysql
//装载一个渗透漏洞模块
use loit/windows/smb/ms08_067_netapi
```

## 查询网站信息

```java
whois foreverqisui.top
```

## DNS记录解析查询

```java
nslookup    
set qt =ns
foreverqisui.top
```

## nmap检测

> 可以检测开放端口

```java
service postgresql start
msfconsole
//使用脚本进行全面的漏洞扫描
db_nmap --script=vuln 123.56.156.8
```

## 代理监听

> 浏览器里配置代理--127.0.0.1（回环地址） port：8800

```java
mitmproxy -p 8800   
i：拦截请求路径【~u \.js】
q:退出
```

## 检测经过几个机器

```java
traceroute hostname
```

## scapy定制数据包

```java
//先提升权限
sudo su
//查看ARP数据包格式
ARP().display()
//拼接数据包
sr1(IP(dst="123.56.156.8")/ICMP(),timeout=1)
```
