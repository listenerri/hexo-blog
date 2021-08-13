title: linux 系统使用 comfast CF-WU810N 无线网卡创建 wifi 热点
author: listenerri
categories:
  - linux
tags:
  - wifi
  - RTL8188EUS
date: 2021-08-12 15:50:00
---
我用来创建热点的工具是 create_ap 可以直接去官方仓库中查看 README 来安装：https://github.com/oblique/create_ap

把 USB 无线网卡插到运行 linux 系统的机器上，然后使用 `lsusb` 命令查看信息，可以得到如下：

```
Bus 001 Device 006: ID 0bda:8179 Realtek Semiconductor Corp. RTL8188EUS 802.11n Wireless Network Adapter
```

可以看到芯片是 `RTL8188EUS`，在 manjaro 系统下，虽然可以识别设备，也能正常连接无线网使用，但默认驱动不能用来创建 wifi 热点，所以需要手动安装一个新的驱动。

经过搜索和不断测试，最终确定一个可用驱动：
https://github.com/aircrack-ng/rtl8188eus

手动安装方法可以参考驱动代码仓库的 README 文档。

如果是 arch 系的系统，比如 manjaro 系统，可以直接 aur 仓库里的包：`8188eu-aircrack-dkms-git`，这个包里带有必要的配置文件，安装完成后重启即可生效。

驱动生效后就可以使用 `create_ap` 工具创建 wifi 热点了。