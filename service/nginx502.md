## 记一次踩坑 nginx 502 问题

### 问题出现背景
广东河长管理系统需要通过 iframe 嵌入第三方系统（赋安）部分页面，地址写全路径的话会有 cookie 跨域不能共享导致不能免登的问题，所以写的相对路径，通过 nginx 匹配自定义的前缀 `/fuan` 转发到赋安的地址，开始代理配置如下：
```
# 代理赋安url地址
location /fuan/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    # 从环境变量取转发地址
    proxy_pass http://${FUAN_URL}/;     // FUAN_URL = 474454be03.qicp.vip
}
```
然后页面就报了 502 Bad Gateway 问题。

## 排查过程
找公司 p8 排查了下，rancher 日志可以看到报错 474454be03.qicp.vip 对应的 ip(假设这个ip地址是 1.1.1.1) 访问不通,  在容器里执行 `curl http://1.1.1.1/` 不通， `curl 474454be03.qicp.vip` 是通过的，p8 大佬说用 `curl -H "Host:474454be03.qicp.vip" http://1.1.1.1/` 试下也是通的，那就把代理指定的 host 改成 474454be03.qicp.vip 应该就好了，发现果然是的。

## 解决
正确配置：
```
location /fuan/ {
    proxy_set_header Host ${FUAN_URL}; 
    proxy_set_header X-Real-IP $remote_addr;
    # 从环境变量取转发地址
    proxy_pass http://${FUAN_URL}/;
}
```

## 扩展阅读
- [Cookie 的 SameSite 属性](https://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)