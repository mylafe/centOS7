##### 下载node，找到对应系统对应版本
    https://nodejs.org/en/download/
##### 获取
    wget https://npm.taobao.org/mirrors/node/v14.8.0/node-v14.8.0-linux-x64.tar.xz
##### 解压
    tar -xvf  node-v*.tar.xz
##### 重命名
    mv  node-v* nodejs
##### 配置环境变量-直接使用常见问题配置
    vi /etc/profile
    
    export NODE_HOME=/usr/local/nodejs # node路径
    export PATH=$PATH:$NODE_HOME/bin
    export NODE_PATH=$NODE_HOME/lib/node_modules
##### 设置生效
    source /etc/profile
    node -v 查看
    npm -v 查看

- 一步到位（版本问题）
```
yum install -y nodejs #安装
yum remove -y nodejs #卸载
```
##### 常见问题
- 每次重启都需要source /etc/profile的原因-环境变量
- 新开centos终端必须source /etc/profile才能使用[原因](https://blog.csdn.net/cdnight/article/details/86653006)
```
如果为了一完成配置信息就能使用，
那么就不用在/etc/profile 和 ~/.profile文件中添加关于软件的配置信息。
而是在/etc/bash.bashrc 或者 ~/.bashrc 中添加，这样就能马上使用了。

vi ~/.bashrc #编辑
# 环境变量
export NODE_HOME=/usr/local/nodejs
export PATH=$NODE_HOME/bin:$PATH
export NODE_PATH=$NODE_HOME/lib/node_modules

source ~/.bashrc #生效
```
