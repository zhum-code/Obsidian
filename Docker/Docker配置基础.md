
### 1.1. 实例
``` bash
docker run -itd -v /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.144-0.b01.ky3.ppc64:/jvm -p 1.10.1.200:12345:12345 --name honeypot honeyimage /jvm/bin/java -jar /honeypot.jar
```
### 2. 说明
 
 + docker run ：从镜像中启动容器
 + -itd ： 容器为交互式 ，后台启动
 + -v /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.144-0.b01.ky3.ppc64:/jvm ： -v为映射地址，将主机位置/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.144-0.b01.ky3.ppc64的内容映射到容器中的/jvm中去（应该类似共享文件夹），从容器中导出镜像时，映射的内容并不会打包，会成为空的文件夹。在本配置中/jvm文件夹会为空。
 +  -p 1.10.1.200:12345:12345 ：映射端口（或ip），将主机的1.10.1.200的12345端口映射到docker容器内部的12345端口上，可用来外部访问容器内部的操作
 +  --name honeypot honeyimage ：--name 容器名称 镜像名称
 + /jvm/bin/java -jar /honeypot.jar : 容器启动时附带的命令

### 3. 基本命令

+  docker 命令中的 容器ID 支持模糊查询，容器名称不支持
+ docker ps : 显示运行中的容器， -a ： 显示所有容器  --no-trunc=true 有的信息过长会省略，加此参数会显示完整信息
+ docker images ：查看容器
+ docker cp 主机位置 容器名称：容器位置 ： 复制命令，将主机文件搬运到容器中去
+ docker run ：从镜像中启动容器（上方举例）
+ docker attach 容器名称/容器ID ：连接容器，进去容器内部
+ docker stop 容器名称/容器ID：停止容器
+ docker rm 容器名称/容器ID：删除容器
+ docker rmi 镜像名称/镜像ID：删除镜像
+ docker logs 容器名称/容器ID：查看容器日志，打印，报错都会显示在这里
+ systemctl stop docker ： 停止docker服务 关闭服务会导致所有容器停止
+ systemctl start docker ： 启动docker服务
+ docker import 离线镜像包名.tar 镜像名称 ： 从离线镜像包中导入镜像
+ docker export 容器名称 > 镜像离线包名.tar 从容器中导出镜像包。
+ docker exec -it 容器名称/ID /bin/bash 连接容器（当attach失效时）

心得以及遇到的问题：

 修改docker0ip：修改/etc/docker/daemon.json 文件内容 {”bip”:"170.17.2.1/24"，“mtu”：1500} ，命令：systemctl stop docker;ip link set dev docker0 down;brctl delbr docker0 ; systemctl start docker

 docker容器类似简洁，小型的虚拟机系统，拥有ifconfig，netstat等命令，可以安装某些软件，类似可以安装jjdk。

 docker run 容器只创建，未启动。说明启动报错，容器有问题，类似可能镜像与宿主机系统不兼容造成的。

 docker 容器启动后立即停止，up -> exited 可使用docker logs 容器名称 查看是否有报错，配置问题，命令出错，都可能造成此问题。如果没有报错，那么可能是容器类似守护线程，当主线程停止时，守护线程也会停止。也就是说容器中启动的程序必然需要一个前台程序，程序停止，容器也会立即停止。

 docker拥有四种网络模式：host，container，none，bridge

| Docker网络模式  | 配置                        | 说明                                                                        |
| ----------- | ------------------------- | ------------------------------------------------------------------------- |
| host模式      | –net=host                 | 容器和宿主机共享Network namespace。                                                |
| container模式 | –net=container:NAME_or_ID | 容器和另外一个容器共享Network namespace。 kubernetes中的pod就是多个容器共享一个Network namespace。 |
| none模式      | –net=none                 | 容器有独立的Network namespace，但并没有对其进行任何网络设置，如分配veth pair 和网桥连接，配置IP等。          |
| bridge模式    | –net=bridge               | （默认为该模式）                                                                  |

Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接通信。

Docker网桥是宿主机虚拟出来的，并不是真实存在的网络设备，外部网络是无法寻址到的，这也意味着外部网络无法通过直接Container-IP访问到容器。如果容器希望外部访问能够访问到，可以通过映射容器端口到宿主主机（端口映射），即docker run创建容器时候通过 -p 或 -P 参数来启用，访问容器的时候就通过[宿主机IP]:[容器端口]访问容器。