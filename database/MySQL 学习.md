# MySQL 学习

## 安装

在Ubuntu环境下，安装MySQL服务器：

```shell
sudo apt-get update
sudo apt-get install mysql-server
```

若需要允许远程连接：

```shell
sudo ufw allow mysql
```

启动MySQL服务：

```shell
systemctl start mysql
```

机器重启时，自动启动MySQL服务：

```shell
systemctl enable mysql
```

## 使用

#### 登录MySQL：

```shell
mysql -u <username> -p
```

然后根据提示输入密码

#### 用户管理

创建用户：

```shell
create user <username>@'<hostname>' identified by '<password>';
```

如果需要允许任意IP登陆，传入 % 给hostname；


授予用户权限：

```shell
grant create,insert,update on <db_name>.* to '<username>'@'<hostname>';
```

查看所有的用户：

```shell
select Host, User from mysql.user;
```

创建数据库：

```shell
create database <db_name>;
```

