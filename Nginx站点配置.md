### 0.入口
    路径：/usr/local/nginx/conf
    配置:文件 nginx.conf 最后一行引入各个站点配置 include vhost/*.conf;
    include /etc/nginx/conf.d/*.conf;
```code
user  www www;

worker_processes auto;
worker_cpu_affinity auto;

error_log  /home/wwwlogs/nginx_error.log  crit;

pid        /usr/local/nginx/logs/nginx.pid;

#Specifies the value for maximum file descriptors that can be opened by this process.
worker_rlimit_nofile 51200;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept off;
        accept_mutex off;
    }

http
    {
        include       mime.types;
        default_type  application/octet-stream;

        server_names_hash_bucket_size 128;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 50m;

        sendfile on;
        sendfile_max_chunk 512k;
        tcp_nopush on;

        keepalive_timeout 60;

        tcp_nodelay on;

        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
        fastcgi_buffer_size 64k;
        fastcgi_buffers 4 64k;
        fastcgi_busy_buffers_size 128k;
        fastcgi_temp_file_write_size 256k;

        gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml application/xml+rss;
        gzip_vary on;
        gzip_proxied   expired no-cache no-store private auth;
        gzip_disable   "MSIE [1-6]\.";

        #limit_conn_zone $binary_remote_addr zone=perip:10m;
        ##If enable limit_conn_zone,add "limit_conn perip 10;" to server section.

        server_tokens off;
        access_log off;

server
    {
        listen 80 default_server reuseport;
        #listen [::]:80 default_server ipv6only=on;
        server_name _;
        index index.html index.htm index.php;
        root  /home/wwwroot/default;

        #error_page   404   /404.html;

        # Deny access to PHP files in specific directory
        #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

        include enable-php.conf;

        location /nginx_status
        {
            stub_status on;
            access_log   off;
        }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /.well-known {
            allow all;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log  /home/wwwlogs/access.log;
    }
include vhost/*.conf;
}
```

### 1.php配置
    vhost目录 新增新建站点conf
    
- 普通
```code
server {
        listen       80;
        server_name  name1 name2;
        index index.html index.htm index.php;
        root /alidata/www/jzg/web;

        location / {
                # Redirect everything that isn't a real file to index.php
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ .*\.(php|php5)?$
        {
                fastcgi_pass  unix:/tmp/php-cgi.sock;
                #fastcgi_pass  127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }


        access_log  /alidata/log/nginx/access/jzg.log;
}
```

- https(SSL)配置
```code
    判断服务器的端口是否打开:telnet 121.40.57.75 443
    当命令窗口跳为全黑，或者出现应用的名称提示，那么就说明端口正常连上了。没有提示连接失败，那么就表示连成功了。
```

```code
server {
        listen 80;
        server_name  name1 name2;
        rewrite ^(.*) https://$server_name$1 permanent;
}

server {
        listen 443;
        server_name  name1 name2;
        index index.html index.htm index.php;
        root /alidata/www/jzg/web;

        ssl on;
        ssl_certificate   /opt/ssl_cert/11.com.pem;
        ssl_certificate_key  /opt/ssl_cert/11.com.key;
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        location / {
                # Redirect everything that isn't a real file to index.php
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ .*\.(php|php5)?$
        {
                fastcgi_pass  unix:/tmp/php-cgi.sock;
                #fastcgi_pass  127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }


        access_log  /alidata/log/nginx/access/jzg.log;
}
```

-  doc.conf 文档说明
```
server {
        listen       80;
        server_name  name1;
        index index.html index.htm index.php;
        root /alidata/www/name1;
        location ~ .*\.(php|php5)?$
        {
                #fastcgi_pass  unix:/tmp/php-cgi.sock;
                fastcgi_pass  127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
                expires 30d;
        }
        location ~ .*\.(js|css)?$
        {
                expires 1h;
        }

        access_log  /alidata/log/nginx/access/default.log;
}

```
-  es.conf es
```
upstream es {
    server 127.0.0.1:9200;
}
upstream kibana {
    server 127.0.0.1:5601;

}

server {

    listen 80;
    server_name  name1;
    charset utf8;

    location / {
        proxy_pass http://es;
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        ##方案一、用户认证方式，依赖apache-utils中的生成密码工具
        auth_basic "secret";
        auth_basic_user_file /alidata/server/nginx/pass.db;

        ##方案二、只有公司的外网IP，局域网IP可以访问
        #allow   5*.**.**.**0;
        #allow   192.168.8.0/24;
        #deny    all;
    }
}
server {

    listen 80;
    server_name  name1;
    charset utf8;

 root /alidata/www/default;
    location / {
        proxy_pass http://kibana$request_uri;
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        ##方案一、用户认证方式，依赖apache-utils中的生成密码工具
        auth_basic "secret";
        auth_basic_user_file /alidata/server/nginx/pass.db;

        ##方案二、只有公司的外网IP，局域网IP可以访问
        #allow   5*.**.**.**0;
        #allow   192.168.8.0/24;
        #deny    all;
    }
}
```

### 2.web配置
- conf slim
```
server {
        listen 8989;
        server_name mame1;
        index index.html index.htm index.php default.html default.htm default.php;
        root  /opt/www/name/public;

   # ssl_certificate   ssl_cert/11.pem;
   # ssl_certificate_key  ssl_cert/11.key;
   # ssl_session_timeout 5m;
   # ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
   # ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
   # ssl_prefer_server_ciphers on;

        #error_page   404   /404.html;

location ~ [^/]\.php(/|$) {
    #fastcgi_pass remote_php_ip:9000;
    fastcgi_pass unix:/dev/shm/php-cgi.sock;
    fastcgi_index index.php;
    include fastcgi.conf;
  }


        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }
        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }
        location / {
            try_files $uri $uri/ /index.php$is_args$args;
        }
        location ~ /\.
        {
            deny all;
        }
        access_log off;
        #access_log  /usr/local/nginx/logs/name1.log;
}
```

### 附录
    $args ：这个变量等于请求行中的参数，同$query_string
    $content_length ： 请求头中的Content-length字段。
    $content_type ： 请求头中的Content-Type字段。
    $document_root ： 当前请求在root指令中指定的值。
    $host ： 请求主机头字段，否则为服务器名称。
    $http_user_agent ： 客户端agent信息
    $http_cookie ： 客户端cookie信息
    $limit_rate ： 这个变量可以限制连接速率。
    $request_method ： 客户端请求的动作，通常为GET或POST。
    $remote_addr ： 客户端的IP地址。
    $remote_port ： 客户端的端口。
    $remote_user ： 已经经过Auth Basic Module验证的用户名。
    $request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
    $scheme ： HTTP方法（如http，https）。
    $server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
    $server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
    $server_name ： 服务器名称。
    $server_port ： 请求到达服务器的端口号。
    $request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
    $uri ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
    $document_uri ： 与$uri相同。
