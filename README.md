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
###### 1. 先安装相关依赖组件，本文基于CentOS 7.9环境，通过yum方式安装
```
  yum -y install make gcc gcc-c++ glibc-devel glibc-headers kernel-devel kernel-headers m4 ncurses ncurses-devel openssl openssl-devel openssl-libs zlib zlib-devel  libselinux-devel xmlto perl git wget unixODBC unixODBC-devel 
 
  注意：openssl的版本低于1.1.1k，则需要通过源码openssl-1.1.1k.tar.gz来安装openssl
  注意：如果测试CoAP功能，需要安装libcoap，具体地址 https://libcoap.net/install.html
```

###### 2. 准备Erlang/OTP R21及以上环境 (推荐使用erlang23.3版本)
```  
  ## 请根据服务器CPU架构不同选择不同rpm安装包。如果服务器是X86架构，使用esl-erlang_23.3.4.5-1~centos~7_amd64.rpm安装，如下；
  wget http://packages.erlang-solutions.com/erlang/rpm/centos/7/x86_64/esl-erlang_23.3.4.5-1~centos~7_amd64.rpm
  yum install esl-erlang_23.3.4.5-1~centos~7_amd64.rpm 
  
  ## 如果服务器是arm架构，使用esl-erlang_23.3.4.5-1~centos~7_arm64.rpm安装，如下：
  wget http://packages.erlang-solutions.com/erlang/rpm/centos/7/aarch64/esl-erlang_23.3.4.5-1~centos~7_arm64.rpm
  yum install esl-erlang_23.3.4.5-1~centos~7_arm64.rpm 
```


###### 3. 下载EMQX源码

 官方源码仓库地址，执行此命令
```
  git clone -b v4.3.12 https://github.com/emqx/emqx.git emqx-v4.3
```

##### 建议直接下载完整EMQX源码包（基于官方emqx v4.3开源源码+kafka插件），下载地址(https://github.com/jameycai/emqx/tree/main-v4.3) 。



###### 4. 修改EMQX文件，增加kafka插件配置

 修改EMQX主目录下Makefile文件，添加如下行
 ```
  export EMQX_EXTRA_PLUGINS = emqx_plugin_kafka
 ```

 修改EMQX目录下lib-extra/plugins文件，在erlang_plugins中添加如下行打入emqx_plugin_kafka插件
```
   , {emqx_plugin_kafka, {git, "https://github.com/jameycai/emqx_plugin_kafka.git", {branch, "main"}}}   #这里仓库为自己仓库地址 方便之后代码修改提交
```
  

注：以上配置，在完整EMQX源码包（基于官方emqx v4.3开源源码+kafka插件）中，已经包括相关配置，不需要配置。


###### 5. 编译EMQX源码，并且启动EMQX
进入emqx-v4.3源码目录，执行make命令，此过程会因为网络问题，多次出现错误导致停止，只需要不断地make直到成功，有条件建议科学上网。
````
  二进制编译命令： make
  Docker镜像打包： make emqx-docker

  编译成功后，会出现_build目录，然后进入_build/emqx/rel/emqx/bin目录，启动emqx，如下：
  ./emqx start  
````

注意： CoAP协议和Lwm2m协议的端口如果存在冲突，需要分别设置两者的端口。



###### 6. 启动EMQX成功，通过浏览器访问地址（ http://ip:18083 ），访问控制台：

![image](https://user-images.githubusercontent.com/13848153/169473622-00443f97-b3ef-47cf-92eb-ef9cc06e9305.png)

![image](https://user-images.githubusercontent.com/13848153/169473900-c897e274-316d-4734-bc41-c1ddd15f83e5.png)

![image](https://user-images.githubusercontent.com/13848153/169473987-a6a97bc7-08ed-4943-a110-9bd23cdf390b.png)

测试MQTT转Kafka接收到消息
![image](https://user-images.githubusercontent.com/13848153/169672811-98ec0240-b5d5-4fdc-a4fe-a9082aeb6d15.png)


License
-------

Apache License Version 2.0

Author
------

EMQ X Team.
