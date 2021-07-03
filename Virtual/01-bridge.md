### 手顺

+++

1. 自动分配一个网络内可用的ip

   ```shell
   dhclient 
   ```

   

2. 配置静态ip

   ```shell
   #vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   #BOOTPROTO=dhcp
   BOOTPROTO=static
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   IPV6INIT=yes
   IPV6_AUTOCONF=yes
   IPV6_DEFROUTE=yes
   IPV6_FAILURE_FATAL=no
   IPV6_ADDR_GEN_MODE=stable-privacy
   NAME=enp0s3
   UUID=87a9455b-d71a-41a4-b7fb-95aff6bddc70
   DEVICE=enp0s3
   #ONBOOT=no
   ONBOOT=yes
   IPADDR=192.168.1.11
   NETMASK=255.255.255.0
   GATEWAY=192.168.1.1
   DNS1=119.29.29.29
   
   systemctl restart network.service
   ```

   

3. 防火墙

   ```shell
   systemctl status firewalld.service
   sudo firewall-cmd --list-all
   sudo firewall-cmd --add-port=80/tcp --permanent
   sudo firewall-cmd --add-service=http --permanent
   sudo firewall-cmd --reload
   sudo firewall-cmd --list-all
   ```

   

4. ss