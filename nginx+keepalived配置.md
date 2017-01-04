## nginx安装配置
+ 安装pcre
```shell
tar -zxvf pcre-8.39.tar.gz
cd pcre-8.39/
./configure
make
make install
```
+ 安装zlib
```shell
tar -zxvf zlib-1.2.8.tar.gz
cd zlib-1.2.8/
./configure
make
make install
```
+ 安装openssl
```shell
tar zxvf openssl-1.1.0c.tar.gz
cd openssl-1.1.0c.tar.gz
./config
make
make install
```
+ 安装nginx
 - 解压：tar zxvf nginx-1.10.2.tar.gz
 - 进入解压后目录：cd nginx-1.10.2/
 - 编译配置：./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-pcre=../pcre-8.39 --with-zlib=../zlib-1.2.8 --with-openssl=../openssl-1.1.0c
 - 编译：make
 - 安装：make install
 + 配置
  - 配置文件路径：/usr/local/nginx/conf/nginx.conf
 ```shell
 worker_processes  4;
events {
    use epoll;
    worker_connections  65535;
}
#虚拟机为4个物理cpu，32GB内存，使用epoll事件模型，最大连接数为65535

#日志配置，请求耗时，加入参数$request_time
    log_format  main  '$request_time-$remote_addr - $remote_user [$time_local] "$request" '
          '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

access_log  /usr/local/nginx/logs/access.log  main;

#负载均衡配置
	upstream  mylocalsite {  
		ip_hash; 
     server  192.168.88.27:8888;
     server  192.168.88.28:8888;
		}
```
+ 几个常用命令
 - 启动：/usr/local/nginx/sbin/nginx，启动完成 shell 是不会有输出的
 - 检查是否有 Nginx 进程：ps aux | grep nginx，正常是显示 3 个结果出来
 - 检查Nginx 是否启动并监听了 80 端口：netstat -ntulp | grep 80
 - 访问：本地ip，如果能看到：Welcome to nginx!，即可表示安装成功
 - 检查Nginx 启用的配置文件是哪个：/usr/local/nginx/sbin/nginx -t
 - 刷新Nginx 配置后重启：/usr/local/nginx/sbin/nginx -s reload
 - 停止Nginx：/usr/local/nginx/sbin/nginx -s stop

## keepalived安装配置
+ 安装openssl
```shell
tar xvzf openssl-1.0.0d.tar.gz
cd openssl-1.0.0d
./config shared --prefix=/usr/local
make && make install
```
+ 安装popt
```shell
tar xvzf popt-1.14.tar.gz
cd popt-1.14
./configure --prefix=/usr/local
make && make install
```
+ 安装keepalived
```shell
tar zxvf keepalived-1.2.18.tar.gz 
cd keepalived-1.2.18 
./configure --prefix=/usr/local/keepalived //指定安装路径
make && make install
ln -s /usr/local/keepalived/sbin/keepalived /usr/bin/keepalived
cp /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/keepalived
chmod 755 /etc/init.d/keepalived //添加执行权限
chkconfig keepalived on //开机启动
```
 - 修改/etc/init.d/keepalived中的程序路径
 ```shell
 # Source configuration file (we set KEEPALIVED_OPTIONS there)
 . /etc/sysconfig/keepalived  改为
 ```
 
 ```shell
 # Source configuration file (we set KEEPALIVED_OPTIONS there)
 . /usr/local/keepalived/etc/sysconfig/keepalived
 ```
  - 默认情况下，keepalived 会读取 /etc/keepalived 下keepalived.conf 文件，如果没有建立这个文件，keepalived也不会报错，但是会发现，所创建的关于keepalived的相关参数根本就没有生效。
  ```shell
  mkdir /etc/keepalived
  ln -s /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf
  ```
  - 启动测试：service keepalived restart
  - 查看虚拟ip是否生效：ip addr
  
 ### 配置keepalived参数（主、备有所不同）
 ```shell
 vi /usr/local/keepalived/etc/keepalived/keepalived.conf
 ```
 + 将keepalived.conf配置文件中内容全部删除，只留下下列配置信息
 ```shell
 vrrp_script check_run {
 script "/usr/local/nginx/check_nginx_pid.sh"        # 运行监控脚本
       interval 2                                    # 时间间隔，2秒
       weight 2                                      # 权重
}

vrrp_sync_group VG1 {
    group {
       VI_1
       }
}

vrrp_instance VI_1 {
   state MASTER    #（重点）Backup机器这里设置为：BACKUP
   interface eth0  #一定要是网卡的名称
   virtual_router_id 51 #一个局域网该id唯一
   mcast_src_ip  168.33.54.101   #此网卡的IP
   priority 100   #（重点）优先级要高于备机，备机建议设置为99
   advert_int 1   #Master 与 Backup 负载均衡器之间同步检查的时间间隔，单位是秒
   authentication {
       auth_type PASS
       auth_pass 1111
   }

   #（重点）配置虚拟 IP 地址，如果有多个则一行一个
   virtual_ipaddress {
       168.33.54.99  #虚拟IP
   }
   
   #（重点）脚本监控调用
   track_script {
        check_run
        }
}
 ```
 + 添加监控脚本
 ```shell
 vim /usr/local/nginx/check_nginx_pid.sh
 ```
 ```shell
 #!/bin/sh

a=`ps -C nginx --no-header|wc -l`
b=0

if [ $a == $b ]
then
  /usr/local/nginx/sbin/nginx 
    if [ $a == $b ]
       then
         killall keepalived
    fi
fi
 ```
 + 查看keepalived日志 
 ```shell
 tail -f /var/log/messages
 ```
 
 
