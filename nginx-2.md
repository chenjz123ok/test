# centos7

 * ```
    yum info nginx  
    ```
  ```

Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile

 * base: mirrors.aliyun.com
 * centos-sclo-rh: mirrors.nju.edu.cn
 * centos-sclo-sclo: mirrors.nju.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
   Available Packages
   Name        : nginx
      Arch        : x86_64
      Epoch       : 1
      Version     : 1.20.1
      Release     : 2.el7
      Size        : 586 k
      Repo        : epel/x86_64
      Summary     : A high performance web server and reverse proxy server
      URL         : https://nginx.org
      License     : BSD
      Description : Nginx is a web server and a reverse proxy server for HTTP, SMTP, POP3 and
               : IMAP protocols, with a strong focus on high concurrency, performance and low
               : memory usage.
  ```


# centos8

```
dnf info nginx 
Last metadata expiration check: 0:00:26 ago on Wed 27 Oct 2021 01:01:31 PM CST.
Available Packages
Name         : nginx
Epoch        : 1
Version      : 1.14.1
Release      : 9.module_el8.0.0+184+e34fea82
Architecture : x86_64
Size         : 570 k
Source       : nginx-1.14.1-9.module_el8.0.0+184+e34fea82.src.rpm
Repository   : appstream
Summary      : A high performance web server and reverse proxy server
URL          : http://nginx.org/
License      : BSD
Description  : Nginx is a web server and a reverse proxy server for HTTP, SMTP,
             : POP3 and IMAP protocols, with a strong focus on high concurrency,
             : performance and low memory usage.
```





```
[root@gitlab ~]# cat /usr/lib/systemd/system/nginx.service
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/run/nginx.pid

# Nginx will fail to start if /run/nginx.pid already exists but has the wrong

# SELinux context. This might happen when running `nginx -t` from the cmdline.

# https://bugzilla.redhat.com/show_bug.cgi?id=1268621

ExecStartPre=/usr/bin/rm -f /run/nginx.pid  #启动前先删除pid
ExecStartPre=/usr/sbin/nginx -t  #检查语法
ExecStart=/usr/sbin/nginx  #启动nginx
ExecReload=/usr/sbin/nginx -s reload
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

设置开机启动

```
systemctl enable --now nginx
systemctl status nginx
```



# 查看进程列表

`pstree -p`




# centos8安装

https://nginx.org/en/linux_packages.html

```
vim /etc/yum.repos.d/nginx.repo
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```



```

# 查看版本信息

[root@centos8 ~]# dnf info nginx
nginx stable repo                                                           20 kB/s |  32 kB     00:01    
Available Packages
Name         : nginx
Epoch        : 1
Version      : 1.20.1
Release      : 1.el8.ngx
Architecture : x86_64
Size         : 819 k
Source       : nginx-1.20.1-1.el8.ngx.src.rpm
Repository   : nginx-stable
Summary      : High performance web server
URL          : https://nginx.org/
License      : 2-clause BSD-like license
Description  : nginx [engine x] is an HTTP and reverse proxy server, as well as
             : a mail proxy server.

# 列出所以版本

`[root@centos8 ~]# dnf list nginx --showduplicates`

安装

`[root@centos8 ~]# dnf -y install nginx`

```
[root@centos8 ~]# systemctl enable --now nginx
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.





[root@centos8 ~]# nginx -h
nginx version: nginx/1.20.1
Usage: nginx [-?hvVtTq] [-s signal] [-p prefix]
             [-e filename] [-c filename] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /etc/nginx/)    #指定nginx目录
  -e filename   : set error log file (default: /var/log/nginx/error.log)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)  #配置文件路径
  -g directives : set global directives out of configuration file    #设置全局指令，注意和配置文件不要同时配置，否则冲突




[root@centos8 ~]# nginx -g "worker_processes 6;"   

[root@centos8 ~]# pstree -p

前台运行

nginx -g "deamon off"

# 验证nginx

```
[root@centos8 ~]# nginx -V
nginx version: nginx/1.20.1
built by gcc 8.4.1 20200928 (Red Hat 8.4.1-1) (GCC) 
built with OpenSSL 1.1.1c FIPS  28 May 2019 (running with OpenSSL 1.1.1g FIPS  21 Apr 2020)
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -specs=/usr/lib/rpm/redhat/redhat-annobin-cc1 -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'


