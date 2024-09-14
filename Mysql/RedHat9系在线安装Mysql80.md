#### 在线安装

1. 安装mysql.repo源

   ```bash
   rpm -i https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm
   ```

2. 查看安装mysql源是否成功

   ```bash
   ll /etc/yum.repos.d/
   # 结果
   -rw-r--r--. 1 root root  951  7月  6  2022 mysql-community-debuginfo.repo
   -rw-r--r--. 1 root root  827  7月  6  2022 mysql-community.repo
   -rw-r--r--. 1 root root  871  7月  6  2022 mysql-community-source.repo
   ```

3. 安装mysql-server程序

   ```bash
   dnf install mysql-server
   # 结果
   已安装:
     mysql-community-client-8.0.39-1.el9.x86_64   mysql-community-client-plugins-8.0.39-1.el9.x86_64
     mysql-community-common-8.0.39-1.el9.x86_64   mysql-community-icu-data-files-8.0.39-1.el9.x86_64
     mysql-community-libs-8.0.39-1.el9.x86_64     mysql-community-server-8.0.39-1.el9.x86_64
     net-tools-2.0-0.62.20160912git.el9.x86_64    perl-AutoLoader-5.74-481.el9.noarch
     perl-B-1.80-481.el9.x86_64                   perl-Carp-1.50-460.el9.noarch
     perl-Class-Struct-0.66-481.el9.noarch        perl-Data-Dumper-2.174-462.el9.x86_64
     perl-Digest-1.19-4.el9.noarch                perl-Digest-MD5-2.58-4.el9.x86_64
     perl-Encode-4:3.08-462.el9.x86_64            perl-Errno-1.30-481.el9.x86_64
     perl-Exporter-5.74-461.el9.noarch            perl-Fcntl-1.13-481.el9.x86_64
     perl-File-Basename-2.85-481.el9.noarch       perl-File-Path-2.18-4.el9.noarch
     perl-File-Temp-1:0.231.100-4.el9.noarch      perl-File-stat-1.09-481.el9.noarch
     perl-FileHandle-2.03-481.el9.noarch          perl-Getopt-Long-1:2.52-4.el9.noarch
     perl-Getopt-Std-1.12-481.el9.noarch          perl-HTTP-Tiny-0.076-462.el9.noarch
     perl-IO-1.43-481.el9.x86_64                  perl-IO-Socket-IP-0.41-5.el9.noarch
     perl-IO-Socket-SSL-2.073-1.el9.noarch        perl-IPC-Open3-1.21-481.el9.noarch
     perl-MIME-Base64-3.16-4.el9.x86_64           perl-Mozilla-CA-20200520-6.el9.noarch
     perl-NDBM_File-1.15-481.el9.x86_64           perl-Net-SSLeay-1.92-2.el9.x86_64
     perl-POSIX-1.94-481.el9.x86_64               perl-PathTools-3.78-461.el9.x86_64
     perl-Pod-Escapes-1:1.07-460.el9.noarch       perl-Pod-Perldoc-3.28.01-461.el9.noarch
     perl-Pod-Simple-1:3.42-4.el9.noarch          perl-Pod-Usage-4:2.01-4.el9.noarch
     perl-Scalar-List-Utils-4:1.56-461.el9.x86_64 perl-SelectSaver-1.02-481.el9.noarch
     perl-Socket-4:2.031-4.el9.x86_64             perl-Storable-1:3.21-460.el9.x86_64
     perl-Symbol-1.08-481.el9.noarch              perl-Term-ANSIColor-5.01-461.el9.noarch
     perl-Term-Cap-1.17-460.el9.noarch            perl-Text-ParseWords-3.30-460.el9.noarch
     perl-Text-Tabs+Wrap-2013.0523-460.el9.noarch perl-Time-Local-2:1.300-7.el9.noarch
     perl-URI-5.09-3.el9.noarch                   perl-base-2.27-481.el9.noarch
     perl-constant-1.33-461.el9.noarch            perl-if-0.60.800-481.el9.noarch
     perl-interpreter-4:5.32.1-481.el9.x86_64     perl-libnet-3.13-4.el9.noarch
     perl-libs-4:5.32.1-481.el9.x86_64            perl-mro-1.23-481.el9.x86_64
     perl-overload-1.31-481.el9.noarch            perl-overloading-0.02-481.el9.noarch
     perl-parent-1:0.238-460.el9.noarch           perl-podlators-1:4.14-460.el9.noarch
     perl-subs-1.03-481.el9.noarch                perl-vars-1.05-481.el9.noarch
   ```

4. 查看安装结果，安装完成

   ```bash
   rpm -qa | grep mysql
   # 结果
   mysql80-community-release-el9-1.noarch
   mysql-community-common-8.0.39-1.el9.x86_64
   mysql-community-client-plugins-8.0.39-1.el9.x86_64
   mysql-community-libs-8.0.39-1.el9.x86_64
   mysql-community-client-8.0.39-1.el9.x86_64
   mysql-community-icu-data-files-8.0.39-1.el9.x86_64
   mysql-community-server-8.0.39-1.el9.x86_64
   ```

