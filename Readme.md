
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
```

#### 在ccd文件中，新建一个与客户端名称一致的文件
```bash
iroute xxx.xxx.xxx.xxx 255.255.255.0  # 此客户端子网的网段与掩码
```

