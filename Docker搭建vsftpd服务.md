> ftp 是File Transfer Protocol的缩写，文件传输协议，用于在网络上进行文件传输的一套标准协议，使用客户/服务器模式。它属于网络传输协议的应用层。

> sftp 是SSH File Transfer Protocol的缩写，安全文件传输协议；

> vsftp 是一个基于GPL发布的类Unix系统上使用的ftp服务器软件，它的全称是Very Secure FTP从此名称可以看出来，编制者的初衷是代码的安全；

> vsftpd 是very secure FTP daemon的缩写，安全性是它的一个最大的特点。vsftpd 是一个 UNIX 类操作系统上运行的服务器的名字，它可以运行在诸如 Linux、BSD、Solaris、 HP-UNIX等系统上面，是一个完全免费的、开放源代码的ftp服务器软件



## Docker搭建
[GitHub地址](https://github.com/fauria/docker-vsftpd)
### 镜像名称 fauria/vsftpd
```bash
docker pull fauria/vsftpd
```
### 启动
```bash
docker run -d \
-v /my/data/directory:/home/vsftpd \    # 挂载宿主机目录到容器家目录
-p 20:20 -p 21:21 -p 21100-21110:21100-21110 \  # 端口20、21、通信端口
-e FTP_USER=myuser \  # 环境变量用户名，不指定则为admin
-e FTP_PASS=mypass \  # 不指定就去日志里看
-e PASV_ADDRESS=127.0.0.1 \  # 启动服务的IP
-e PASV_MIN_PORT=21100 \  # 通信？
-e PASV_MAX_PORT=21110 \
--name vsftpd --restart=always fauria/vsftpd
```
### 自定义增加新用户

```bash
docker exec -i -t vsftpd bash  # 进入容器
mkdir /home/vsftpd/myuser  # 手动建立用户家目录
echo -e "myuser\nmypass" >> /etc/vsftpd/virtual_users.txt  # 加入用户信息 
/usr/bin/db_load -T -t hash -f /etc/vsftpd/virtual_users.txt /etc/vsftpd/virtual_users.db  # 二进制存储用户信息
exit
docker restart vsftpd  # 重启容器
```
**这里有坑**
+ 有时候，加了用户，重启容器后，配置又回到了原始的配置，结果用户没加上
+ 有人提过这个问题，有些人改了启动脚本
+ 有些人建议把用户信息的配置项挂载到宿主机上，这个我没试过。
