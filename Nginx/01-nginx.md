### Nginx：https://nginx.org/en/docs/

+++

1. 基本概念

   1. nginx是什么，做什么事情

      ```
      nginx是一个高性能的http和反向代理服务器，特点是占有内存少，并发能力强，专为性能优化而开发。
      ```

      

   2. 反向代理

      1. 正向代理：https://baike.baidu.com/item/%E6%AD%A3%E5%90%91%E4%BB%A3%E7%90%86
      2. 反向代理：https://baike.baidu.com/item/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86

   3. 负载均衡

      1. 负载均衡：https://baike.baidu.com/item/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1
      2. 将请求通过代理服务器（nginx）平均分担到集群中的不同服务器中

   4. 动静分离

      1. 动静分离：https://baike.baidu.com/item/%E5%8A%A8%E9%9D%99%E5%88%86%E7%A6%BB
      2. 为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度，降低原来单个服务器的压力。

   

2. nginx安装、常用命令和配置文件

   1. 在linux系统中安装nginx：https://nginx.org/en/docs/install.html

      ```shell
      #yum安装，并enable external module
      #安装完nginx后，通过nginx -v查看nginx版本，下载相对应的二进制文件源码。
      #克隆想要的第三方module
      #将下载的nginx源码解压缩，就进入到目录中，通过./configure命令重新将第三方module生成进Makefile
      #make && make install 编译并安装
      #通过nginx -V查看第三方module是否已经添加上
      
      sudo yum install yum-utils
      
      #EOF只是标识符，可以换成别的；>代表添加，>>代表追加。
      cat << EOF > /etc/yum.repos.d/nginx.repo
      [nginx-stable]
      name=nginx stable repo
      baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
      gpgcheck=1
      enabled=1
      gpgkey=https://nginx.org/keys/nginx_signing.key
      module_hotfixes=true
      EOF
      
      sudo yum install nginx
      
      nginx -v
      #nginx version: nginx/1.20.1
      
      wget https://nginx.org/download/nginx-1.20.1.tar.gz
      git clone https://github.com/kvspb/nginx-auth-ldap.git
      
      tar -xvzf nginx-1.20.1.tar.gz
      cd nginx-1.20.1
      
      #查看已安装后的configure arguments
      nginx -V
      ./configure --prefix=/etc/nginx ...... --add-module=../nginx-auth-ldap
      make && make install
      nginx -V
      
      #源码安装
      #1.下载nginx源代码并解压
      wget https://nginx.org/download/nginx-1.20.1.tar.gz
      tar -xvzf nginx-1.20.1.tar.gz
      
      #2.为nginx设置安装目录和启用的模块
      #不加参数则安装在/usr/local下
      ./configure
      
      相关参数说明：
      		--prefix 用于指定nginx编译后的安装
          --add-module 为添加的第三方模块，此次添加了fdfs的nginx模块
          --with..._module 表示启用的nginx模块，如此处启用了http_ssl_module模块
      	错误解决：可能缺少必要的依赖包，yum install pcre-devel -y
      	
      #3.编译
      #执行make 进行编译，如果编译成功的话会在第一步中objs中出现一个nginx文件
      make
      
      #4.安装
      make install
      
      #5.启动
      cd /usr/local/nginx/sbin
      ./nginx
      
      #防火墙设置
      firewall-cmd --list-all
      
      #firewall-cmd --add-port=http --permanent
      firewall-cmd --add-port=80/tcp --permanent
      #firewall-cmd --remove-port=80/tcp --permanent
      firewall-cmd --reload
      #https://www.cnblogs.com/lixipeng/p/11141201.html
      
      #可以创建nginx软链接
      ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
      nginx start
      
      #	注意：
      		(如需开机自启，可在/etc/rc.d/rc.local 文件中添此命令)
      		如出现：nginx: [error] invalid PID number "" in "/usr/local/nginx/logs/nginx.pid"
      		则需通过nginx –c ../conf/nginx.conf    命令指定nginx的配置
      ```

      

   2. nginx常用命令

      ```shell
      #进入nginx的目录，/usr/local/nginx/sbin
      #1.查看nginx的版本号
      ./nginx -v
      
      #2.启动nginx
      ./nginx
      
      #3.关闭nginx
      ./nginx -s stop
      
      #4.重新加载nginx
      ./nginx -s reload
      ```

      

   3. nginx配置文件

      ```shell
      #1.配置文件位置：/usr/local/nginx/conf/nginx.conf
      #2.配置文件组成：三部分
      #第一部分 全局块
      	从配置文件开始到events块之间的内容，主要会设置一些影响nginx服务器整体运行的配置指令。
      	主要包括配置运行Nginx服务器的用户（组）、允许生产的worker process数，进程PID存放路径、日志存放路径和类型以及配置文件的引入等。
      	
      #例如
      worker_processes 1; #worker值越大，可以支持的并发处理量也越大。
      
      #第二部分 events块
      	events块涉及的指令主要影响Nginx服务器与用户的网络连接，常用的设置包括是否开启对多work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个word process可以同时支持的最大连接数。
      	
      #例如
      worker_connections 1024; #支持的最大连接数
      
      #第三部分 http块
      	nginx服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。
      	需要注意的是：http块也可以包括http全局块、server块。
      	
      #1.http全局块
      	http全局块配置的指令包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单链接请求数上限等。
      
      #2.server块
      	和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。
      	每个http块可以包含多个server块，而每个server块就相当于一个虚拟主机。
      	而每个server块也分为全局server块，以及可以同时包含多个location块。
      	
      	#1.全局server块
      		最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或IP设置。
      	
      	#2.location块
      		一个server块可以配置多个location块。
      		
      		这块的主要作用是基于Nginx服务器接收到的请求字符串（例如server_name/url-string），对虚拟主机名称（也可以是IP别名）之外的字符串（例如前面的/url-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。
      		
      		location指令说明
      		location [= | ~ | ~* | ^~] uri {
      		
      		}
      		#1、=：用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
      		#2、~：用于表示uri包含正则表达式，并且区分大小写。
      		#3、~*：用于表示uri包含正则表达式，并且不区分大小写。
      		#4、^~：用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字符串匹配度最高的location后，立即使用此location处理请求，而不使用location块中的正则uri和请求字符串做匹配。
      		#注意：如果uri包含正则表达式，则必须要有~或者~*标识。
      ```

      

