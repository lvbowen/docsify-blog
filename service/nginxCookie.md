# nginx 使用 proxy_cookie_path 解决反向代理 cookie 丢失问题

## 问题背景
我们系统通过 iframe 内嵌第三方云瑶的页面，内嵌地址使用的相对地址，然后通过 nginx 代理转发。云瑶页面的接口会设置 JSESSIONID 到 cookie， 但是发现设置不进来，导致会调转到他们的登录页。云瑶同事说如果是根目录访问，是可以设置上，由于我们根目录是首页，不可能让出，只需要解决非根路径访问页面也能设置上 cookie 的问题。

## 分析和解决
经过分析发现， 设置的 cookie 的 path 是 `/server`, 由于 cookie 的 path 与地址栏上的 path 不一致，浏览器就不会接受这个 cookie，无法传入 JSESSIONID 的 cookie，导致登录验证失败。查资料，通过设置 proxy_cookie_path 解决：
```
   location /yunyaoserver {
        proxy_pass http://${YUNYAO_URL}/server;
        proxy_cookie_path /server /;        // 关键！
        proxy_redirect off;
        proxy_set_header   host             $host;
        proxy_set_header   x-real-ip        $remote_addr;
        proxy_set_header   x-forwarded-for  $proxy_add_x_forwarded_for;
        proxy_next_upstream error timeout invalid_header http_500 http_504 http_404;
     }
```

## proxy_cookie_path 语法
proxy_cookie_path source target; 
source 源路径
target 目标路径

使用场景：当 nginx 配置的反向代理的路径和源地址路径不一致时使用