nginx配置文件

范例：查看配置文件列表

[root@centos8 ~]# rpm -qc nginx
/etc/logrotate.d/nginx
/etc/nginx/conf.d/default.conf
/etc/nginx/fastcgi_params
/etc/nginx/mime.types
/etc/nginx/nginx.conf
/etc/nginx/scgi_params
/etc/nginx/uwsgi_params
```




# nginx编译安装

```
下载
[root@centos8 ~]# wget https://nginx.org/download/nginx-1.20.1.tar.gz -P /usr/local/src/
解压
[root@centos8 src]# tar xvf nginx-1.20.1.tar.gz 
cd nginx-1.20.1/
创建nginx账号
[root@centos8 ~]# useradd -r -s /sbin/nologin nginx
安装依赖
[root@centos8 ~]# yum -y install gcc pcre-devel openssl-devel zlib-devel 

[root@centos8 nginx-1.20.1]# ./configure --prefix=/apps/nginx \

> --user=nginx \
> --group=nginx \
> --with-http_ssl_module \
> --with-http_v2_module \
> --with-http_realip_module \
> --with-http_stub_status_module \
> --with-http_gzip_static_module \
> --with-pcre \
> --with-stream \
> --with-stream_ssl_module \
> --with-stream_realip_module

[root@centos8 nginx-1.20.1]# make -j 2 && make install
[root@centos8 ~]# tree /apps/nginx/
#修改权限
[root@centos8 nginx-1.20.1]# chown -R nginx.nginx /apps/nginx
```




# 验证版本及编译参数

```
[root@centos8 ~]# ls /apps/nginx/sbin/
nginx
[root@centos8 ~]# ln -s /apps/nginx/sbin/nginx /usr/sbin/

#查看版本
[root@centos8 nginx-1.20.1]# nginx -v
nginx version: nginx/1.20.1

查看编译参数
[root@centos8 nginx-1.20.1]# nginx -V  #以后编译安装查看一下
nginx version: nginx/1.20.1
built by gcc 8.4.1 20200928 (Red Hat 8.4.1-1) (GCC) 
built with OpenSSL 1.1.1g FIPS  21 Apr 2020
TLS SNI support enabled
configure arguments: --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module
```




# 启动和停止nginx测试访问web界面

```
#启动nginx
[root@centos8 ~]# nginx 
```


 ![image-20211028230158400](https://typora01c.oss-cn-guangzhou.aliyuncs.com/image-20211028230158400.png)

# 创建nginx自启动文件

```
#复制同一版本的nginx的yum安装生成的service文件
[root@centos8 ~]# vim /usr/lib/systemd/system/nginx.service
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forkingE
PIDFile=/apps/nginx/run/nginx.pid  #pid路径要修改

# Nginx will fail to start if /run/nginx.pid already exists but has the wrong

# SELinux context. This might happen when running `nginx -t` from the cmdline.

# https://bugzilla.redhat.com/show_bug.cgi?id=1268621

ExecStartPre=/usr/bin/rm -f /run/nginx.pid
ExecStartPre=/apps/nginx/sbin/nginx -t   #语法路径
ExecStart=/usr/sbin/nginx -c /apps/nginx/conf/nginx.conf  #配置文件路径
ExecReload=/apps/nginx/sbin/nginx -s reload  #重启路径
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]
WantedBy=multi-user.target

#创建目录
[root@centos8 system]# mkdir /apps/nginx/run/

#修改配置文件
[root@centos8 ~]# vim /apps/nginx/conf/nginx.conf
pid     /apps/nginx/run/nginx.pid;
#关闭selinux
[root@centos8 ~]# setenforce 0
```

# 验证nginx自启动文件

```
[root@centos8 ~]# systemctl daemon-reload 
[root@centos8 ~]# systemctl enable --now nginx
[root@centos8 ~]# ss -tunl
#生成了pid文件
[root@centos8 ~]# ll /apps/nginx/run/nginx.pid 
-rw-r--r--. 1 root root 6 Oct 28 23:26 /apps/nginx/run/nginx.pid
#进程编号 20187
[root@centos8 ~]# cat /apps/nginx/run/nginx.pid 
20187
[root@centos8 ~]# ps -ef | grep nginx
root       20187       1  0 23:26 ?        00:00:00 nginx: master process /usr/sbin/nginx -c /apps/nginx/conf/nginx.conf
nginx      20188   20187  0 23:26 ?        00:00:00 nginx: worker process
root       20346   10027  0 23:29 pts/3    00:00:00 grep --color=auto nginx
[root@centos8 ~]# pstree -p | grep nginx
           |-nginx(20187)---nginx(20188)
