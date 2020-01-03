### 隐藏nginx版本
    vim /etc/nginx.conf 在http配置端中加入一行: server_tokens off;
### 隐藏php版本
    vim xxx/php.ini，修改expose_php设置为expose_php=Off;