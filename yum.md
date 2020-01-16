##### 1.简介
    Yum(全称为 Yellow dogUpdater,Modified)是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。yum提供了查找、安装、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。

##### 2.参数
    -h：显示帮助信息； 
    -y：对所有的提问都回答“[yes](http://man.linuxde.net/yes)”；
    -c：指定配置文件；
    -q：安静模式；
    -v：详细模式； 
    -d：设置调试等级（0-10）； 
    -e：设置错误等级（0-10）； 
    -R：设置yum处理一个命令的最大等待时间； 
    -C：完全从缓存中运行，而不去下载或者更新任何头文件。

##### 3.更新&升级
    yum update 				// 更新所有的包
    yum upgrade				// 升级所有的包 
    yum check-update 			// 检查所有可更新的包
    yum update package1			// 更新指定程序包package1
    yum upgrade package1			// 升级指定程序包package1
    yum groupupdate group1 			// 升级程序组group1
    yum --exclude=python* update		// 升级的时候不升级python相关的包

##### 4.安装
    yum install 				// 全部安装 
    yum install package1 			// 安装指定的安装包package1 
    yum localinstall ～ 			// 从硬盘安装rpm包并使用yum解决依赖
    yum groupinsall group1 			// 安装程序组group1
    
    yum install <package name>-<version info>

##### 5.查找&显示
    yum list 				// 显示所有已经安装和可以安装的程序包 
    yum list python				// 显示出名为python的包
    yum list python*			// 显示出名为python开头的所有的包
    yum list updates			// 显示出所有可以更新的包
    yum list installed			// 显示出所有已经安装的包
    yum list extras				// 显示出所有已安装但是不在yum仓库里的包
    yum grouplist 				// 查看系统中已经安装的和可用的软件组，可用的可以安装
    yum info				// 显示所有已经安装和可以安装的程序包的信息
    yum info python				// 显示出名为python的包的信息
    yum info python*			// 显示出名为python开头的所有的包的信息
    yum info updates			// 显示出所有可以更新的包的信息
    yum info installed			// 显示出所有已经安装的包的信息
    yum info extras				// 显示出所有已安装但是不在yum仓库里的包的信息
    yum groupinfo group1			// 显示程序组group1信息
    yum search python 			// 根据关键字python查找相关的包
    yum deplist python 			// 查看程序python依赖情况
    yum provides python			// 检测python包中包含的文件以及软件提供的功能。

##### 6.删除
    yum remove package1 			// 删除程序包package1 
    yum groupremove group1 			// 删除程序组group1 

##### 7.清缓存
    yum clean packages        		// 清除缓存目录下的软件包 
    yum clean headers			// 清除缓存目录下的 headers 
    yum clean oldheaders 			// 清除缓存目录下旧的 headers

##### 8.升级
    yum update 				// 升级所有的包同时也升级软件和系统内核
    yum upgrade				// 只升级所有包，不升级软件和系统内核 PS:但是重启后软件和系统内核一样会升级

##### 9.配置国内yum源
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bk #备份
    
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo #获取文件
    或者
    vim /etc/yum.repos.d/CentOS-Base.repo #内容替换
    
    yum clean all     # 清除系统所有的yum缓存
    yum makecache     # 生成yum缓存

##### 10.epel源安装和配置    
- 查看可用的epel源
```
yum list | grep epel-release
```
- 安装 epel
```
yum install -y epel-release
```
- 配置阿里镜像提供的epel源
```
wget -O /etc/yum.repos.d/epel-7.repo  http://mirrors.aliyun.com/repo/epel-7.repo
```
- 清除缓存
```
yum clean all     # 清除系统所有的yum缓存
yum makecache     # 生成yum缓存
```
##### 11.yum-Remi源配置
    # CentOS 7 / RHEL 7
    yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
    
##### 12.查看yum源
    yum repolist all #查看所有的yum源
    yum repolist enabled #查看可用的yum源
    
##### 附录
[阿里云官方镜像站](https://developer.aliyun.com/mirror/)