5. 修改mysql启动配置文件(不同版本路径不同，/etc/my.cnf)

   ```bash
   [mysqld]
   #数据路径
   datadir=/var/lib/mysql
   socket=/var/lib/mysql/mysql.sock
   
   log-error=/var/log/mysqld.log
   pid-file=/var/run/mysqld/mysqld.pid
   
   default_authentication_plugin=mysql_native_password
   default-time-zone='+08:00'
   max_connections=300 
   character-set-server=utf8mb4
   collation-server=utf8mb4_general_ci
   #表名存储在磁盘是小写的，但是比较的时候是不区分大小写
   lower_case_table_names=1
   port=3306
   
   # 设置密码规则
   validate_password.policy = LOW
   validate_password.length = 1
   validate_password.number_count = 0
   validate_password.special_char_count = 0
   validate_password.mixed_case_count = 0
   validate_password.check_user_name = OFF
   
   ```

6. 启动mysql服务

   ```bash
   systemctl start mysqld
   ```

7. 查找mysql的初始密码 （/var/log/mysqld.log  TXYpe+O#i4d2）

   ```bash
   2024-09-12T02:39:36.952390Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.39) initializing of server in progress as process 3953
   2024-09-12T02:39:36.965325Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
   2024-09-12T02:39:37.459881Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
   2024-09-12T02:39:43.426092Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: TXYpe+O#i4d2
   2024-09-12T02:39:43.690356Z 6 [Warning] [MY-013360] [Server] Plugin mysql_native_password reported: ''mysql_native_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
   ```

8. 进入mysql并修改密码

   ```bash
   mysql -uroot -p
   
   #如果配置文件中没有配置低密码检查，则不能使用root作为密码
   mysql>ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
   ```

9. 修改mysql远程连接

   ```bash
   mysql> update user set host='%' where user ='root';
   
   #如果是创建新的用户
   # 创建用户
   mysql> create user 'gogetter'@'%' identified by 'P@ssW0Rd';
   mysql> grant all privileges on *.* to 'gogetter'@'%' with grant option;
   mysql> FLUSH PRIVILEGES;
   ```

#### 遇到的问题

1. 安装mysql程序失败，GPG校验失败

   + 问题展示
     
     ```bash
     MySQL 8.0 Community Server                                        3.0 MB/s | 3.1 kB     00:00
       file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022 的 GPG 公钥(0x3A79BD29)已安装
       仓库 "MySQL 8.0 Community Server" 的 GPG 公钥已安装，但是不适用于此软件包。
       请检查此仓库的公钥 URL 是否配置正确。. 失败的软件包是：mysql-community-client-8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       mysql-community-client-plugins-8.0.39-1.el9.x86_64.rpm 的公钥没有安装. 失败的软件包是：mysql-commu                                                             nity-client-plugins-8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       mysql-community-common-8.0.39-1.el9.x86_64.rpm 的公钥没有安装. 失败的软件包是：mysql-community-com                                                             mon-8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       mysql-community-icu-data-files-8.0.39-1.el9.x86_64.rpm 的公钥没有安装. 失败的软件包是：mysql-commu                                                             nity-icu-data-files-8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       mysql-community-libs-8.0.39-1.el9.x86_64.rpm 的公钥没有安装. 失败的软件包是：mysql-community-libs-                                                             8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       mysql-community-server-8.0.39-1.el9.x86_64.rpm 的公钥没有安装. 失败的软件包是：mysql-community-ser                                                             ver-8.0.39-1.el9.x86_64
        GPG密钥配置为：file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       下载的软件包保存在缓存中，直到下次成功执行事务。
       您可以通过执行 'yum clean packages' 删除软件包缓存。
       错误：GPG 检查失败
     ```

   + [Index of /232905 (mysql.com)](https://repo.mysql.com/) 查看最新MYSQL GPG校验,并安装
     
     ```bash
     sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
     ```

   + 执行dnf clean packages ,清理之前安装失败的包和缓存
     
     ```bash
     dnf clean packages
     ```

   + 修改mysql.repo ，指向新的GPG校验
     
     ```bash
     vi /etc/yum.repos.d/mysql-community.repo
     
     
     [name=MySQL 8.0 Community Server]
       baseurl=http://repo.mysql.com/yum/mysql-8.0-community/el/9/$basearch/
       enabled=1
       gpgcheck=1
       gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023
     
      [mysql-connectors-community]
       name=MySQL Connectors Community
       baseurl=http://repo.mysql.com/yum/mysql-connectors-community/el/9/$basearch/
       enabled=1
       gpgcheck=1
       gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023
     
      [mysql-tools-community]
       name=MySQL Tools Community
       baseurl=http://repo.mysql.com/yum/mysql-tools-community/el/9/$basearch/
       enabled=1
       gpgcheck=1
       gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023
     
      [mysql-cluster-8.0-community]
       name=MySQL Cluster 8.0 Community
       baseurl=http://repo.mysql.com/yum/mysql-cluster-8.0-community/el/9/$basearch/
       enabled=0
       gpgcheck=1
       gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023
     ```

   + 重新安装
     
     ```bash
     dnf install mysql-server
     ```
     

2. 启动mysql服务失败，没有权限

   ```
   rm -rf /var/lib/mysql
   mkdir /var/lib/mysql
   chown -R mysql:mysql /var/lib/mysql
   chmod -R 755 /var/lib/mysql
   ```

3. 启动失败，可到配置文件中的错误日志路径查看详细情况
