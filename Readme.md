
# openvp网对网

## 服务端配置

### 简单配置，使用openvpn-install.sh

[git地址](https://github.com/angristan/openvpn-install)

```bash
bash ./openvpn-install
# 回车先装上，后面改
```

### 关闭默认安装后的openvpn


```bash 
# 获取openvpn运行程序ID
ps -aux|grep openvpn  
kill <openvpn的ID>
```
### 修改服务端的配置文件

```bash
# 客户端的IP转发要开启
systcl -p # 验证一下

# 启用配置文件中的, ccd是当前配置文件同级目录下的文件夹名称，可以说绝对地址
client-config-dir ccd
route xxx.xxx.xxx.xxx 255.255.255.0  # 此客户端子网的网段与掩码，多个客户端就写多个route

push "route xxx.xxx.xxx.xxx 255.255.255.0"  # 告知客户端，服务端子网的网段与掩码
```

#### 在ccd文件中，新建一个与客户端名称一致的文件,多个客户端就建多个文件
```bash
iroute xxx.xxx.xxx.xxx 255.255.255.0  # 此客户端子网的网段与掩码
```

### 开启服务，测试服务端与客户端的点对点联通情况
```bash
openvpn --config server.conf  # 服务端启动
```

```bash
# 把生成的客户端配置文件发送至客户端
openvpn --config client.conf  # 客户端启动
```

```bash
# 客户端与服务端互相ping测试
# 1、在虚拟局域网中（10.8.0.0）
# 2、客户机ping服务端在其子网的IP,服务机ping客户机在其子网的IP
```

### 扩展子网
> 假设:
> 服务端:虚拟局域网中IP<10.8.0.1>,在其子网中IP<192.168.1.128>
> 客户端:虚拟局域网中IP<10.8.0.2>,在其子网中IP<192.168.2.155>

> 如果可以操作子网的路由设备，就不要用这个办法扩展了，这个办法麻烦

```bash
# 在服务端的子网中,假设有台机器[linux]IP<192.168.1.129>，想要加入虚拟局域网
# 在此机器上添加路由信息
route add -net 192.168.2.0/24 gw 192.168.1.128  # 将服务端作为前往客户端子网的网关
route add -net 10.8.0.0/24 gw 192.168.1.128  # 将服务端作为前往虚拟局域网的网关，这个有时候不加也能行，很奇怪，没理解，我还试过在客户端或服务端使用iptables的nat表中添加转发规则，好像也可以，但我不确定

# 客户端同理，假设有台机器[linux]IP<192.168.2.156>，想要加入虚拟局域网
route add -net 192.168.1.0/24 gw 192.168.2.155  # 将客户端作为前往客户端子网的网关
route add -net 10.8.0.0/24 gw 192.168.2.155  # 将服务端作为前往虚拟局域网的网关

# 这个方法的缺点很明显，每个想加入虚拟局域网的机器都要加这么两条路由，麻烦的很，如果是windows,我没真正意义的测试过，windows添加路由的命令与linux不同
route ADD <目标网段> MASK <目标网段掩码> <网关>
```

### 互相ping一下
在这个局域网中，只有客户端与服务端拥有虚拟局域网的IP,其余机器均使用自己所属子网的IP,所以服务端与客户端所属的子网网段是不能相同的