```



tengine帮助文档

`http://tengine.taobao.org/nginx_docs/cn/`

Nginx的配置文件组成部分

- 主配置文件：nginx.conf
- 子配置文件：include.conf.d/*.conf
- fastcgi，uwsgi，s

```
#和邮件相关的配置

#mail {

​				...

​			}

#tcp代理配置，1.9版本以上支持

#stream {

​						.  .  .

​				}

#导入其他路径的配置文件

[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf

 include /apps/nginx/conf.d/*.conf;

[root@centos8 ~]#mkdir /apps/nginx/conf.d/
```

# 全局配置

main全局配置段常见的配置指令分类

- 正常运行必备的配置

- 优化性能相关的配置
- 用于调试及定位问题相关的配置
- 事件驱动相关的配置

全局配置说明：

```
user  nginx nginx; # 启动nginx工作进程的用户和组
worker_processes  1;  # [number | auto]启动nginx工作进程的数量，一般设为cpu核心数相同
worker_cpu_affinity 00000001 00000010 00000100 00001000;  #将nginx工作进程绑定到指定的cpu核心，默认nginx是不进行进程绑定的，绑定并不是意味着当前nginx进程独占以一核心cpu，但是可以保证此进程不会运行在其他核心上，这就极大减少了nginx的工作进程在不同的cpu核心上的来回跳转，减少了cpu对进程的资源分配与回收以及内存管理等，因此可以有效的提升nginx服务器的性能。
CPU MASK：00000001：0号cpu
          00000010: 1号cpu
          10000000: 7号cpu

#示例
worker_cpu_affinity 0001 0010 0100 1000;第0号---第3号cpu
worker_cpu_affinity 0101 1010;

#示例
worker_processes  4;
worker_cpu_affinity 00000010 00001000 00100000 10000000;

#查看绑定的cpu
[root@centos8 ~]#watch -n.5 'ps axo pid,cmd,psr | grep nginx'
Every 0.5s: ps axo pid,cmd,psr | grep nginx                               centos8: Sat Oct 30 14:12:07 2021

   1478 nginx: master process /usr/   6
   1481 nginx: worker process         5
   1482 nginx: worker process         2
   1483 nginx: worker process         4
   1484 nginx: worker process         3
   1486 nginx: worker process         7
   1488 nginx: worker process         3
   1490 nginx: worker process         6
   1491 nginx: worker process         0
   2828 grep nginx                    6

#安装ab工具
yum -y install httpd-tools
#压力测试  -c10表示并发用户数为10,-n100表示请求总数为100
[root@centos8 ~]#ab -c 1000 -n 2000 http://192.168.14.8/
#死循环
[root@centos8 ~]#while true ;do ab -c 1000 -n 2000 http://192.168.14.8/ ;done
#消耗cpu
[root@centos8 ~]#dd if=/dev/zero of=/dev/null

#cpu发生随机变化
Every 0.5s: p...  centos8: Sat Oct 30 14:22:46 2021

   1478 nginx: master process /usr/   6
   1481 nginx: worker process         5
   1482 nginx: worker process         1
   1483 nginx: worker process         1
   1484 nginx: worker process         5
   1486 nginx: worker process         7
   1488 nginx: worker process         2
   1490 nginx: worker process         2
   1491 nginx: worker process         5
   8405 grep nginx                    7


#绑定cpu测试
[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf
worker_processes  4;
worker_cpu_affinity 00000010 00001000 00100000 10000000;

在查看，已经绑定cpu，不会切换
Every 0.5s: p...  centos8: Sat Oct 30 14:28:21 2021

  11091 nginx: master process /usr/   6
  11092 nginx: worker process         1
  11093 nginx: worker process         3
  11094 nginx: worker process         5
  11095 nginx: worker process         7
  11390 grep nginx                    2

```

# 工作进程优先级，-20~19

```
[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf
worker_priority 3; #nice值
[root@centos8 ~]#watch -n.5 'ps axo pid,cmd,psr,ni | grep nginx'  #验证进程优先级

Every 0.5s: ps axo pid,cmd,psr,ni | grep nginx                            centos8: Wed Nov  3 17:13:49 2021

   2928 nginx: master process /usr/   2   0
   2929 nginx: worker process         1   3
   2930 nginx: worker process         3   3
   2931 nginx: worker process         5   3
   2932 nginx: worker process         7   3
   3501 grep nginx                    0   0

worker_rlimit_nofile 65536; #所有worker进程能打开的数量上限，包括nginx的所有连接（例如与代理服务器的连接等），而不仅仅是与客户端的连接，另一个考虑因素是实际的并发连接数不能超过系统级别的最大打开文件数的限制，最好与ulimit -n或者limits.conf的值保持一致

[root@centos8 ~]#ulimit -n 102400
[root@centos8 ~]#ulimit -a
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 14602
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 102400
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 14602
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

#永久生效,修改pam限制
[root@centos8 ~]#vim /etc/security/limits.conf #最后加入
*               soft    nofile                  1000000
*               hard    nofile                  1000000
#退出重新开或者重启
[root@centos8 ~]#ulimit -a
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 14602
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1000000
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 14602
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited


worker_rlimit_nofile 65536;
daemon off; #前台运行用于调试


events {
    worker_connections  65536;  #设置单个工作进程的最大并发连接数
    use epoll; #使用epoll事件驱动，Nginx支持众多的事件驱动，比如：select、poll、epoll，只能设置在events模块
中设置。
    accept_mutex on; #on为同一时刻一个请求轮流由work进程处理，而防止被同时唤醒所有worker，避免多个睡眠进程>被唤醒的设置，默认为off，新请求会唤醒所有worker进程，此过程也称为“惊群”，因此nginx刚安装完以后要进行适当的>优化，建议设置为on
    multi_accept on;  #on时Nginx服务器的每一个工作进程可以同时接受多个新的网络连接，此指令默认为off，即默认
为一个工作进程只能一次接受一个新的网络连接，打开后几个同时接受多个。建议设置为on
}

```

范例：实现nginx的高并发配置

```
[root@centos8 ~]#ulimit -n 102400
[root@centos8 ~]#while true;do ab -c 5000 -n 10000 http://192.168.14.8/;sleep 0.5;done

#默认配置不支持高并发，会出现一下错误日志
[root@centos8 ~]#tail /apps/nginx/logs/error.log 

#修改配置
[root@centos8 ~]#vim /etc/security/limits.conf 
*       -       nproc   100000

[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf
worker_rlimit_nofile 100000;

[root@os7 ~]#ulimit -n 1024
[root@os7 ~]#ab -n 5000 -c 3000 http://192.168.14.8/
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.14.8 (be patient)
socket: Too many open files (24)
[root@os7 ~]#ulimit -n 1000000
[root@os7 ~]#ab -n 5000 -c 3000 http://192.168.14.8/
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 192.168.14.8 (be patient)
Completed 500 requests
Completed 1000 requests
Completed 1500 requests
Completed 2000 requests

```

# http协议配置说明

http{

​	

}





范例：识别php文件为text/html

```
[root@centos8 ~]#cat /apps/nginx/html/test.php 
<?php
phpinfo();
?>

[root@os7 ~]#curl http://192.168.14.8/test.php -I
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Mon, 08 Nov 2021 08:10:26 GMT
Content-Type: application/octet-stream
Content-Length: 20
Last-Modified: Mon, 08 Nov 2021 08:00:15 GMT
Connection: keep-alive
ETag: "6188d90f-14"
Accept-Ranges: bytes

[root@centos8 ~]#cat /apps/nginx/conf/mime.types | grep txt
    text/plain                                       txt;
    
[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf
http {
    include       mime.types;    
    default_type          text/plain;
......
[root@centos8 ~]#nginx -s reload



[root@os7 ~]#curl http://192.168.14.8/test.php -I
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Mon, 08 Nov 2021 08:20:03 GMT
Content-Type: text/plain
Content-Length: 20
Last-Modified: Mon, 08 Nov 2021 08:00:15 GMT
Connection: keep-alive
ETag: "6188d90f-14"
Accept-Ranges: bytes

```

# 指定响应报文server首部

```
#是否在响应报文中的Content-Type显示指定的字符集，默认off不显示
charset charset | off;

#示例
charset utf-8;

[root@os7 ~]#curl 192.168.14.8 -I
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Mon, 08 Nov 2021 08:30:08 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 612
Last-Modified: Thu, 28 Oct 2021 13:32:37 GMT
Connection: keep-alive
ETag: "617aa675-264"
Accept-Ranges: bytes

#是否在响应报文的server首部显示nginx版本
server_tokens on | off | build | string;

[root@os7 ~]#curl 192.168.14.8 -I
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 08 Nov 2021 08:38:03 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 612
Last-Modified: Thu, 28 Oct 2021 13:32:37 GMT
Connection: keep-alive
ETag: "617aa675-264"
Accept-Ranges: bytes

```



 ![image-20211108163715035](https://typora01c.oss-cn-guangzhou.aliyuncs.com/image-20211108163715035.png)

范例：修改server字段

```
如果想自定义响应报文的nginx版本信息，需要修改源码文件，重新编译
如果server_tokens on，修改src/core/nginx.h 修改第13-14行，如下示例
#define NGINX_VERSION      "1.20.1"
#define NGINX_VER          "nginx/" NGINX_VERSION
修改为#define NGINX_VERSION      "1.60.100"
#define NGINX_VER          "chennginx/" NGINX_VERSION



如果server_tokens off，修改 src/http/ngx_http_header_filter_module.c 第49行，如下示例：
static u_char ngx_http_server_string[] = "Server: nginx" CRLF;
把其中的nginx改为自己想要的文字即可，如:static u_char ngx_http_server_string[] = "Server: chenjunzhinginx" CRLF;

[root@centos8 nginx-1.20.1]#./configure --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module

[root@centos8 nginx-1.20.1]#cp /apps/nginx/sbin/nginx /root/

[root@centos8 nginx-1.20.1]#make && make install 

[root@centos8 nginx-1.20.1]#ll /apps/nginx/sbin/nginx /root/nginx 
-rwxr-xr-x 1 root root 7728008 Nov  8 17:22 /apps/nginx/sbin/nginx
-rwxr-xr-x 1 root root 7727968 Nov  8 17:20 /root/nginx

#版本发生了变化
[root@os7 ~]#curl 192.168.14.8 -I
HTTP/1.1 200 OK
Server: chenjunzhinginx
Date: Mon, 08 Nov 2021 09:24:17 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 612
Last-Modified: Thu, 28 Oct 2021 13:32:37 GMT
Connection: keep-alive
ETag: "617aa675-264"
Accept-Ranges: bytes

#修改/apps/nginx/conf/nginx.conf

server_tokens on; #查看版本号

[root@os7 ~]#curl 192.168.14.8 -I
HTTP/1.1 200 OK
Server: chennginx/1.60.100
Date: Mon, 08 Nov 2021 09:27:32 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 612
Last-Modified: Thu, 28 Oct 2021 13:32:37 GMT
Connection: keep-alive
ETag: "617aa675-264"
Accept-Ranges: bytes

```

# 核心配置示例

基于不同的IP、不同的端口以及不同的域名实现不同的虚拟主机，依赖核心模块ngx_http_core_module实现。

# 新建一个pc web站点

```
#定义本置文件路径
[root@centos8 ~]#mkdir /apps/nginx/conf/conf.d
[root@centos8 ~]#vim /apps/nginx/conf/nginx.conf


[root@centos8 ~]#mkdir -p /data/nginx/html/{mobile,pc}
[root@centos8 ~]#tree /data/nginx/
/data/nginx/
└── html
    ├── mobile
    └── pc
[root@centos8 ~]#echo www.cjz.org > /data/nginx/html/pc/index.html
[root@centos8 ~]#echo m.cjz.org > /data/nginx/html/mobile/index.html

[root@centos8 ~]#cat /apps/nginx/conf.d/www.conf
server {
    listen 80;
    server_name www.cjz.org;
    root /data/nginx/html/pc;
}
[root@centos8 ~]#cat /apps/nginx/conf.d/mobile.conf 
server {
    listen 80;
    server_name m.cjz.org;
    root /data/nginx/html/mobile;
}

[root@os7 ~]#cat /etc/hosts
192.168.14.8 www.cjz.org m.cjz.org
[root@os7 ~]#curl www.cjz.org
www.cjz.org
[root@os7 ~]#curl m.cjz.org
m.cjz.org

```

# root与alias

root：指定web的家目录，在定义location的时候，文件的绝对路径等于root+location

```
[root@centos8 ~]#cat /apps/nginx/conf.d/www.conf 
server {
    listen 80;
    server_name www.cjz.org;
    #root /data/nginx/html/pc;
    location / {
	root /data/nginx/html/pc;
	}
}
[root@os7 ~]#curl www.cjz.org
www.cjz.org


[root@centos8 ~]#cat /apps/nginx/conf.d/www.conf 
server {
    listen 80;
    server_name www.cjz.org;
    #root /data/nginx/html/pc;
    location / {
	root /data/nginx/html/pc;
	}
    location /about {
        root /opt/nginx/html;
    }
}
[root@centos8 ~]#mkdir /opt/nginx/html -p
[root@centos8 ~]#echo /opt/nginx/html/index.html > /opt/nginx/html/index.html
[root@centos8 ~]#cat /opt/nginx/html/index.html 
/opt/nginx/html/index.html

[root@centos8 ~]#mkdir /opt/nginx/html/about
[root@centos8 ~]#echo /opt/nginx/html/about/index.html > /opt/nginx/html/about/index.html
[root@os7 ~]#curl www.cjz.org/about/
/opt/nginx/html/about/index.html

```

alias:定义路径别名，会把访问的路径重新定义到其指定的路径，文档映射的另一种机制；仅能用于location上下文，此指令使用较少

范例：

```
[root@centos8 ~]#cat /apps/nginx/conf.d/www.conf 
server {
    listen 80;
    server_name www.cjz.org;
    #root /data/nginx/html/pc;
    location / {
	root /data/nginx/html/pc;
	}
    location /about {
        alias /opt/nginx/html;
    }
}

[root@os7 ~]#curl www.cjz.org/about/
/opt/nginx/html/index.html
[root@os7 ~]#curl www.cjz.org/about
<html>
<head><title>301 Moved Permanently</title></head>
<body>   #301重定向
<center><h1>301 Moved Permanently</h1></center>
<hr><center>chennginx/1.60.100</center>
</body>
</html>
[root@os7 ~]#curl -L  www.cjz.org/about #-L跟踪连接
/opt/nginx/html/index.html
[root@os7 ~]#curl www.cjz.org/about -I
HTTP/1.1 301 Moved Permanently
Server: chennginx/1.60.100
Date: Thu, 11 Nov 2021 02:53:38 GMT
Content-Type: text/html
Content-Length: 175
Location: http://www.cjz.org/about/
Connection: keep-alive

```

# location的详细使用

```
#语法规则：
	location [ = | ~ | ~* | ^~ ] uri { ... }
	
=    #用于标准uri,需要请求字串与uri精确匹配，大小敏感，如果匹配成功就停止向下匹配并立即处理请求
^~   #用于标准uri前，表示包含正则表达式，并且匹配以指定的正则表达式开头，对uri的最左边部分做匹配检查，不区分字符大小写
~    #用于标准uri前，表示包含正则表达式，并且区分大小写
~*   #用于标准uri前，表示包含正则表达式，并且不区分大小写
不带符合    #匹配起始于uri的所有的uri
\    #用于标准uri前，表示包含正则表达式并且转义字符。可以将.*?等转义为普通符号
#匹配优先级从高到低：
=,^~,~/~*,不带符号
```

官方范例

```
location = / {
    [ configuration A ]
}

location / {
    [ configuration B ]
}

location /documents/ {
    [ configuration C ]
}

location ^~ /images/ {
    [ configuration D ]
}

location ~* \.(gif|jpg|jpeg)$ {
    [ configuration E ]
}
The “/” request will match configuration A, the “/index.html” request will match configuration B, the “/documents/document.html” request will match configuration C, the “/images/1.gif” request will match configuration D, and the “/documents/1.jpg” request will match configuration E.
```

