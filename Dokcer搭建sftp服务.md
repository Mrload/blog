# 镜像

**atmoz/sftp**

[git地址](https://github.com/atmoz/sftp)

```bash
docker pull  atmoz/sftp
```

拉不下来用魔法,然后迁移过去
```bash
docker save xximage > xx.tar
scp xxx xxx
docker load < xx.tar
```

# 启动

```bash
docker run \
    -v <host-dir>/users.conf:/etc/sftp/users.conf:ro \  # 只读挂载用户信息
    -v mySftpVolume:/home \  # 挂载用户家目录 
    -p 2222:22 -d atmoz/sftp 
```
```bash
# users.conf 格式
用户:密码:用户ID:组ID
foo:123:1001:100
bar:abc:1002:100
baz:xyz:1003:100
```

# 坑

用户进入家目录后没有权限

对于用户，`宿主机/用户名` 这个文件其实是他的根目录，根目录是不能写入与编辑的
在 `宿主机/用户名` 这个文件夹下建一个二级子目录或者多个都行，权限改成777， 用户可在这个文件下操作。
试过把挂载文件改成777，没用的
