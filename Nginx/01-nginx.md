### 内容https://nginx.org/en/docs/

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
      #源码安装，添加外部module
      
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

4. nginx配置实例2-负载均衡

5. nginx配置实例3-动静分离

6. nginx配置高可用集群

7. nginx原理

   

   

