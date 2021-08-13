title: edge 实现 ctrl tab 切换最近标签页
author: listenerri
categories:
  - linux
tags:
  - linux
  - chrome
  - edge
  - ctrl-tab
date: 2021-08-13 11:33:00
---
以前一直在用 firefox 火狐浏览器作为主力浏览器，主要是因为它可以做到不翻墙就实现全平台（linux/mac/windows/android/ios）数据同步，只是 firefox 在 linux 下长时间运行后会变得打开新网页速度很慢，必须要退出程序重新打开才能恢复正常访问速度，给我这种感觉已经很长一段时间了，搜索不到解决方法，自己也没有摸索出来根本原因，但苦于没有更好的选择，只能一直将就下去。

不久前巨硬为 linux 版 edge 浏览器增加了账户同步功能，我终于等来了一个新的，不翻墙全平台数据同步浏览器，虽然是测试版，但巨硬维护很积极，bug 修复更新很及时，相信很快就会发布正式版。

对我来说 edge 浏览器在各个平台上的使用体验都很符合需求，唯独有一点让我很不舒服，就是 ctrl-tab 切换标签页的方式，firefox 浏览器按一下 ctrl-tab 默认会在最近使用的两个标签页中切换，而 edge 则是循环得朝着右边的标签页切换，chrome 也是同样的行为。在搜索这个问题相关的内容时发现，有一部分人觉得这种方式很好，当然也有一部分人和我有同样的感觉，所以这个问题比较主观，并不是说 edge 或 chrome 的切换模式就一定是不好用的。当然 edge 或 chrome 能够像 firefox 一样对这个行为在设置里提供一个修改选项自然是最好的结果。

经过一段时间的摸索，最终找到了一个满足需求的扩展程序，在 edge 和 chrome 的官方扩展仓库中都能找到，链接分别是：

https://microsoftedge.microsoft.com/addons/detail/quickey-%E2%80%93-the-quick-tab-s/mcjciddpjefdpndgllejgcekmajmehnd

https://chrome.google.com/webstore/detail/quickey-%E2%80%93-the-quick-tab-s/ldlghkoiihaelfnggonhjnfiabmaficg

扩展程序代码仓库是： https://github.com/fwextensions/QuicKey

QuickKey 扩展程序提供了很多丰富的功能，感兴趣的可以自行探索。

针对修改 ctrl-tab 行为的功能并不是默认启用的，需要手动修改一些浏览器配置，官方有详细的文档：https://fwextensions.github.io/QuicKey/ctrl-tab/

下面我筛选出适合我的步骤，并翻译如下，此方法适用于 edge 和 chrome。

## 使用 ctrl-tab 切换最近标签页

正常情况下 edge/chrome 不允许设置 ctrl-tab 作为切换标签页的快捷键，但是可以使用开发者工具打破这一限制。（相对于其他方法这是最简单有效的一个方法）

1. 根据上面的链接安装 QuickKey 扩展程序
2. 右键点击工具栏上 QuickKey 扩展程序图标，在弹出的菜单中选择“扩展选项”
3. 向下滚动至 `Browser keyboard shortcuts` 或 `Chrome keyboard shortcuts`
4. 点击 `Change browser shortcuts` 或 `Change Chrome shortcuts` 按钮
5. 在打开的新标签页中按 `ctrl-shift-J`（Windows/Linux）或 `cmd-opt-J`（macOS）打开开发者工具面板
6. 复制以下代码粘贴到开发者工具面板中，按回车键执行：
```
chrome.developerPrivate.updateExtensionCommand({
    extensionId: "mcjciddpjefdpndgllejgcekmajmehnd",
    commandName: "30-toggle-recent-tabs",
    keybinding: "Ctrl+Tab"
});
```

到这里就完成了，且已经生效了，可以按 ctrl-tab 快捷键测试下。

注意：如果你使用 edge 浏览器，但却是从 Chrome 扩展程序商店安装的 QuickKey 扩展，那么你需要将上面代码中的 `mcjciddpjefdpndgllejgcekmajmehnd` 替换为 `ldlghkoiihaelfnggonhjnfiabmaficg` 也就是修改以下 `extensionId` 的值。