##### 必要安装
    yum -y install gcc gcc-c++

##### 1.安装EPEL源-已配置跳过
    yum install epel-release
    
    安装REMI源
    yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

##### 2.安装Yum源管理工具
    yum install yum-utils
    
    yum-utils提供的程序之一是yum-config-manager，您可以使用它来启用Remi存储库作为安装不同PHP版本的默认存储库
    yum-config-manager --enable remi-php71 [ 安装PHP 7.1 ]
    yum-config-manager --enable remi-php72 [ 安装PHP 7.2 ]
    yum-config-manager --enable remi-php73 [ 安装PHP 7.3 ]

##### 3.安装php和模块
    yum -y install php php-mcrypt php-devel php-cli php-gd php-pear php-curl php-fpm php-mysql php-ldap php-zip php-fileinfo
    
    php -v/-m #查看版本/模块
    
    systemctl start php-fpm
    systemctl enable php-fpm.service
    
    php -ini #查看php.ini文件
    
##### 4.pecl安装扩展准备
    yum -y install php-pear
    
##### 5.pecl安装redis
    pecl install redis
    
##### 附录
- php的nginx配置
```
server {
        #监听端口
        listen  80;
        #域名
        server_name  192.168.17.26;
         #首页名称
        index   index.php index.html index.htm;

        # 默认网站根目录（www目录）
        root   /var/www/;

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

        # PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI协议默认配置.
        # Fastcgi服务器和程序(PHP,Python)沟通的协议.
        location ~ \.php$ {
            # 设置监听端口
            fastcgi_pass   127.0.0.1:9000;
            # sock协议
            # fastcgi_pass  unix:/tmp/php-cgi.sock
            # 设置nginx的默认首页文件(上面已经设置过了，可以删除)
            fastcgi_index  index.php;
            # 设置脚本文件请求的路径
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            # 引入fastcgi的配置文件
            include        fastcgi_params;
        }
    }
```
- [性能评测](https://blog.csdn.net/liv2005/article/details/7741732)
```
    TCP Socket(本地回环127.0.0.1)方式的数据传输:
    Nginx <=> socket <=> TCP/IP <=> socket <=> PHP-FPM
    
    Unix domain socket和Tcp socket，在性能上没有显著差距。
    当访问压力较小时，可以使用UnixSocket，因为不会占用额外的端口、且理论上效率较高。
    当高并发的时候，Tcp Socket能表现出更高的稳定性，且性能并不差于UnixSocket，缺点是会占用大量的临时端口，需要用内核参数优化。
```
