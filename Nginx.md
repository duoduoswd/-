# 1.简介

## 1.1 什么是Nginx

Nginx是高性能的HTTP和反向代理的服务器，处理高并发能力十分强大，能经受高负载的考验,有报告表明能支持高达 50,000个并发连接数。

## 1.2  正向代理

+ 需要在客户端配置代理服务器进行指定网站访问

  ![image-20221116204438834](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116204438834.png)

  ## 1.3 反向代理

+ 暴露的是代理服务器地址，隐藏了真实服务器IP地址。

![image-20221116204523426](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116204523426.png)

## 1.4 负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

![image-20221116204617450](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116204617450.png)

## 1.5 动静分离

![image-20221116204634608](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116204634608.png)

# 2.Nginx 的安装

## 2.1 准备工作

+ 打开虚拟机，使用远程连接工具连接linux操作系统

+ 在nginx官网下载软件
  http://nginx.org/

  ![image-20221116204800556](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116204800556.png)

## 2.2 开始进行nginx安装

+ 安装pcre依赖

  + 联网下载pcre压缩文件依赖
    ```sh
    wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
    ```

  ![image-20221116205239346](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116205239346.png)

  + 解压压缩文件

    ```sh
    tar –xvf pcre-8.37.tar.gz
    ```

  + 执行命令

    ```sh
    ./configure
    ```

  + 回到pcre目录下依次执行

    ```sh
    make
    make install
    ```

  ![image-20221116205310340](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116205310340.png)

+ 安装openssl 、zlib 、 gcc 依赖

  ```sh
  yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
  ```

+ 安装nginx

  - 使用命令解压

  - ```
    ./configure
    ```

  - ```sh
    make && make install
    ```

+ 进入目录 /usr/local/nginx/sbin 执行以下命令启动服务

  ```sh
  ./nginx
  ```

+ 访问虚拟机的ip地址

  ![image-20221116205556548](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116205556548.png)

+ 注意：在windows系统中访问linux中nginx，默认是不能访问的，因为有防火墙问题，两种解决方案：

  + 关闭防火墙(危险)

  + 开放访问的端口号， 80 端口

    + 查看开放的端口号

      ```sh
      firewall-cmd --list-all
      ```

    + 设置开放的端口号

      ```sh
      firewall-cmd --add-service=http –-permanent
      firewall-cmd --add-port=80/tcp --permanent
      ```

    + 重启防火墙

      ```sh
      firewall-cmd –-reload
      ```

      ![image-20221116210809181](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116210809181.png)

# 3.Nginx 的常用命令

+ 进入nginx目录中
  ```sh
  cd /usr/local/nginx/sbin
  ```

## 3.1 查看nginx版本号
```sh
./nginx -v
```

![image-20221116211028473](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211028473.png)

## 3.2 启动nginx

```sh
./nginx
```

## 3.3 停止nginx
```sh
./nginx -s stop
```

## 3.4 重新加载nginx
```sh
./nginx -s reload
```

# 4.Nginx 的配置文件

## 4.1 nginx配置文件位置

```sh
cd /usr/local/nginx/conf
```

![image-20221116211158793](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211158793.png)

## 4.2 配置文件中的内容

1. 全局块：配置服务器整体运行的配置指令

   比如`worker_processes 1;`是处理并发数的配置

2. events块：影响 Nginx 服务器与用户的网络连接
   比如`worker_connections 1024; `指支持的最大连接数为 1024

3. http块
   1. http全局块
   2. server块

# 5.Nginx 配置实例-反向代理实例 1

## 5.1 实现效果

