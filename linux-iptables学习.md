+ 四表五链

| 表     | 链                                              |
| ------ | ----------------------------------------------- |
| filter | INPUT、FORWARD、OUTPUT                          |
| raw    | PREROUTING、OUTPUT                              |
| mangle | PREROUTING、INPUT、FORWARD、OUTPUT、POSTROUTING |
| nat    | PREROUTING、INPUT、OUTPUT、POSTROUTING          |


+ 数据包走向

![img](https://www.zsythink.net/wp-content/uploads/2017/02/021217_0051_6.png)

+ iptables命令参数解释

  ![](https://pic4.zhimg.com/v2-cad9d71237f2be1efbcedf2cbcc9f6a3_r.jpg)
  
  
  ```bash
  # iptables                []可选 不加必选
  # [-t table,不加就是filter表]  # 选的哪张表
  # -L [chain,不加就是all]  以链分类来列出规则,policy表示默认规则【accept全部接受】
  	# -nL 可以把解读为域名和程序名的IP和端口号显示出来
  	# -nvL 显示更新详细的内容
  # -F [chain，不加就是当前表的全部链] 清空规则
  # -A chain 将一个或多个规则附加到选定链的末端 
  # -I chain [rulenum,在该序号前，不加就是首位] 插入一条规则
  # -D chain rulenum  删除指定链的某个规则
  # -p/--protocol  要检查的规则或数据包的协议 tcp、udp等，默认为all,前方加！可反选 【!tcp】表示除tcp外的协议
  # -s/-src IP或者IP断,源地址，可以是网络名称、主机名、网络IP地址（带/mask）或普通IP地址
  # --sport  源端口，要先指定协议
  # -i 进入的网卡
  # -d/--dst  IP或者IP断,目的地址
  # --dport  目标端口，要先指定协议
  # -o 发出的网卡
  # -j 动作
  
  	# ACCEPT（允许）、DROP（丢弃）、REJECT（拒绝）、RETURN（返回）
  	
  	# REDIRECT 本机重定向
  	# --to-ports 重定向端口
  	
  	# NAT 网络地址转换
  	
  	# DNAT 目标网络地址转换
  	# --to-destination 重定向目标地址IP:port，不加port表示port与原始一致
  	
  	# SNAT 源网络地址转换
  	# --to-source 修改后的源地址IP:port，不加port表示port与原始一致
  	
  	# MASQUERADE 网络地址伪装
  	
  
  ```

  + 例子

  ```bash
  # 拒绝ip【a.a.a.a】的请求
  iptables [-t filter] -A INPUT -s a.a.a.a -j DROP
  
  # 将到80端口的数据包重定向到本机的8080端口
  iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080
  
  # 将访问【本机IP:80】的请求，转发给【172.18.27.8:8080】
  iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 172.18.27.8:8080 
  iptables -t nat -A POSTROUTING -p tcp [-s 172.18.27.8<我觉得要加上>] --dport 8080 -j SNAT --to-source [本机IP]
  # 和上面一样，另种写法，MASQUERADE相比于SNAT是动态获取本机地址后自动伪装，在本机IP会动态变化时尤其有用
  iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 172.18.27.8:8080
  iptables -t nat -A POSTROUTING -p tcp --dport 8080 -j MASQUERADE
  
  ```
