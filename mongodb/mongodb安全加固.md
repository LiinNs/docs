# mongodb 安全加固

## 启用安全访问

1. 创建用户

    ```mongo
    use admin
    # 设置密码
    db.createUser({
      user: 'userAdmin',
      pwd: 'xxx',
      roles:[{
        role: 'userAdminAnyDatabase',
        db: 'admin'
      }]
    })
    ```

2. 启用 `mongo --auth`
3. 修改配置文件，然后重启服务

    ```conf
    security:
      authorization: enabled
    ```

## 访问权限控制

MongoDB可以设置的权限字符串

- 数据库用户角色：read、readWrite；
- 数据库管理角色：dbAdmin、dbOwner、userAdmin;
- 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManage；
- 备份恢复角色：backup、restore；
- 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
- 超级用户角色：root
- 内部角色：__system

- Read：允许用户读取指定数据库
- readWrite：允许用户读写指定数据库
- dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
- userAdmin：允许用户向system.users集合写入，可以在指定数据库里创建、删除和管理用户
- clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
- readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
- readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
- userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
- dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
- root：只在admin数据库中可用。超级账号，超级权限

## 绑定IP 修改端口号

```conf
net:
  port: 27017
  bindIp: 0.0.0.0
```

## 用户管理

- 查看用户 `show users` `db.system.users.find()` `db.runCommand({usersInfo: "username"})`
- 修改密码 `use admin db.changeUserPassword("username", "xxx")`
- 修改密码和用户信息

    ```mongodb
    db.updateUser('username', {'pwd': ''})

    db.runCommand(
        {
            updateUser:"username",
            pwd:"xxx",
            customData:{title:"xxx"}
        }
    )
    ```

- 删除用户数据 `use admin db.dropUser('username')`

[1](https://blog.csdn.net/fofabu2/article/details/78983741)
[2](https://blog.fundebug.com/2019/01/21/how-to-protect-mongodb/)
