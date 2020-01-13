##### 1.官网下载压缩包
    地址：https://nginx.org/en/download.html
    wget https://nginx.org/download/nginx-1.18.0.tar.gz #获取
    tar -zxvf nginx-1.18.0.tar.gz #解压
    mv nginx-1.18.0 nginx #重命名
    cd nginx

##### 2.配置
    ./configure
    --help自定义配置-不推荐

##### 3.编译安装
    make
    make install
    
    whereis nginx #查找nginx安装路径
##### 4.启动
    cd /usr/local/nginx/sbin/
    
    ./nginx #启动
    ./nginx -s stop #此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程
    ./nginx -s quit #此方式停止步骤是待nginx进程处理任务完毕进行停止
    ./nginx -s reload #平滑启动

    ps aux|grep nginx #查看nginx进程
    
##### 5.添加环境变量
    vi ~/.bashrc #编辑
    
    # 环境变量
    export NGINX_HOME=/usr/local/nginx
    export PATH=$PATH:$NGINX_HOME/sbin
    
    source ~/.bashrc #生效

##### 6.加入systemctl管理服务
    cd /usr/lib/systemd/system #新增nginx.service文件
    vi nginx.service
    chmod 754 nginx.service #754权限
    
    添加配置：
    [Unit]
    Description=nginx
    After=network.target
      
    [Service]
    Type=forking
    ExecStart=/usr/local/nginx/sbin/nginx
    ExecReload=/usr/local/nginx/sbin/nginx -s reload
    ExecStop=/usr/local/nginx/sbin/nginx -s quit
    PrivateTmp=true
      
    [Install]
    WantedBy=multi-user.target
    
    
    参数说明：
    注意：[Service]的启动、重启、停止命令全部要求使用绝对路径
    [Unit]:服务的说明
    Description:描述服务
    After:描述服务类别
    [Service]服务运行参数的设置
    Type=forking是后台运行的形式
    ExecStart为服务的具体运行命令
    ExecReload为重启命令
    ExecStop为停止命令
    PrivateTmp=True表示给服务分配独立的临时空间
    [Install]运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3
    
    systemctl enable nginx.service #开机启动
    systemctl disable nginx.service #取消开机启动
    systemctl start nginx.service #启动服务
    systemctl stop nginx #停止
    systemctl restart nginx #重启
    systemctl reload nginx #平滑重启
    
    systemctl list-units --type=service #查看已启动的服务
    
##### 常见问题：
- Nginx 出现 403 Forbidden 最终解决方法
```
步骤一：目录权限
chmod -R 755 / var/www

步骤二：nginx.conf
vim /etc/nginx/nginx.conf
把user用户名改为user root 或其它有高权限的用户名称即可

步骤三：如果是centos,看一下selinux是否关闭
查看SELinux状态：
1、/usr/sbin/sestatus -v ##如果SELinux status参数为enabled即为开启状态
SELinux status:enabled
2、getenforce ##也可以用这个命令检查
关闭SELinux：
1、临时关闭（不用重启机器）：
setenforce 0 ##设置SELinux 成为permissive模式
##setenforce 1 ##设置SELinux 成为enforcing模式

2、修改配置文件需要重启机器：
修改/etc/selinux/config 文件
将SELINUX=enforcing改为SELINUX=disabled
重启机器即可
```