3. nginx配置实例1-反向代理

   ```shell
   #当访问服务器本机的80端口，就会将请求转发到本机的8080端口
   server {
           listen       80;
           server_name  localhost;
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
           location / {
               root   html;
               index  index.html index.htm;
               proxy_pass http://127.0.0.1:8080;
           }
   }
   ```

   ```shell
   #当访问http://127.0.0.1:9001/edu/ 直接跳转到127.0.0.1:8080
   #当访问http://127.0.0.1:9001/vod/ 直接跳转到127.0.0.1:8081
   server {
           listen       9001;
           server_name  localhost;
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
           location ~ /edu/ {
               proxy_pass http://127.0.0.1:8080;
           }
           
           location ~ /vod/ {
               proxy_pass http://127.0.0.1:8080;
           }
   }
   
   #对外开放访问的端口号 9001 8080 8081
   firewall-cmd --list-all
   firewall-cmd --add-port=http --permanent
   firewall-cmd --add-port=9001/tcp --permanent
   firewall-cmd --add-port=8080/tcp --permanent
   firewall-cmd --add-port=8081/tcp --permanent
   firewall-cmd --reload
   ```

   

4. nginx配置实例2-负载均衡

   1. 负载均衡策略
      1. 轮询（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
      2. weight：权重，默认为1。权重越高被分配的请求越多。
      3. ip_hash：每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器。可以解决session的问题。
      4. fair（第三方）：按后端服务器的相应时间来分配请求，响应时间短的优先分配。

   ```shell
   #当访问http://127.0.0.1/edu/a.html,负载均衡效果，平均到8080和8081端口中去。
   upstream myserver {
   		server	127.0.0.1:8080;
   		server	127.0.0.1:8081 weight=1;
   #		ip_hash;
   #		fair；
   }
   server {
           listen       80;
           server_name  localhost;
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
           location / {
               root   html;
               index  index.html index.htm;
               proxy_pass http://myserver;
           }
   }
   ```

   

