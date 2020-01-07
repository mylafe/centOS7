##### iptables 的安装与配置
    由于centos7默认是使用firewall作为防火墙，下面介绍如何将系统的防火墙设置为iptables。
    
    #停止firewall 
    systemctl stop firewall.service
    #禁止firewall开机启动 
    systemctl disable firewall.service
     
    #安装iptables 
    yum install iptables-services

##### 编辑防火墙文件 （建议都在配置文件配置，不要命令配置）
    vi /etc/sysconfig/iptables 
    #添加80和3306端口 等等（自己配置）
    -A INPUT -m state –state NEW -m tcp -p tcp –dport 80 -j ACCEPT        #80端口开放
    -A INPUT -m state –state NEW -m tcp -p tcp –dport 3306 -j ACCEPT　　　 #3306端口开放
    -I INPUT -s 113.106.93.110 -p tcp --dport 8089 -j DROP              #禁止指定IP访问 8089
    -I INPUT -s 113.106.93.110 -p tcp --dport 8080 -j ACCEPT            #开放固定ipIP访问 8080

    #重启防火墙使配置文件生效  
    systemctl restart iptables.service
    #设置iptables防火墙为开机启动项 
    systemctl enable iptables.service
    
    service iptables  start     #启动服务
    service iptables  stop　　   #停止服务
    service iptables  restart　　#重启服务

    关闭SELINUX 
    vi /etc/selinux/config 
    　#注释以下配置 
    　SELINUX=enforcing 
    　SELINUXTYPE=targeted 
    　#增加以下配置 
    　SELINUX=disabled 
    　#使配置立即生效 
    　setenforce 0

##### 常用命令
    1、查看当前iptables状态
    iptables -nL #默认查看filter表的状态，如果需要查看其他表的状态加上 -t 表名
    iptables -nL --line-numbers #可以列出序列号，在插入或者删除的时候就不用自己去数了
    iptables -nL --line-numbers --verbose #可以查看到包过滤的流量统计，访问次数等
    
    2、插入一条记录
    iptables -I INPUT 1 -i lo -j ACCPET #在第一条的位置插入一条记录，接受所有来自lo网口的访问
    iptables -I INPUT 2 -s 192.168.1.0/24 -j ACCPET # 如果在INPUT中不指明在第几条插入，默认就是在第一条插入
    
    3、追加一条记录
    iptables -A INPUT -s 192.168.2.0/24 -j ACCEPT #在INPUT最后追加一条记录。
    
    4、删除一条记录
    iptables -D INPUT 7 #删除第7条记录
    
    5、针对协议开放
    iptables -I INPUT -p imcp -j ACCEPT
    
    6、针对端口开放（需要指明协议）
    iptables -I INPUT -p tcp --dport 22 -j ACCEPT
    
    7、限制ip端口访问
    iptables -I INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCPET
    
    8、拒绝所有访问
    iptables -A INPUT -j DROP #这个一般放到最后，不然会对前面的规则造成影响。
    
    9、根据时段限制访问
    iptables -A INPUT -p tcp -m time --timestart 00:00 --timestop 02:00 -j DROP #这里的时间是指UTC时间记得换算
    
    10、限制单个IP一分钟内建立的连接数
    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 25 -j REJECT
    
    11、保存iptables规则
    iptables-save > /etc/sysconfig/iptables
    
    12、从文件里面恢复iptables规则
    iptables-restore < /etc/sysconfig/iptables
    
    13、对外建立的连接经过INPUT不拦截
    iptables -I INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    
    14、端口转发（本机8080转发到远程192.168.1.22:80）
    iptables -t nat -A PREROUTING -p tcp -i eth0 --dport 8080 -j DNAT --to 192.168.1.22:80
    iptables -t nat -A POSTROUTING -j MASQUERADE
    echo 1 > /proc/sys/net/ipv4/ip_forward #需要打开网转发
    iptables -t filter FORWARD -d 192.168.1.22/32 -j ACCEPT #转发的FROWARD要允许双方的数据传输
    iptables -t filter FORWARD -s 192.168.1.22/32 -j ACCEPT
