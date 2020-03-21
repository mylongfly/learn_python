# Mongodb安装配置

## 一、下载

下载zip版解压到想要安装的路径

## 二、添加环境变量

将bin添加到系统环境变量

### 三、创建数据库目录

在你想要存放数据的地方，新建一个文件夹，如`db`，我们推荐的数据库目录设置是：

```
data/	
        conf	-->配置文件目录		
        mongod.conf		-->配置文件	
        db		-->数据库目录	
        log		-->日志文件目录		mongodb.log		-->日志记录文件
```

## 四、启动数据库方法

打开cmd输入：

mongod --path=D:\mongo\data\db

## 五、将Mongodb服务器作为windows系统服务运行

### 配置文件mongo.conf详解：

```txt
# 端口号，默认27017
#port=27017
 
# 数据库文件位置
dbpath=/data/db
 
#日志文件位置
logpath=/log/mongo.log
 
# 是否追加方式写入日志，默认True
logappend=true
 
# 设置绑定ip
bind_ip = 127.0.0.1
# 设置端口
port = 27017
 
# 是否以守护进程方式运行，默认false
fork = true
 
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=false
 
# 启用日志文件，默认启用
journal=true
 
# 启用定期记录CPU利用率和 I/O 等待,默认false
#cpu = true
 
# 是否以安全认证方式运行，默认是不认证的非安全方式
#noauth = true
#auth = true
 
# 详细记录输出，默认false
#verbose = true
 
#用于开发驱动程序时验证客户端请求
#objcheck = true
 
# # 启用数据库配额管理,默认false
#quota = true
 
# 设置oplog日志记录等级，默认0
#   0=off (default)
#   1=W
#   2=R
#   3=both
#   7=W+some reads
#oplog = 0
 
# 是否打开动态调试项，默认false
#nocursors = true
 
# 忽略查询提示，默认false
#nohints = true
 
# 禁用http界面，默认为localhost：28017
#nohttpinterface = true
 
# 关闭服务器端脚本，这将极大的限制功能，默认false
#noscripting = true
 
# 关闭扫描表，任何查询将会是扫描失败
#notablescan = true
 
# 关闭数据文件预分配
#noprealloc = true
 
# 为新数据库指定.ns文件的大小，单位:MB
# nssize = <size>
 
# 用于Mongo监控服务器的Accout token。
#mms-token = <token>
 
# Mongo监控服务器的服务器名称。
#mms-name = <server-name>
 
# Mongo监控服务器的Ping间隔时间，即心跳
#mms-interval = <seconds>
 
# Replication Options
 
# 设置主从复制参数
#slave = true # 设置从节点
#source = master.example.com # 指定从节点的主节点
# Slave only: 指定要复制的单个数据库
#only = master.example.com
# or
#master = true # 设置主节点
#source = slave.example.com 
 
# 设置副本集的名字，所有的实例指定相同的名字属于一个副本集
replSet = name
 
#pairwith = <server:port>
 
# 仲裁服务器地址
#arbiter = <server:port>
 
# 默认为false，用于从实例设置。是否自动重新同步
#autoresync = true
 
# 指定的复制操作日志（OPLOG）的最大大小
#oplogSize = <MB>
 
# 限制复制操作的内存使用
#opIdMem = <bytes>
 
# 设置ssl认证
# Enable SSL on normal ports
#sslOnNormalPorts = true
 
# SSL Key file and password
#sslPEMKeyFile = /etc/ssl/mongodb.pem
#sslPEMKeyPassword = pass
 
# path to a key file storing authentication info for connections
# between replica set members
#指定存储身份验证信息的密钥文件的路径
#keyFile=/path/to/keyfile
```

### MongoDB集群基本配置：

```txt
# 集群配置文件
dbpath=/var/lib/mongodb
logpath=/var/log/mongodb/mongodb.log
pidfilepath=/var/log/mongodb/master.pid
directoryperdb=true
logappend=true
replSet=name
bind_ip=127.0.0.1
port=27017
fork=true
noprealloc=true
```

### 以配置文件安装服务：

 mongod --config "D:\mongodb\mongo.conf" --install --serviceName "mongodb" --serviceDisplayName "mongodb"

