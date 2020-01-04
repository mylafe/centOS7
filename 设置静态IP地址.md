##### 查看IP
    ip addr

##### 编辑修改配置
    vi /etc/sysconfig/network-scripts/ifcfg-你的网卡名字

    BOOTPROTO=static # 使用静态IP地址，默认为dhcp 
    IPADDR=192.168.75.136 # 设置的静态IP地址
    NETMASK=255.255.255.0 # 子网掩码 
    GATEWAY=19.37.33.1 # 网关地址 
    ONBOOT=yes  #设置网卡启动方式为 开机启动 并且可以通过系统服务管理器
    
    查询相关配置-window
    ipconfig

##### 重启服务
    service network restart

- 会导致xshell远程链接失败
