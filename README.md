# 适配emqx v4.3版本

emqx-plugin-template
====================

[emqx/emqx-plugin-template at emqx-v4 (github.com)](https://github.com/emqx/emqx-plugin-template/tree/emqx-v4) This is a template plugin for the EMQ X broker. 

Plugin Config
-------------

Each plugin should have a 'etc/{plugin_name}.conf|config' file to store application config.

Authentication and ACL
----------------------

```
emqx:hook('client.authenticate', fun ?MODULE:on_client_authenticate/3, [Env]).
emqx:hook('client.check_acl', fun ?MODULE:on_client_check_acl/5, [Env]).
```

Build the EMQX broker
-----------------
###### 1. 基于CentOS7.5环境下编译，先安装相关插件
```
  yum -y install  make gcc gcc-c++ glibc-devel glibc-headers kernel-devel kernel-headers m4 ncurses ncurses-devel openssl openssl-devel openssl-libs zlib zlib-devel libselinux-devel xmlto perl git wget unixODBC unixODBC-devel 
 
  注意：openssl的版本不是1.1.1k，则需要通过源码openssl-1.1.1k.tar.gz来安装openssl
```

###### 2. 准备Erlang/OTP R21及以上环境
```
  根据服务器CPU架构不同选择不同安装包，如果是X86架构，使用esl-erlang_23.3.4.5-1~centos~7_amd64.rpm安装，如下；
  
  wget http://packages.erlang-solutions.com/erlang/rpm/centos/7/x86_64/esl-erlang_23.3.4.5-1~centos~7_amd64.rpm
  yum install esl-erlang_23.3.4.5-1~centos~7_amd64.rpm 
  
  如果是arm架构，使用esl-erlang_23.3.4.5-1~centos~7_arm64.rpm安装，如下：
  
  wget http://packages.erlang-solutions.com/erlang/rpm/centos/7/aarch64/esl-erlang_23.3.4.5-1~centos~7_arm64.rpm
  yum install esl-erlang_23.3.4.5-1~centos~7_arm64.rpm 
```


###### 3. 下载EMQX源码

 官方源码仓库地址，执行此命令
```
  git clone -b v4.3.0 https://github.com/emqx/emqx-rel.git emqx-v4.3
```


###### 4. 修改EMQX文件，增加kafka插件

 修改EMQX主目录下Makefile文件，添加如下行
 ```
  export EMQX_EXTRA_PLUGINS = emqx_plugin_kafka
 ```

 修改EMQX目录下lib-extra/plugins文件，在erlang_plugins中添加如下行打入emqx_plugin_kafka插件
```
   , {emqx_plugin_kafka, {git, "https://github.com/jameycai/emqx_plugin_kafka.git", {branch, "main"}}}   #这里仓库为自己仓库地址 方便之后代码修改提交
```
  

注：下载 [EMQX完整源码(增加kafka插件配置)] (https://github.com/jameycai/emqx/tree/main-v4.3) 。


###### 5. 编译EMQX，并且启动EMQX

进入emqx目录，执行make命令，需要保持外网通畅，有条件建议科学上网。

二进制编译命令：`make`

docker镜像打包：`make emqx-docker`


License
-------

Apache License Version 2.0

Author
------

EMQ X Team.
