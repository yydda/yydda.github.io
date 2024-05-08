---
title: Nginx 笔记
tags:
  - Nginx
abbrlink: 12618
date: 2022-04-25 02:13:41
---

Nginx 是一款高性能的 Web 服务器，它可以作为反向代理、负载均衡器和 HTTP 缓存使用。它的设计目标是高并发、低内存占用和高度可扩展性。除了作为 Web 服务器之外，Nginx 还可以作为邮件代理服务器、TCP 代理服务器和流媒体服务器使用。

## 安装 Nginx

在 Linux 系统下，使用以下命令安装 Nginx：

```bash
sudo apt-get update
sudo apt-get install nginx
```

## Nginx 工作模型

Nginx 的工作模型是事件驱动的异步非阻塞模型，它使用少量的线程来处理大量的连接，并且采用 epoll 模型来实现高效的事件处理。

## 常用命令

- 启动 Nginx 服务：sudo systemctl start nginx
- 停止 Nginx 服务：sudo systemctl stop nginx
- 重新加载配置文件：sudo nginx -s reload
- 查看 Nginx 进程：ps -ef | grep nginx
- 查看 Nginx 版本号：nginx -v

## Nginx 配置选项

Nginx 的配置文件 nginx.conf 位于其安装目录的 conf 目录下。nginx.conf 由多个块组成，最外面的块是 main，main 包含 Events 和 HTTP，HTTP 包含 upstream 和多个 Server，Server 又包含多个 location。

常用的配置选项包括：

- worker_processes：设置 Nginx 的工作进程数
- listen：设置 Nginx 监听的 IP 地址和端口号
- server_name：设置虚拟主机的域名
- root：设置虚拟主机的根目录
- index：设置虚拟主机默认的首页文件
- location：设置 URL 匹配规则和处理方式

## Nginx 反向代理

Nginx 可以作为反向代理使用，将客户端的请求转发到后端的 Web 服务器上。反向代理可以提高 Web 应用程序的可靠性、安全性和性能。

使用 Nginx 作为反向代理的步骤如下：

1. 在 nginx.conf 文件中配置 upstream 块，指定后端 Web 服务器的 IP 地址和端口号。

```nginx
upstream backend {
    server 192.168.1.100:8080;
    server 192.168.1.101:8080;
}
```

2. 在 nginx.conf 文件中配置 server 块，设置监听的 IP 地址和端口号，并将请求转发到 upstream 块中的后端 Web 服务器。

```nginx
server {
    listen 80;
    server_name example.com;
    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## 项目部署与域名解析

在部署 Web 项目时，通常需要将其部署到 Nginx 的根目录下，并配置 Nginx 的虚拟主机。如果需要使用自定义域名，需要先将域名解析到服务器 IP 地址上。

1. 将 Web 项目部署到 Nginx 的根目录下，例如/var/www/html/myapp。

2. 在 nginx.conf 文件中新增一个 server 块，设置监听的 IP 地址和端口号，并将请求转发到 Web 项目的根目录。

```nginx
server {
    listen 80;
    server_name myapp.com;
    root /var/www/html/myapp;
    index index.html;
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

3. 在 DNS 服务商处添加一条 A 记录，将 myapp.com 解析到服务器的 IP 地址上。

## 配置 HTTPS 安全协议

HTTPS 是一种基于 TLS/SSL 加密协议的安全传输协议，它可以保护数据在传输过程中不被窃取或篡改。在使用 Nginx 作为 Web 服务器时，可以启用 HTTPS 来提供更安全的服务。

1. 生成私钥文件和证书签名请求文件。

```bash
openssl genrsa -out mykey.key 2048
openssl req -new -key mykey.key -out mycsr.csr
```

2. 向证书颁发机构提交证书签名请求，并获取 SSL 证书文件。

3. 在 nginx.conf 文件中配置 ssl 块，指定私钥文件和 SSL 证书文件的路径。

```nginx
server {
    listen 443 ssl;
    server_name myapp.com;
    ssl_certificate /etc/ssl/certs/myapp.crt;
    ssl_certificate_key /etc/ssl/private/mykey.key;
    ...
}
```

4. 在 server 块中添加 location 块，重定向 HTTP 请求到 HTTPS。

```nginx
server {
    listen 80;
    server_name myapp.com;
    return 301 https://$server_name$request_uri;
}
```

## 总结

Nginx 是一款高性能、可扩展的 Web 服务器，可以作为反向代理、负载均衡器和 HTTP 缓存使用。在实际应用中，我们可以通过配置 Nginx 来提高 Web 应用程序的可靠性、安全性和性能。以上就是 Nginx 介绍与安装、Nginx 工作模型及常用命令、Nginx 配置选项解析、Nginx 反向代理、项目部署与域名解析、配置 HTTPS 安全协议的简单介绍。
