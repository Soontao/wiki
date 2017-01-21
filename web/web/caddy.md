# Caddy Server

Caddy是一个用Go语言实现的Web服务器，亮点在于支持自动部署Let's Encrypt，也就是轻易从HTTP切换到HTTPS协议

配置文件十分简单，性能也还不错，推荐个人应用全部使用Caddy

## 安装

到[Caddy官网](https://caddyserver.com/download)下载二进制文件 

解压缩到caddy目录下

## 静态文件服务器

如果只是简单的静态服务器，直接运行caddy.exe(或者./caddy)就可以了

默认情况下，当前目录为Web文件目录，端口为80(Linux下可能需要Root权限)

```
[sudo] ./caddy -port 80 -root .
```

这样caddy目录下的所有文件都可以被访问了

再将设定好的index.html放进caddy目录下，浏览器打开localhost就可以访问了

## 反向代理

首先要确认域名已经指向了部署caddy的服务器

在caddy目录下创建Caddyfile文件，写下反向代理配置（如果文件不与caddy二进制文件同目录，后面的-conf参数需要修改Caddyfile的路径）

```
subname.domain.com {
    proxy / localhost:PORT
}
```

将subname.domain.com下的所有连接全部反向代理到本机的PORT端口

例如本网站，就是利用caddy反向代理wiki.xxxxx.xxx到了本地的3000端口。

然后运行

```
sudo ./caddy -conf 'Caddyfile'
```

即可使用二级域名访问


## FastCGI PHP

支持各种fastcgi，以wordpress为例，安装php5fpm，然后下载caddy

然后在Caddyfile中配置如下几行

```
domain.com {
    root /var/wp
    fastcgi / /var/run/php5-fpm.sock php
}
```

括号中的第一行指定wordpress路径，第二行配置了fastcgi，根据不同情况，可以选择unix socket或者一般的socket

更多高级配置请参看官方文档

## Let's Encrypt

在可能的情况下，caddy会将http自动切换到https

**因为证书是颁发给域名的，所以在使用ip地址访问的时候，是无法切换到HTTPS的**

**另外需要注意，首先需要修改DNS，让域名指向Caddy部署的服务器，因为启动Caddy的时候会，验证域名是否指向本机**

如果只想使用http协议的话，在域名前加上'http://'即可

## 后台运行

linux下

```
sudo nohup ./caddy -conf 'Caddyfile' &
```

---

现在caddy提供了相应的linux服务文件，可以参看下载包中的文档

## 官方示例

在https://github.com/caddyserver/examples/tree/master/markdown有几个官方的配置文件，包括wp，dokuwiki等

## 其他

默认情况下，caddy会读取当前路径中，名为Caddyfile的文件，默认端口是80

第一次启动的时候推荐前台运行，看看获取证书时，有没有什么问题

此页面只是一个示例，[官方文档](https://caddyserver.com/docs)在此处，有更多模块的用法