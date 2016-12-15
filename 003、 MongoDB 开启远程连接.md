### 003、 MongoDB 开启远程连接

##### 版本

v.3.4.0

#### 添加新用户

##### 新版

> db.createUser({user:'root',pwd:'root',roles:['userAdminAnyDatabase']});

##### 旧版

> db.addUser('root','root');

#### 配置 MongoDB Conf

> vi /etc/mongod.conf
>
> bind_ip = 127.0.0.1,10.9.1.36

#### 重启 MongoDB

> sudo service mongod restart

#### 防火墙开启27017端口

> iptables -A INPUT -p tcp --dport 27017 -j ACCEPT

#### 本地连接

使用 `MongoBooster` -> `Connections` -> `Create`  填上对应的连接 `10.9.1.36`  -> `test`



连接成功！