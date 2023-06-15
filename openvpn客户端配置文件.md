```bash
# 定义这是一个client，配置从server端pull拉取过来，如IP地址，路由信息之类，Server使用push指令推送过来
client

# 定义openvpn运行的模式，这个地方需要严格和Server端保持一致。
dev tun

# 定义openvpn运行的模式，这个地方需要严格和Server端保持一致。
proto tcp

# 设置Server的IP地址和端口，这个地方需要严格和Server端保持一致。
# 如果有多台机器做负载均衡，可以多次出现remote关键字。
remote 192.168.1.8 1194


# 随机选择一个Server连接，否则按照顺序从上到下依次连接。该选项默认不启用。
;remote-random


# 始终重新解析Server的IP地址（如果remote后面跟的是域名），保证Server IP地址是动态的使用DDNS动态更新DNS后，Client在自动重新连接时重新解析Server的IP地址。这样无需人为重新启动，即可重新接入VPN。
resolv-retry infinite


# 定义在本机不邦定任何端口监听incoming数据。
nobind

persist-key

persist-tun

# 定义CA证书的文件名，用于验证Server CA证书合法性，该文件一定要与服务器端ca.crt是同一个文件。
ca ca.crt

# 定义客户端的证书文件。
cert laptop.crt

# 定义客户端的密钥文件。
key laptop.key

# Server使用build-key-server脚本生成的，在x509 v3扩展中加入了ns-cert-type选项。防止client使用他们的keys ＋ DNS hack欺骗vpn client连接他们假冒的VPN Server，因为他们的CA里没有这个扩展。
ns-cert-type server

# 启用允许数据压缩，这个地方需要严格和Server端保持一致。
comp-lzo

# 启用允许数据压缩，这个地方需要严格和Server端保持一致。
verb 3
```