注意 : 要使用管理员权限来执行
如果需要 , 您可以安装 `mongod.exe` 的多个实例的服务 , 只需要通过使用 `--serviceName` 和 `--serviceDisplayName` 指定不同的服务实例名

启动 MongoDB 服务 :

```cmd
net start mongodb
```

关闭 MongoDB 服务 :

```cmd
net stop mongodb
```

移除 MongoDB 服务 :

```cmd
D:\Program\MongoDB\bin\mongod.exe --remove
```

## 六、配置账号和密码

MongoDB 默认安装完成以后 , 只允许本地连接 , 同时不需要使用任何账号密码就可以直接连接 MongoDB (27017) , 且没有认证 , 这样做的好处是 , 用户可以即时上手 , 不用担心被一堆配置弄的心烦意乱 , 然而坏处也是显而易见

默认情况下 , MongoDB 也是没有管理员账户的 , 因此除非你在 `admin` 数据库中使用 `db.createUser()` 命令添加了管理员帐号 , 且使用 `--auth` 参数启动 MongoDB , 否则在数据库中任何人都可以无需认证执行所有命令 , 包括 `delete` 和 `shutdown` , 这样就容易被黑 , 所以我们需要给Mongo设置启用认证 , 启动服务时启用认证

MongoDB 的用户分为两种 , 一种是 `admin` 用户 , 另一种是特定数据库用户 , `admin` 用户拥有最高的权限 , 而特定数据库用户则只能访问特定的数据库 ; 当 MongoDB 的 `admin` 库里没有任何用户的时候 , 也就是说整个 MongoDB 没有一个户的时候 , 即便 `--auth` 权限需求打开了 , 用户还是可以通过 `localhost` 界面进入 MongoDB 进行用户设置 , 否则的话整个 MongoDB 就完全没法访问了 ; 而当这个用户创建完成之后 , 之后的用户登录和操作就需要授权了 , 不是直接登录就能使用的了

MongoDB 登录授权也必须在 `admin` 数据库下进行 `use admin` , 而不能在其他数据库下进行

1. 启动服务时开启认证

   ```
   D:\Program\MongoDB\bin\mongod --config D:\Program\MongoDB\mongod.cfg --auth
   ```

2. 连接到数据库

   ```
   D:\Program\MongoDB\bin\mongo.exe
   ```

3. 切换到 admin 数据库创建管理员用户

   ```
   > use admin
   switched to db admin
   ```

   创建管理员用户 (创建用户前必须切换到指定的数据库)

   ```
   > db.createUser({user:"admin",pwd:"12345678",roles:["root"]})
   Successfully added user: { "user" : "admin", "roles" : [ "root" ] }
   ```

4. 认证登录

   ```
   > db.auth("admin", "12345678")
   1
   ```

   > 如果返回 '1'表示验证成功， 如果是 '0' 表示验证失败

5. 显示当前系统用户

   ```
   > db.system.users.find()
   ```

6. 删除用户 (必须切换到用户所在的数据库去删除)

   ```
   db.dropUser("flowpp")
   ```

## 七、MongoDB Role 类型

数据库用户角色 (Database User Roles) :

```
read : 授予 User 只读数据的权限
readWrite : 授予 User 读写数据的权限
```

数据库管理角色 (Database Administration Roles) :

```
dbAdmin : 在当前 DB 中执行管理操作
dbOwner : 在当前 DB 中执行任意操作
userAdmin : 在当前 DB 中管理 User
```

备份和还原角色 (Backup and Restoration Roles) :

```
backup
restore
```

跨库角色 (All-Database Roles) :

```
readAnyDatabase : 授予在所有数据库上读取数据的权限
readWriteAnyDatabase : 授予在所有数据库上读写数据的权限
userAdminAnyDatabase : 授予在所有数据库上管理 User 的权限
dbAdminAnyDatabase : 授予管理所有数据库的权限
```

集群管理角色 (Cluster Administration Roles) :

```
clusterAdmin : 授予管理集群的最高权限
clusterManager : 授予管理和监控集群的权限
clusterMonitor : 授予监控集群的权限 , 对监控工具具有 readonly 的权限
hostManager : 管理 Server
```