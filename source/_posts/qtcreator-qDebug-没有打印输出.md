title: qtcreator qDebug 没有打印输出
author: listenerri
categories:
  - qt
tags:
  - qt
  - qtcreator
  - qdebug
date: 2021-08-14 11:22:00
---
前段时间遇到个奇怪的问题，在 qtcreator 中 `qDebug` 语句的打印内容一直出不来，解决方法是在构建工具链设置中新增一个环境变量：`QT_ASSUME_STDERR_HAS_CONSOLE=1`

具体步骤（英文菜单）：
点击 qtcreator 工具栏，依次点击 `Tools` > `Options` > `Kits`，之后选择正在使用的 `Kits`，滚动找到 `Environment`, 点击 `Change` 然后新增上面提到的环境变量即可。

参考链接：https://stackoverflow.com/questions/41425530/qtcreator-qdebug-messages-not-shown