title: Qtcreator Fakevim 映射 + 寄存器不生效
author: listenerri
categories:
  - qt
tags:
  - qtcreator
  - fakevim
  - clipboard
  - register
date: 2021-09-10 17:53:00
---
在 QtCreator 使用 Fakevim 可以指定读取配置文件，在配置文件中映射按键，我发现当映射 `+` 寄存器时（也就是系统剪切板）不生效，比如下面这行命令：
```
map <Space>yy "+yy
```

尝试了很多方法，比如转义：`\"+yy` 等都不行，网上也搜不到相关问题，下载了 QtCreator 的源码后，分析 Fakevim 插件解析配置文件的部分，发现是一个 Bug，相关代码如下：

```
// qt-creator/src/plugins/fakevim/fakevimhandler.cpp
// bool FakeVimHandler::Private::handleExSourceCommand(const ExCommand &cmd)

//  remove comment
int i = nextline.lastIndexOf('"');
if (i != -1)
    nextline = nextline.remove(i, nextline.size() - i);
```

原来是判断 `"` 号是配置文件注释，将 `"` 号开始到结尾的部分给删除了，已经给 QtCreator 官方提了 Issue，说实话我也没想到修复这个问题的好方法。

另辟蹊径，既然要判断 `"` 号，而且代码里是判断最后一个 `"` 号的位置，那就给它加上个行内注释，让它不删除命令内容就行，比如还是上面的命令，修改后如下：
```
map <Space>yy "+yy " placehoder commend for a Fakevim parse bug
```

这样源码删除后面的注释后，剩下的就是原本想要执行的命令了，搞定 :)