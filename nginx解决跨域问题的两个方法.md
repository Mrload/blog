nginx服务端口8080

前端服务端口8081

后端服务端口8082

# **在nginx中配置地址重写**（反向代理）

+ 将前后端的访问地址全部重写给8080

```bash
server
{
    listen       8080;
    location /resource {
    rewrite  ^/resource/?(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://192.168.137.189:8082/; # 转发地址
    }
    location /js {
    rewrite  ^/js/?(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://192.168.137.189:8081/; # 转发地址
    }
}
```

# **nginx中添加允许跨域请求头**

```bash
server
{
    listen       8080;
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
 
    location /resource {
        rewrite  ^/resource/?(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://192.168.137.189:8082/; # 转发地址
    }
```

这就和不用nginx，直接在后端项目中（tomcat或者自己写的服务端代码）配置允许跨域一样，只不过把允许跨域的配置放在nginx中，

这个配置解决了前端项目访问nginx的跨域问题，而nginx访问后端项目不存在跨域问题（不是浏览器，没有同源策略限制）。此时nginx对于后端就相当于一个代理分发服务器。

前端访问8080等同于访问8082。8080==>8082这一过程中添加了允许跨域的请求头信息。