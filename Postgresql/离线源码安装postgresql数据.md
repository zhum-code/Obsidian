### 离线源码安装postgresql数据库

一、 编译源码

```bash
#建议自己创建一台与正式服环境相同，架构相同的虚拟机，编译源码后将编译程序复制过去
https://ftp.postgresql.org/pub/source下载源码
tar -zxvf 解压压缩包
# 如果需要安装uuid扩展，那么需要以下步骤
#  yum -y install e2fsprogs-devel uuid uuid-devel libuuid-devel
# 如果yum找不到uuid-devel 那么可以去本机镜像或者阿里镜像对应版本查找安装包
#
./configure --prefix=/app/pgsql --with-uuid=ossp(不需要uuid可不带with)
make && make install
#需要uuid 还需以下步骤 安装uuid-ossp 扩展
cd contrib/uuid-ossp/
make && make install


```

二、配置环境变量

```bash
vim /etc/profile
#复制到最下面 位置自定义
PG_LIBRARY_PATH=/app/pgsql/lib
PATH=/app/pgsql/bin:$PATH
MANPATH=/app/pgsql/man:$MANPATH
export PATH PG_LIBRARY_PATH MANPATH
# 刷新环境变量
source /etc/profile
```

三、配置用户

```bash
groupadd postgres
useradd postgres -g postgres
chown -R postgres:postgres /app/pgsql
```

四、初始化数据库

```bash
su - postgres
initdb -D /app/pgsql/data
```

五、配置远程访问

```bash
1. 修改/data/postgresql.conf
#listen_addresses = 'localhost'
修改为
listen_addresses = '*'

#此文件可修改连接数和共享内存大小
max_connections = 
shared_buffers =

2. 修改/data/pg_hba.conf
添加host	replication	all		0.0.0.0/0		md5

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
#host    all             all             127.0.0.1/32            trust
host	all		all		0.0.0.0/0		md5
# IPv6 local connections:
host    all             all             ::1/128                 trust
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
host	replication	all		0.0.0.0/0		md5
```

六、 测试

```bash
su postgres
pg_ctl -D /app/pgsql/data start
pg_ctl -D /app/pgsql/data stop
/app/pgsql/bin/psql -h localhost -p 5432
#设置密码是为了远程访问
ALTER user postgres WITH PASSWORD 'postgres'
SELECT pg_reload_conf();
```

七、可能遇到的问题

```bash
1. 使用命令缺少 libpq.so.5 库
将程序文件夹的lib路径的libpq.so.5直接复制到/usr/lib64下
2. 使用命令缺少 libossp-uuid.so.16
将安装uuid依赖的服务器的/usr/lib64下的
libossp-uuid.so.16和libossp-uuid.so.16.* 复制到目标服务器的/usr/lib64下
3. libc.so.6版本问题 /lib64/libc.so.6:version 'GLIBC_XXX' not found

```

八、配置主从复制

`1`.主数据库

```bash
# 添加主从复制的专用用户
psql -c "CREATE ROLE replicator login replication encrypted password 'postgres';"
# 配置从库的访问ip和用户
vim pgsql/data/pg_hba.conf
...
#在最下面添加
host    replication    replicator     从库ip/24         md5
...
```

2.从数据库

```bash
# 从库只需要最基本的安装程序，配置和数据可以从主库复制
# 使用命令从主库拉取 可能会报缺少库的错误，看第七步
pg_basebackup -h 主库ip -D /usr/local/pgsql/data -U replicator -P -v -R -X stream -C -S pgstandby1
# 配置data路径所有权给postgres用户
chown -R postgres:postgres /usr/local/pgsql
# 配置data路径权限，750即可，不能是777
chmod -R 750 /usr/local/pgsql/data
#从数据库可以启动了
```

