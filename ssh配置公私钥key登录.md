### windows公钥私钥生成
    cmd命令：
    ssh-keygen -t rsa
    默认路径当前用户.ssh文件夹
### 服务器配置ssh的公私钥
- 修改SSH主配置文件
```
vim /etc/ssh/sshd_config
```
```
AuthorizedKeysFile      .ssh/authorized_key

#放文件尾
UseDNS no
AddressFamily inet
SyslogFacility AUTHPRIV
PermitRootLogin yes

PasswordAuthentication yes
RSAAuthentication yes
PubkeyAuthentication yes

```
- 导入公钥进服务器
```
把用户公钥文件的内容
/root/.ssh/authorized_keys
注：文件不存在新建
mkdir .ssh
touch authorized_keys
```
- 重启ssh服务
```
systemctl restart sshd.service
```
#### 常见问题
- 无权限或者确认配置正确，报错所选的用户密钥未在远程主机上注册
```
chmod 700 .ssh

cd .ssh
chmod 600 *

systemctl restart sshd.service
```

----
#### 附录
```
chown www:www -R（递归） /alidata/ （文件目录） 递归修改文件目录的所有者
chmod -R（递归） 777（权限） /alidata/（文件目录） 递归修改文件目录的权限
```

```
-rw------- (600)   只有拥有者有读写权限。
-rw-r--r-- (644)   只有拥有者有读写权限；而属组用户和其他用户只有读权限。
-rwx------ (700)    只有拥有者有读、写、执行权限。
-rwxr-xr-x (755)    拥有者有读、写、执行权限；而属组用户和其他用户只有读、执行权限。
-rwx--x--x (711)    拥有者有读、写、执行权限；而属组用户和其他用户只有执行权限。
-rw-rw-rw- (666)   所有用户都有文件读、写权限。
-rwxrwxrwx (777)  所有用户都有读、写、执行权限。  

-代表无权限，r代表读权限，w代表写权限，x代表执行权限
```
