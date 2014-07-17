# Reverse_proxy

## openshift-nginx
latest change 2014-07-17 使用本参考请留意openshift的最新更新说明，特别是环境变量的变动。

1. ### create app

        Do-It-Yourself 0.1

2. ### 用ssh登录

3. ### 下载并编译Nginx

        cd $OPENSHIFT_TMP_DIR
        wget http://nginx.org/download/nginx-1.7.3.tar.gz
        wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.35.tar.bz2 #下载并编译nginx的依赖包PCRE
        tar zxf nginx-1.7.3.tar.gz
        tar jxf pcre-8.35.tar.bz2
        cd nginx-1.7.3
        ./configure --prefix=$OPENSHIFT_DATA_DIR --with-pcre=$OPENSHIFT_TMP_DIR/pcre-8.35 #配置nginx未添加ssl支持
        make install 安装

4. ### 定制nginx.conf
   
 shell下输入`env | grep IP`和`env | grep PORT`获取`$OPENSHIFT_DIY_IP`和`$OPENSHFT_DIY_PORT`

	events {
	       use epoll;
            worker_connections  10240;
        }
        
        http {
        include       mime.types;
        default_type  application/octet-stream;
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        #keepalive_timeout  0;
        keepalive_timeout  65;

        #gzip  on;

        server {
            listen 127.8.191.1:8080; 
            server_name diy-agent01.rhcloud.com;
        #	listen 127.8.191.1:443;
        #	server_name agentcg01.ml;
	       #ssl on;
	       #ssl_certificate $OPENSHIFT_DATA_DIR/conf/agnetcg01.crt;
	       #ssl_certificate_key $OPENSHIFT_DATA_DIR/conf/agentcg01.key;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://218.253.0.145/;
                proxy_redirect  off;
                proxy_set_header        X-Real-IP       $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
            #root   html;
           #index  index.html index.htm;
        }
        
5. ### 修改启动和停止脚本
  
退出shell
    
        vim .openshift/action_hooks/start
        
        #!/bin/bash
        nohup $OPENSHIFT_DATA_DIR/sbin/nginx > $OPENSHIFT_DIY_LOG_DIR/server.log 2>&1 &
        
        vim .openshift/action_hooks/stop
        
        to be continued
