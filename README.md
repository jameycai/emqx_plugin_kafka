## 适配emqx v4.3版本

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
  yum -y install make gcc gcc-c++ glibc-devel glibc-headers kernel-devel kernel-headers m4 ncurses ncurses-devel openssl openssl-devel openssl-libs zlib zlib-devel  libselinux-devel xmlto perl git wget unixODBC unixODBC-devel 
 
  注意：openssl的版本低于1.1.1k，则需要通过源码openssl-1.1.1k.tar.gz来安装openssl
  注意：需要通过源码安装libcoap，下载地址：https://github.com/obgm/libcoap/archive/refs/tags/v4.3.0.tar.gz
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

###### 3. 安装libcoap环境
```
  yum install autoconf automake libtool asciidoc doxygen 

  安装libcoap环境，通过源码进行安装 （首先确保openssl不低于1.1.1k版本，否则通过源码openssl-1.1.1k.tar.gz来安装openssl ）
  wget https://github.com/obgm/libcoap/archive/refs/tags/v4.3.0.tar.gz
  
  tar zxvf libcoap-4.3.0.tar.gz
  cd libcoap-4.3.0
  ./autogen.sh
  make
  make install
```


###### 4. 下载EMQX源码

 官方源码仓库地址，执行此命令
```
  git clone -b v4.3.0 https://github.com/emqx/emqx-rel.git emqx-v4.3
```


###### 5. 修改EMQX文件，增加kafka插件

 修改EMQX主目录下Makefile文件，添加如下行
 ```
  export EMQX_EXTRA_PLUGINS = emqx_plugin_kafka
 ```

 修改EMQX目录下lib-extra/plugins文件，在erlang_plugins中添加如下行打入emqx_plugin_kafka插件
```
   , {emqx_plugin_kafka, {git, "https://github.com/jameycai/emqx_plugin_kafka.git", {branch, "main"}}}   #这里仓库为自己仓库地址 方便之后代码修改提交
```
  



##### 注：以上配置的完整的EMQX源码 [EMQX v4.3的完整源码(增加kafka插件配置)] 下载地址(https://github.com/jameycai/emqx/tree/main-v4.3) 。



###### 6. 编译EMQX，并且启动EMQX
进入emqx-v4.3目录，执行make命令，此过程会因为网络问题，多次出现错误导致停止，只需要不断地make直到成功，有条件建议科学上网。
````
  二进制编译命令： make
  Docker镜像打包： make emqx-docker

  编译成功后，会出现_build目录，然后进入_build/emqx/rel/emqx/bin目录，启动emqx，如下：
  ./emqx start  
````


License
-------

Apache License Version 2.0

Author
------

EMQ X Team.
