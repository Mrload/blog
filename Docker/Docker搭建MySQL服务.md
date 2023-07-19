+ 首先需要建立要挂载到容器的文件夹，如果需要又权限调整配置，必须提前建好，否则属主是root

```bash
mkdir mysqlData && cd mysqlData
mkdir conf log data
```

+ 运行

```bash
docker run \
-d  \
--name mysql  \
--privileged   \
--env MYSQL_ROOT_PASSWORD=yunzhisql  \
-p 3306:3306/tcp  \
-v /data/users/yuncoder03/mysql/log:/var/log/mysql  \
-v /data/users/yuncoder03/mysql/data:/var/lib/mysql  \
-v /data/users/yuncoder03/mysql/conf:/etc/mysql/conf.d  \
mysql:5.7
```

+ 修改配置

```bash
# 在conf文件夹下新增 xxx.cnf 文件
# 重启容器
# mysql
```

```bash
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf

容器内查看 /etc/my.conf

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

