title: 解决 gradle 下载不下来
author: listenerri
categories:
  - android
tags:
  - gradle
  - android-studio
  - idea
date: 2021-08-13 23:59:00
---
有时候由于网络问题，会发现 android-studio 或 idea 下载不懂 gradle，可以修改 `gradle-wrapper.properties` 配置文件中的 `distributionUrl` 选项来解决这个问题。

```
distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-5.1.1-all.zip
```

这个选项表示从腾讯提供的 gradle 镜像服务下载数据，注意替换上面示例链接末尾的具体文件名。

可以访问此链接查看有哪些版本的 gradle 可以下载：https://mirrors.cloud.tencent.com/gradle/