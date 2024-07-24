# squid

## squid是什么
根据[wikipedia](https://zh.wikipedia.org/wiki/Squid_(%E8%BD%AF%E4%BB%B6))的解释：
Squid Cache（简称为Squid）是HTTP代理服务器软件。Squid用途广泛，可以作为缓存服务器，可以过滤流量帮助网络安全，也可以作为代理服务器链中的一环，向上级代理转发数据或直接连接互联网。Squid程序在Unix一类系统运行。由于它是开源软件，有网站修改Squid的源代码，编译为原生Windows版；用户也可在Windows里安装Cygwin，然后在Cygwin里编译Squid。

Squid历史悠久，功能完善。除了HTTP外，对FTP与HTTPS的支持也相当好，在3.0测试版中也支持了IPv6。但是Squid的上级代理不能使用SOCKS协议。

## 为什么使用squid
squid是开源软件，目前仍在开发中，并且功能完善

## 如何搭建squid
虽然squid支持HTTP,FTP和HTTPS，但是建议使用安全的HTTPS：
* 不安全的代理，可能被其它人窃听；
* 不安全的代理，可能被其它人破坏：例如中间的网络设备根据包内容判断是否插入TCP的reset请求，从而导致某些网站不能通过代理访问；

大部分Linux发行版都带squid，但自带的squid可能不支持HTTPS。碰到这种情况，最好从源代码安装。下面以Debian为例，说明如何搭建支持HTTPS的代理。

### 安装HTTPS证书
网上有很多免费的签名证书，我这里使用let's encrypt，[获取签名证书的流程](https://certbot.eff.org/instructions?ws=other&os=debianstretch)

### 安装squid
* 如果系统自带ssl版本的squid（例如debian 12就有squid-openssl），则建议使用系统自带的版本，否则需要自行安装（编译耗费时间）。
* 如果没有ssl版本的squid，则需要自行安装，以squid版本3.5.38为例，可以从squid官网找到：[squid的下载地址](http://www.squid-cache.org/Versions/)。[squid安装过程](https://wiki.squid-cache.org/SquidFaq/CompilingSquid)重点注意：
  * 执行configure时，必须添加参数“--with-openssl”，确保支持HTTPS；
  * 手册中是否有相关系统的额外安装说明。例如对于Debian系统，手册详细介绍config的参数；
  * configure过程中，如果缺少其它依赖包，直接通过系统自带的包工具安装；
  * 我安装时的configure命令如下：
```
./configure --prefix=/usr --localstatedir=/var --libexecdir=${prefix}/lib/squid --datadir=${prefix}/share/squid --sysconfdir=/etc/squid --with-default-user=proxy --with-logdir=/var/log/squid --with-pidfile=/var/run/squid.pid --with-openssl
```

### 添加账号
使用如下命令，添加账号：
```
sudo htpasswd -c /etc/squid/passwords <username_you_like>
```

### 配置squid
安装完成后，将原始配置文件/etc/squid/squid.conf存档，然后修改成类似下面的配置文件：
* 将<cert file>替换为HTTPS证书中的证书文件；
* 将<key file>替换为HTTPS证书中的KEY文件；

我的squid配置文件：
```
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwords
acl authenticated proxy_auth REQUIRED

http_access allow authenticated
http_access deny all

http_port 0.0.0.0:3128

# add cert/key with letsencrypt.org
https_port 0.0.0.0:443 cert=<cert file> key=<key file>


coredump_dir /var/spool/squid

refresh_pattern ^ftp:		1440	20%	10080
refresh_pattern ^gopher:	1440	0%	1440
refresh_pattern -i (/cgi-bin/|\?) 0	0%	0
refresh_pattern .		0	20%	4320
```

### 测试squid
* 登录网页，确认证书正常：https://<proxy url>
* 使用如下命令，测试squid代理是否正常：
```
curl --proxy-insecure -x https://<proxy url> --proxy-user <username>:<password> https://www.google.com
# <proxy url>: HTTPS证书域名；
# <username>：用户名；
# <password>：用户密码；
```
  
### 配置客户端
客户端的代理配置如下：
* 协议：HTTPS（注意不是HTTP，后面有一个S）;
* 服务器：安装HTTPS证书中填写的域名；
* 端口：443；
* 用户名/密码：添加账号时使用的用户名/密码；
享受代理上网:)

### 更新HTTPS证书后Squid自动加载新证书
Certbot自动更新证书后，squid需要加载新证书：
* 创建文件/etc/letsencrypt/renewal-hooks/deploy/001-reload-squid.sh，并且填充如下内容：
```
#!/bin/bash
echo "cert deployed, reload squid" && systemctl reload squid
```
* 给文件/etc/letsencrypt/renewal-hooks/deploy/001-reload-squid.sh可执行权限:
```
sudo chmod a+x /etc/letsencrypt/renewal-hooks/deploy/001-reload-squid.sh
```
* 手动运行Certbot更新证书，并登录https://<proxy url>，以验证配置是否正确：
```
sudo certbot renew --force-renewal
```
* 只是测试脚本是否运行，可以执行如下代码
```
sudo certbot renew --dry-run --run-deploy-hooks
```