5. nginx配置实例3-动静分离

   1. expires：浏览器缓存过期时间。

   ```shell
   #访问静态资源
   server {
           listen       80;
           server_name  localhost;
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
   				#访问/data/目录下的a.html文件
           location /a.html {
               root   /data/;
               index  index.html index.htm;
           }
   }
   ```

   

6. nginx配置高可用集群

   ```shell
   #安装keepalived
   yum install keepalived -y
   rpm -qa keepalived
   
   #安装完之后会在/etc/目录下有keepalived的目录，有文件keepalived.conf
   ```

   ```shell
   #keepalived.conf
   ! Configuration File for keepalived
   
   global_defs {
      notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
      }
      notification_email_from Alexandre.Cassen@firewall.loc
      smtp_server 192.168.200.1
      smtp_connect_timeout 30
      router_id LVS_DEVEL
      vrrp_skip_check_adv_addr
      vrrp_strict
      vrrp_garp_interval 0
      vrrp_gna_interval 0
   }
   
   vrrp_instance VI_1 {
       state MASTER
       interface eth0
       virtual_router_id 51
       priority 100
       advert_int 1
       authentication {
           auth_type PASS
           auth_pass 1111
       }
       virtual_ipaddress {
           192.168.200.16
           192.168.200.17
           192.168.200.18
       }
   }
   
   virtual_server 192.168.200.100 443 {
       delay_loop 6
       lb_algo rr
       lb_kind NAT
       persistence_timeout 50
       protocol TCP
   
       real_server 192.168.201.100 443 {
           weight 1
           SSL_GET {
               url {
                 path /
                 digest ff20ad2481f97b1754ef3e12ecd3a9cc
               }
               url {
                 path /mrtg/
                 digest 9b3a0c85a887a256d6939da88aabd8cd
               }
               connect_timeout 3
               nb_get_retry 3
               delay_before_retry 3
           }
       }
   }
   
   virtual_server 10.10.10.2 1358 {
       delay_loop 6
       lb_algo rr
       lb_kind NAT
       persistence_timeout 50
       protocol TCP
   
       sorry_server 192.168.200.200 1358
   
       real_server 192.168.200.2 1358 {
           weight 1
           HTTP_GET {
               url {
                 path /testurl/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl2/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl3/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               connect_timeout 3
               nb_get_retry 3
               delay_before_retry 3
           }
       }
   
       real_server 192.168.200.3 1358 {
           weight 1
           HTTP_GET {
               url {
                 path /testurl/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334c
               }
               url {
                 path /testurl2/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334c
               }
               connect_timeout 3
               nb_get_retry 3
               delay_before_retry 3
           }
       }
   }
   
   virtual_server 10.10.10.3 1358 {
       delay_loop 3
       lb_algo rr
       lb_kind NAT
       persistence_timeout 50
       protocol TCP
   
       real_server 192.168.200.4 1358 {
           weight 1
           HTTP_GET {
               url {
                 path /testurl/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl2/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl3/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               connect_timeout 3
               nb_get_retry 3
               delay_before_retry 3
           }
       }
   
       real_server 192.168.200.5 1358 {
           weight 1
           HTTP_GET {
               url {
                 path /testurl/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl2/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               url {
                 path /testurl3/test.jsp
                 digest 640205b7b0fc66c1ea91c463fac6334d
               }
               connect_timeout 3
               nb_get_retry 3
               delay_before_retry 3
           }
       }
   }
   ```

   

   主服务器

   ```shell
   ! Configuration File for keepalived
   #全局定义
   global_defs {
      notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
      }
      notification_email_from Alexandre.Cassen@firewall.loc
      smtp_server 192.168.1.11
      smtp_connect_timeout 30
      #LVS_DEVEL是服务器的名字
      router_id LVS_DEVEL
      vrrp_skip_check_adv_addr
      vrrp_strict
      vrrp_garp_interval 0
      vrrp_gna_interval 0
   }
   
   vrrp_script chk_http_port {
      script "/usr/local/src/nginx_check.sh"
      #检测脚本执行的间隔 s
      interval 2
      #设置当前服务器的权重
      weight 2
   }
   
   #虚拟ip的配置
   vrrp_instance VI_1 {
       #当前服务器是主服务器还是备份服务器BACKUP
       state MASTER
       #绑定的网卡
       interface enp0s3
       #主备机的id值，主备机的id值必须相同
       virtual_router_id 51
       #主备机取不同的优先级，主机值较大，备机值较小
       priority 100
       #心跳值，检测当前服务器是否还活着
       advert_int 1
       #权限校验的方式
       authentication {
           auth_type PASS
           auth_pass 1111
       }
       #虚拟ip是什么，可以有多个
       virtual_ipaddress {
           192.168.1.100
       }
   }
   ```

   

   从服务器

   ```shell
   ! Configuration File for keepalived
   #全局定义
   global_defs {
      notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
      }
      notification_email_from Alexandre.Cassen@firewall.loc
      smtp_server 192.168.1.12
      smtp_connect_timeout 30
      #LVS_DEVEL是服务器的名字
      router_id LVS_DEVEL
      vrrp_skip_check_adv_addr
      vrrp_strict
      vrrp_garp_interval 0
      vrrp_gna_interval 0
   }
   
   vrrp_script chk_http_port {
      script "/usr/local/src/nginx_check.sh"
      #检测脚本执行的间隔 s
      interval 2
      #设置当前服务器的权重
      weight 2
   }
   
   #虚拟ip的配置
   vrrp_instance VI_1 {
       #当前服务器是主服务器还是备份服务器BACKUP
       state BACKUP
       #绑定的网卡
       interface enp0s3
       #主备机的id值，主备机的id值必须相同
       virtual_router_id 51
       #主备机取不同的优先级，主机值较大，备机值较小
       priority 90
       #心跳值，检测当前服务器是否还活着
       advert_int 1
       #权限校验的方式
       authentication {
           auth_type PASS
           auth_pass 1111
       }
       #虚拟ip是什么，可以有多个
       virtual_ipaddress {
           192.168.1.100
       }
   }
   ```

   

   

   检测脚本

   ```shell
   #nginx_check.sh
   #!/bin/bash
   A=`ps -C nginx --no-headers | wc -l`
   if [ $A -eq 0 ];then
   		/usr/local/nginx/sbin/nginx
   		sleep 2
   		if [ `ps -C nginx --no-headers | wc -l` -eq 0 ];then
   				killall keepalived
   		fi
   fi
   
   chmod +x nginx_check.sh
   ```

   ```shell
   #主从服务器都打开
   /usr/local/nginx/sbin/nginx
   systemctl start keepalived.service
   ps -ef | grep -f keepalived
   
   #浏览器访问虚拟ip地址
   http://192.168.100
   
   #关闭主服务器
   /usr/local/nginx/sbin/nginx
   systemctl stop keepalived.service
   
   #浏览器访问虚拟ip地址
   http://192.168.100
   ```

   

7. nginx原理

   1. master&worker的工作方式
   2. 一个master和多个worker
   3. 优点：
      1. 可以使用nginx -s reload进行热部署
      2. 每个worker是独立的进程，一个挂了，不会影响其他worker
   4. 设置多少个worker合适
      1. worker数和服务器cpu数相等是最适宜的。
   5. 连接数worker_connection
      1. 发送请求，占用了worker几个连接数。静态2个，动态4个。
      2. nginx有一个master，四个worker，每个worker支持的最大连接数1024，支持的最大并发是多少。
         1. 静态：worker_connection * worker_process/2
         2. 动态：worker_connection * worker_process/4