打开浏览器，在浏览器地址栏输入地址 [http://www.123.com](http://www.123.com/)，跳转到liunx系统tomcat主页面中

## 5.2 准备工作

+ 在liunx系统安装tomcat，使用默认端口 8080

  - tomcat安装文件放到liunx系统中，解压

  - 进入tomcat的bin目录中，./startup.sh启动tomcat服务器

+ 对外开放访问的端口

  ```sh
  firewall-cmd --add-port=80 80 /tcp --permanent
  firewall-cmd –-reload
  ```

+ 查看已经开放的端口号

  ```sh
  firewall-cmd --list-all
  ```

+ 在windows系统中通过浏览器访问tomcat服务器

  ![image-20221116211713099](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211713099.png)

## 5.3 访问过程分析

![image-20221116211754967](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211754967.png)

## 5.4 具体配置

+ 在windows系统的host文件进行域名和ip对应关系的配置

  ![image-20221116211856865](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211856865.png)

  + 添加内容在host文件中

    ![image-20221116211946507](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116211946507.png)

+ 在nginx进行请求转发的配置（反向代理配置）

  ![image-20221116212012152](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212012152.png)

## 5.5 最终测试

![image-20221116212043824](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212043824.png)

# 6.Nginx 配置实例-反向代理实例 2

## 6.1 实现效果

使用nginx反向代理，根据访问的路径跳转到不同端口的服务中nginx监听端口为 9001 ,

访问 http://192.168.17.129:9001/edu/ 直接跳转到127.0.0.1:808 0
访问 [http://](http:) 192.168.17.129:9001/vod/ 直接跳转到127.0.0.1:808 1

## 6.2 准备工作

+ 准备两个tomcat服务器，一个 8080 端口，一个 8081 端口
+ 创建文件夹和测试页面

## 6.3 具体配置

+ 找到nginx配置文件，进行反向代理配置

  ![image-20221116212230161](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212230161.png)

+ 开放对外访问的端口号 9001 8080 8081

## 6.4 最终测试

![image-20221116212246038](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212246038.png)

# 7.Nginx 配置实例-负载均衡

## 7.1 实现效果

浏览器地址栏输入地址 http://192.168.17.129/edu/a.html，负载均衡效果，平均 8080和 8081 端口中

## 7.2 准备工作

+ 准备两台tomcat服务器，一台 8080 ，一台 8081
+ 在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建页面a.html，用于测试

+ 在nginx的配置文件中进行负载均衡的配置

  ![image-20221116212438280](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212438280.png)

  ![image-20221116212456875](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212456875.png)

+ nginx分配服务器策略

  + 轮询（默认）
    每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

  + weight
    weight代表权重默认为1,权重越高被分配的客户端越多

  + ip_hash
    每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器

  + fair（第三方）
    按后端服务器的响应时间来分配请求，响应时间短的优先分配。

# 8.Nginx 配置实例-动静分离

## 8.1 什么是动静分离

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一个请求，比对服务器该文件最新时间没有变化，则不会从服务器抓取，返回状态码 304 ，如果有修改，则直接从服务器重新下载，返回状态码 200 。

![image-20221116212620582](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212620582.png)

## 8.2 准备工作

在liunx系统中准备静态资源，用于进行访问。

![image-20221116212638166](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212638166.png)

## 8.3 具体配置

在nginx配置文件中进行配置

![image-20221116212657707](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212657707.png)

## 8.4 最终测试

+ 浏览器中输入地址

http://192.168.17.129/image/

![image-20221116212804011](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212804011.png)

+ 在浏览器地址栏输入地址

http://192.168.17.129/www/a.html

![image-20221116212813452](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212813452.png)

# 9.Nginx 配置高可用的集群

## 9.1 什么是nginx高可用

1. 需要两台nginx服务器

![image-20221116212857914](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116212857914.png)

2. 需要keepalived
3. 需要虚拟ip

## 9.2 配置高可用的准备工作

+ 需要两台服务器 192.168.17.129 和 192.168.17.

+ 在两台服务器安装nginx

## 9.3 在两台服务器安装keepalived

+ 使用yum命令进行安装
  ```sh
  yum install keepalived –y
  ```

+ 安装之后，在etc里面生成目录keepalived，有文件keepalived.conf

## 9.4 完成高可用配置（主从配置）

+ 修改/etc/keepalived/keepalivec.conf配置文件
  ```sh
  + global_defs {
    notification_email {
    acassen@firewall.loc
    failover@firewall.loc
    sysadmin@firewall.loc
    }
    notification_email_from Alexandre.Cassen@firewall.loc
    smtp_server 192.168.17.
    smtp_connect_timeout 30
    router_id LVS_DEVEL
    }
  
  vrrp_script chk_http_port {
  
  script "/usr/local/src/nginx_check.sh"
  
  interval 2 #（检测脚本执行的间隔）
  
  weight 2
  
  }
  
  vrrp_instance VI_1 {
  state BACKUP # 备份服务器上将 MASTER 改为 BACKUP
  interface ens33 //网卡
  virtual_router_id 51 # 主、备机的virtual_router_id必须相同
  priority 90 # 主、备机取不同的优先级，主机值较大，备份机值较小
  advert_int 1
  
  authentication {
  auth_type PASS
  auth_pass 1111
  }
  virtual_ipaddress {
  192.168.17.50 // VRRP H虚拟地址
  }
  }
  ```

+ 在/usr/local/src添加检测脚本
  ```sh
  \#!/bin/bash
  A=`ps -C nginx –no-header |wc -l`
  if [ $A -eq 0 ];then
  /usr/local/nginx/sbin/nginx
  sleep 2
  if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
  killall keepalived
  fi
  fi
  ```

+ 把两台服务器上nginx和keepalived启动
  启动nginx

  ```sh
  ./nginx
  ```

  启动keepalived

  ```sh
  + systemctl start keepalived.service
  ```

## 9.5 最终测试

+ 在浏览器地址栏输入 虚拟ip地址 192.168.17

  ![image-20221116213404490](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213404490.png)

  ![image-20221116213417063](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213417063.png)

+ 把主服务器（192.168.17.129）nginx和keepalived停止，再输入192.168.17

  ![image-20221116213437850](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213437850.png)

  ![image-20221116213444123](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213444123.png)

# 10.Nginx的原理

## 10.1 mater和worker

![image-20221116213517317](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213517317.png)

![image-20221116213531557](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213531557.png)

## 10.2 worker如何进行工作的

![image-20221116213618992](https://gitee.com/Y-aY-a/imgs/raw/master/nginx/assets/image-20221116213618992.png)

## 10.3 一个master和多个woker有好处

+ 可以使用nginx **–** s reload 热部署，利用nginx进行热部署操作
+ 每个woker是独立的进程，如果有其中的一个woker出现问题，其他woker独立的，继续进行争抢，实现请求过程，不会造成服务中断

## 10.4 设置多少个woker合适

worker数和服务器的cpu数相等是最为适宜的

## 10.5 连接数worker_connection

1. 发送请求，占用了woker的几个连接数？
   答案： 2 或者 4 个

2. nginx有一个master，有四个woker，每个woker支持最大的连接数 1024 ，支持的最大并发数是多少？
   + 普通的静态访问最大并发数是： worker_connections * worker_processes /2
   + 而如果是HTTP作 为反向代理来说，最大并发数量应该是worker_connections *worker_processes/4