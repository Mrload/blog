```bash
# 【可选】定义openvpn监听的IP地址，如果是服务器单网卡的也可以不注明，但是服务器是多网卡的建议注明。
local 172.24.28.233  # 服务端的内网IP

# tcp/udp端口，防火墙打开此端口
port 1194

# 定义openvpn使用的协议，默认使用UDP。如果是生产环境的话，建议使用TCP协议，网上更多建议udp
# proto tcp
proto udp

# 定义openvpn运行时使用哪一种模式，openvpn有两种运行模式一种是tap模式，一种是tun模式。
    # tap模式也就是桥接模式，通过软件在系统中模拟出一个tap设备，该设备是一个二层设备，同时支持链路层协议。
    # tun模式也就是路由模式，通过软件在系统中模拟出一个tun路由，tun是ip层的点对点协议。
# dev tap 
dev tun

# 定义openvpn使用的CA证书文件，该文件通过build-ca命令生成，CA证书主要用于验证客户证书的合法性。
ca ca.crt

# 定义openvpn服务器端使用的证书文件。
cert server.crt

# 定义openvpn服务器端使用的秘钥文件，该文件必须严格控制其安全性。
key server.key

# 定义Diffie hellman文件。
dh dh.pem

# 防DDOS攻击，openvpn控制通道的tls握手进行保护，服务器端0,客户端1
tls-auth ta.key 0
key-direction 0

# 选择一个密码加密算法。
# 该配置项也必须复制到每个客户端配置文件中。 
# 添加SHA256算法
cipher AES-256-CBC
auth SHA256

# 定义openvpn在使用tun路由模式时，分配给client端分配的IP地址段。不能和VPN服务器内网网段有相同。
server 10.8.0.0 255.255.255.0  # tun模式的VPN网段

# 定义客户端和虚拟ip地址之间的关系。特别是在openvpn重启时,再次连接的客户端将依然被分配和断开之前的IP地址。
ifconfig-pool-persist ipp.txt

# 定义openvpn在使用tap桥接模式时，分配给客户端的IP地址段。
;server-bridge 10.8.0.4 255.255.255.0 10.8.0.50 10.8.0.100

# 向客户端推送的路由信息，假如客户端的IP地址为10.8.0.2，要访问192.168.10.0网段的话，使用这条命令就可以了。如果有网段的话，可以多次出现push route关键字。同时还要配合iptables一起使用。以允许客户端能够连接到服务器背后的其他私有子网。
push "route 172.24.28.0 255.255.255.0"  # 服务端的内网网段，如果不添加，只能通过VPN的网段进行通信

# 如果启用该指令，所有客户端的默认网关都将重定向到VPN，这将导致诸如web浏览器、DNS查询等所有客户端流量都经过VPN。
push "redirect-gateway def1"  # 重定向网关
;push "redirect-gateway def1 bypass-dhcp"  # 还有给这个配置的


# 指定客户端IP地址。
;client-config-dir ccd
# 使用方法是在server.conf同级目录下创建ccd目录，然后创建在ccd目录下创建以客户端命名的文件。比如要设置客户端 ilanni为10.8.0.100这个IP地址，只要在 /etc/openvpn/ccd/ilanni文件中包含如下行即可:
ifconfig-push 10.8.0.100 255.255.255.0
iroute 172.16.0.0 255.255.255.0  # 客户端声明自己的网段是172.16.0.0


# 重定向客户端的网关，在进行翻墙时会使用到。没理解使用场景
push “redirect-gateway def1 bypass-dhcp”

# 向客户端推送的DNS信息。
;push “dhcp-option DNS 208.67.222.222”


# 使客户端之间能相互访问，默认设置下客户端间是不能相互访问的。
client-to-client

# 定义openvpn一个证书在同一时刻是否允许多个客户端接入，默认没有启用。
duplicate-cn

# 定义活动连接保时期限，10秒超时，120秒断开
keepalive 10 120  

# 启用允许数据压缩，客户端配置文件也需要有这项。
comp-lzo

# 定义最大客户端并发连接数量
;max-clients 100

# 定义openvpn运行时使用的用户及用户组。在完成初始化工作之后，降低OpenVPN守护进程的权限。该指令仅限于非Windows系统中使用。
;user nobody
;group nogroup

# 如果协议改成了TCP，这里数值要改成0
;explicit-exit-notify 1

# 通过keepalive检测超时后，重新启动VPN，不重新读取keys，保留第一次使用的keys。
persist-key

# 通过keepalive检测超时后，重新启动VPN，一直保持tun或者tap设备是linkup的。否则网络连接，会先linkdown然后再linkup。
persist-tun

# 把openvpn的一些状态信息写到文件中，比如客户端获得的IP地址。
status openvpn-status.log

# 记录日志，每次重新启动openvpn后删除原有的log信息。也可以自定义log的位置。默认是在/etc/openvpn/目录下。
log openvpn.log

# 记录日志，每次重新启动openvpn后追加原有的log信息。
;log-append openvpn.log

# 设置日志记录冗长级别。有些教程说这是openvpn版本
verb 3

# 重复日志记录限额
;mute 20
```
