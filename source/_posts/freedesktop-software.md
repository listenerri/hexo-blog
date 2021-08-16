title: freedesktop software
author: listenerri
categories:
  - translation
tags:
  - freedesktop
date: 2021-08-15 16:27:00
---
## 与 freedesktop.org 相关的软件

下面是一个与 freedesktop.org 相关的不完全软件列表。

你可以去我们的 [Gitlab 服务](https://gitlab.freedesktop.org/)查看和下载它们的代码、Bugs、提交合并请求。

freedesktop.org 本身并不每天运行这些项目：我们为自己运行项目的社区提供托管和基础设施。

All projects hosted by us are available under open-source licenses under equitable terms to all parties. These projects do not require Contributor License Agreements which assign more rights to one party than another. They may require an assertion like the Developer's Certificate of Origin which is simply a more formal document to state that you are leally permitted to contribute under the project's license; you do not, however, need to assign anyone rights that you do not yourself receive to others' contributions.

## 桌面中间件和框架

这些项目提供了桌面的基础服务。

- AccountsService 提供本地账户的信息。
- D-Bus 是一个详细系统，提供了进程间和服务间通讯。
- GeoClue 提供地理位置信息。
- PolicyKit 是一个允许非特权应用配置和请求特权服务和接口的工具。
- NetworkManager 允许应用发现，配置，连接有线和无线网络。
- realmd 允许客户端发现，认证和加入网络（如 Active Directory）。
- upower 提供了关于电源设备，如电池和充电状态的信息。
- Zeitgeist 是一个桌面事件日志框架。

## 桌面规范和工具

这些规范和工具定义了跨桌面标准和互通性。

- desktop-file-utils 提供了一些命令行工具用来处理 .desktop 和 .menu 文件。
- icon-theme 包含了一些关于图标主题的标准，还引用了一个叫 hicolor 的默认图标主题。
- pyxdg 是一个访问 freedesktop.org 标准的 python 库。
- shared-mime-info 是一个包含大量常见 MIME 类型的包，通过将现有的 KDE 和 GNOME 数据库转换为新格式并将它们合并在一起创建，以及基于 share-mime-info 规范的更新数据库的软件。
- startup-notification 是启动通知的示例实现（在应用程序完成启动时告诉桌面环境）。
- xdg-utils 是一套命令行实用程序，用于简化与自由桌面的整合。它具有创建菜单、打开文件、设置 mime 类型等简单功能。
- xdg-user-dirs 是一个处理用户 home 目录下一些标准目录的工具。

## 显卡驱动，窗口系统和支持库

这些项目允许应用程序在屏幕上渲染图形，包括对 GPU 加速的支持。

- Beignet 是针对老式英特尔 GPU 的 OpenCL 实现，现在被 NEO 驱动所取代。
- Cairo 是一个具有跨设备输出支持的矢量图形库。
- DRM 是 Linux 内核图形子系统，在 dri-devel 列表中开发。
- drm_hwcomposer 是在 DRM/KMS 设备上运行的 Android HWComposer 的后端。
- Mesa 提供了 OpenGL、OpenGL ES、Vulkan、EGL 和 GLX 渲染 API 的硬件加速和软件实现。
- Monado 是用于虚拟和增强现实环境和设备的 OpenXR API 的实现。
- Nouveau 是 NVIDIA GPU 的逆向工程驱动程序。
    - Piglit 是用于 OpenGL 和 OpenGL ES 实现的测试套件。
    - VirGL 为 VM 内的虚拟客户提供 OpenGL 和 OpenGL ES 加速。
- Pixman 是一个低级的纯软件 2D 像素操作和合成库。
- Plymouth 在开机启动期间提供启动画面和进度更新。
- Wayland 是一个被各种桌面使用的窗口系统。
- X.Org 是 X Window 系统的一个实现，称为 X11。
    - XCB 为 X11 协议提供了一个低级客户端库。
    - Xephyr 是一个嵌套的 X11 服务器，主要用于测试。
    - xrestop 是一个 X Server 资源使用监视器像 “top” 命令一样，它使用 XRes 扩展。
    - xsettings 提供了配置中心的功能。
    - X Testing 提供有关用于测试 X 服务器和客户端的各种软件的信息。
    - xwininfo 是一个窗口信息实用程序，适用于应用程序、工具包和窗口管理器的开发人员。

## 输入、国际化 (i18n) 和字体渲染

这些项目为键盘、鼠标、手写板或其他输入和字体渲染提供了支持。

- fontconfig 是一个用于配置和自定义字体访问的库。
    - Xft 是一个用于客户端字体渲染的库。
- libinput 是输入设备的高级包装库，提供对键盘、鼠标、触摸板、触摸屏和手写板的访问。
    - libevdev 是输入设备的高级包装库，提供对键盘、指针、触摸板、触摸屏和平板设备的访问。
- uchardet 是一个编码检测器库，它从未知字符编码文件中提取字节序列并尝试确定文本的编码。
- UTF-8 是一个记录和宣传开源项目使用 UTF-8 编码的项目。
- xkeyboard-config 是 XKB 键盘布局的存储库，用于大多数桌面和窗口系统。

## 其他项目

- Bustle 记录并显示 D-Bus 流量的序列图。
- CppUnit 是著名的单元测试 JUnit 框架的 C++ 移植。
- kmscon 是一个基于 KMS/DRM 的系统控制台。
- libbsd 是一个提供 BSD 系统实用功能的库。
- pkg-config 是一个用于管理，开发库的编译/链接标志的系统，适用于大多数构建系统。
- Slirp 是一种通用 TCP-IP 仿真器，被虚拟机管理程序广泛用于提供虚拟网络服务。
- SPICE 提供对虚拟访客的远程访问。
- SyncEvolution 在设备和服务之间同步日历和联系人信息。

## 多媒体和图片支持

这些项目为音频、图像和视频的捕获、回放和流式传输提供支持。

- Farstream 允许基于网络远程传输多媒体流。
- GStreamer 是一个流媒体框架。
- libnice 在 NAT 之后使用 ICE 协议在各方之间建立对等连接。
- libopenraw 是一个用于 Camera RAW 文件解码和处理的开源库。
- libspectre 是 PostScript 渲染库。
- media-player-info 描述了外部媒体播放器设备。
- Poppler 是一个 PDF 渲染库。
- PulseAudio 是一个 sound 系统和API。
- VDPAU 提供视频解码加速和高质量视频呈现。

## 其他硬件支持

这些项目为硬件设备提供了高级支持。

- cups-pk-helper 供细粒度的 PolicyKit 身份验证集成，以允许用户配置 CUPS 打印服务。
- fprint 为各种指纹读取器提供硬件支持。
- ModemManager 是一个 DBus 系统服务，它为与移动宽带调制解调器的通信提供统一的高级 API。
    - libmbim 是一个较低级别的库，用于管理 MBIM-powered 驱动的移动宽带调制解调器。
    - libqmi 是一个较低级别的库，用于管理 QMI-powered 驱动的移动宽带调制解调器。

## 其他地方托管的项目

这些项目以前由 freedesktop.org 托管，但已转移到其他地方。

- AppStream 是几个主要的GNU/Linux提供者之间的协议（如红帽公司、Canonical公司、SUSE、Debian、Mandriva等），为了要创造Linux上的共同的软件安装程序以及一个统一的软件安装图形用户界面，还有分享元数据。
- at-spi2 是一个为应用程序提供可访问性工具信息的框架。
- Avahi 是一个多播 dns 网络服务发现库。
- Clipart 是一个开源剪贴画存储库。
- Cogl 是 OpenGL 和 OpenGL ES 之上的抽象
- colord 是一个颜色管理服务。
- epoxy 处理 OpenGL 函数指针。
- Flatpak 安装、管理和运行沙盒桌面应用程序。
- FreeType 是一个 TrueType 字体渲染库。
- Galago 是一个桌面状态框架，旨在在程序之间传输状态信息。
- FriBidi 是一个实现 Unicode Bidirectional Algorithm（双向算法）并处理了阿拉伯语的库。
- HarfBuzz 是通用的 OpenType 布局引擎，由 Pango、Qt 和可能的其他公司共享。
- intltool 提供了用于翻译 XML 文件的脚本。
- LDTP 是一个 Linux 桌面测试框架。
- libburn 是一个开源库套件，用于读取、制作和写入光盘。
- libminidump 是一种用于核心转储的压缩格式。
- LibreOffice 是一个跨桌面的办公套件。
- OHM 是温度和风扇传感器的监视器。
- OpenRaster 是一种用于分层光栅图像的文件格式。
- p11-glue 是一个 PKCS#11 支持库。
- PackageKit 允许用户发现和安装软件包（跨发行版）。
- SCIM (Smart Common Input Method) 是一个开发输入法服务的平台。
- systemd 是一个完整的系统管理器，包括服务和守护进程管理、日志记录、会话控制等。
- Telepathy 是一种实时通信框架（在线状态、消息传递、voice/video-over-IP等）。
- Tracker 是一个文件系统索引器、元数据存储系统和搜索工具
- udisks 提供一个守护程序、工具和库来访问和操作磁盘、存储设备和技术。
- uim 是一种支持多种语言的输入法，重点是中日韩。
- VA-API 为所有视频格式提供视频加速 API。
- xiccd 是一个X11颜色管理守护程序。
- XQuartz 是一个在 Apple MacOS 上运行的X11服务器。

## 不活跃的项目

这些项目以前托管在这里，但不再维护的。代码可能无法使用，并且可能无法为这些项目提供修复或更改。

- APOC 提供了配置和策略控制。
- CCSS 提供 CSS 解析.
- CJK-Unifonts 提供了CJK Unicode TrueType字体，包括Minnan和Hakka语言。
- ConsoleKit 提供了登录和会话管理，现在已被 Logind 替换。
- Desktop VFS 是针对GUI应用程序的虚拟文件系统。
- dolt 是 libtool（与 autoconf automake 一起使用的工具） 的包装器，提高其性能，现在是多余的。
- Enchant 是一个跨桌面拼写检查工具。
- Eventuality was an "application automation meets cron" type D-Bus based framework for creating a means to schedule arbitrary "actions" performed by conforming apps.
- glitz 是 OpenGL 2D 图形库，包括 GPU 加速的 Cairo 后端。
- GTK-Qt Theme Engine 旨在统一 GTK 和 Qt 的主题。
- Gypsy 是一个守护进程，提供对 GPS 设备的访问。
- HAL was an all-encompassing hardware abstraction layer.
- Hieroglyph 是一个 PostScript 渲染库。
- icon-slicer 是生成图标主题和 libXcursor 游标主题的实用程序。
- immodule for Qt 是 Qt 的模块化，可扩展的输入法子系统。
- libdlo 是一个用于 DisplayLink USB 图形适配器的用户空间库。
- liblazy 封装了一些常见的D-Bus提供了常见的 D-Bus 方法。
- liboil 是一个库，为了简化 MMX/SSE/Altivec 扩展的开发和代码维护。
- libxklavier 是一个工具库，X keyboard 相关的。
- Loudmouth 是一个 XMPP 工具库。
- OpenSync 是一个在设备之间提供标准化数据同步的项目。
- Papyon 支持 MSN Messenger 协议。
- pm-utils 发行版无关的一组管理睡眠和唤醒的脚本。
- Razor 试图替换基于 RPM 的系统的 yum 包管理器。
- Scratchbox2 是一个交叉汇编环境。
- shared-desktop-ontologies was an attempt to define standard NEPOMUK ontologies for the semantic desktop.
- Shave 使 AutoTools 构建输出不那么冗余。
- swfdec 为 Adobe Flash playback 提供支持。
- system-tools-backends 尝试提供发行版无关的系统配置工具。
- Tango 提供常见的图标和主题设计指南。
- unicode-translation 旨在将 Unicode 字符名称和其他数据翻译成多种语言。
- waimea 旨在成为 X 窗系统的标准窗口管理器，使用 Cairo 图形库进行所有渲染。
- wmctrl 是一个与兼容 EWMH 的窗口管理器交互的命令行工具。
- xfullscreen 是一个对支持全屏模式的应用程序或窗口管理器非常有用的模块。
- Xoo 是嵌套 X 服务器的包装器。
- xprint 试图为 X11 提供打印服务。
- xresponse 是一个测量鼠标点击事件响应时间的工具。
- Ytstenut 是一种允许在多个设备之间同步用户活动的协议。

翻译自：https://www.freedesktop.org/wiki/Software/
