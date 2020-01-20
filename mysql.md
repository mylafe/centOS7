##### 1.官网下载rpm包
    https://dev.mysql.com/downloads/repo/yum/
    wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
    
##### 2.安装
    yum -y install mysql57-community-release-el7-10.noarch.rpm #30k左右
    yum -y install mysql-community-server #安装MySQL服务 等待结束，安装完成
    
    systemctl start mysqld #启动服务
    
##### 3.查看密码
    cat /var/log/mysqld.log | grep 'password'
    对应匹配信息，密码是:之后的所有字符
    [Note] A temporary password is generated for root@localhost: ;6U2oMSrXLs:
    
##### 4.登录
    mysql -uroot -p
    password：#输入密码

##### 4.常见问题
- 安装好，需要初始化密码
```
修正密码强度校验规则(用于测试环境使用)，高版本的mysql在修改密码时会限制简单密码的创建，如果单单是为了测试使用，可以将他的密码检测策略修改下：

修改：密码最小长度策略
mysql> set global validate_password_length=0;

修改：密码强度检查等级策略，0/LOW、1/MEDIUM、2/STRONG
mysql> set global validate_password_policy=0;
 
修改密码
mysql> set password for 'root'@'localhost' = password('root123');

刷新
mysql> flush privileges;
```

- 开启远程连接
```
开启mysql的root用户远程连接服务(%号即远程连接，IDENTIFIED BY后面跟的密码)
update user set host='%' where user='root';

如果有防火墙，需要开启mysql端口（3306）服务

查看修改结果
use mysql;
select host,user from user;

刷新
flush privileges;
```

- Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' 权限问题
```
mysql -uroot -p
root

这是/var/lib/mysql权限问题，修改MySQL权限为当前用户
sudo chown -R xxx:xxx /var/lib/mysql #赋权
xxx为当前的用户名以及所属组 
重启MySQL服务

查看用户组：
whoami 查看当前用户
groups 当前用户
```
