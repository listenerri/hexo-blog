title: nextcloud 部署后没有日历邮件等应用
author: listenerri
categories:
  - others
tags:
  - nextcloud
date: 2021-08-13 22:46:00
---
在公司内部服务器上搭了一个 [Nextcloud](https://nextcloud.com/) 服务，跑起来之后发现没有日历、邮件等应用，且不能打开应用商店，分析了服务日志发现是由于网络问题。

启动服务时加上代理即可正常，比如使用 docker 跑的话：

```
docker run --name nextcloud -p 8080:80 -e http_proxy=http://192.168.1.100:1081 -e https_proxy=http://192.168.1.100:1081 nextcloud
```

代理相关参数解释：

- -e http_proxy 为容器内 NC 服务设置 http 代理地址和端口
- -e https_proxy 同上
