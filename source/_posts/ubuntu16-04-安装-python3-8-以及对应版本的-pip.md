title: ubuntu16.04 安装 python3.8 以及对应版本的 pip
author: listenerri
categories:
  - python
tags:
  - linux
  - python
date: 2021-08-10 17:48:00
---
ubuntu 系统 16.04 版本中，最新版本的 python 是 3.5，从系统源中找不到更新版本的 python，本文介绍一种安装 python3.8 的方法，以及安装对应版本的 pip。

## 安装 python3.8

执行如下命令增加 ppa 仓库，并更新源数据：

```
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
```

如果提示找不到 `add-apt-repository` 命令，执行如下命令安装：

```
sudo apt install software-properties-common
```

更新完成源数据之后，就可以安装 ppa 仓库中的包了，这个仓库中不止是包含 python3.8，还包含其他版本的 python 包，详细情况可查看链接：

https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa

执行以下命令安装 python3.8 相关的包：

```
sudo apt install python3.8 python3.8-dev python3.8-distutils python3.8-venv
```

安装完成后，当需要使用 3.8 版本的 python 时，执行：

```
python3.8 --version
```

## 安装 pip

当需要使用 pip 时，由于系统源里的 pip 是 python3.5 对应的，所以针对 python3.8 并不能正常工作，比如使用 virtualenv 创建 python3.8 的虚拟环境时就会报错，可以执行如下命令查看 pip 是对应哪个 python 版本的：

```
pip3 --version

# 可能的输出如下，注意最后括号里的内容
pip 8.1.1 from /usr/lib/python3/dist-packages (python 3.5)
```

前面新增的 ppa 仓库中，只有 python 的包，没有 pip 的，所以需要额外的安装方法，参考 pip 官方安装教程：https://pip.pypa.io/en/stable/installation/，使用如下方法安装 python3.8 对应的 pip：

```
wget -O /tmp/get-pip.py https://bootstrap.pypa.io/get-pip.py
python3.8 /tmp/get-pip.py
```

上面的两个命令是，下载 pip 官方安装脚本，然后使用 python3.8 执行安装脚本，执行完成后就安装好了 python3.8 对应的 pip，执行如下命令检验：

```
pip --version
pip3 --verison
pip3.8 --version
```

注意！按照此方法安装 pip 后，系统中的默认 pip 将会是最新安装的 pip3.8，可执行程序位于 /usr/local/bin 目录下。

系统默认源中的 python3-pip 包（如果之前安装过），也就是 python3.5 对应的 pip，如果丢失了，可通过执行如下命令重新安装：

```
sudo apt install --reinstall python3-pip
```

需要使用这个版本的 pip 时，使用如下命令：

```
/usr/bin/pip --version
```

虽然重新安装了 python3.5 版本的 pip 但系统默认的 pip 仍然是 python3.8 的，因为 python3.8 的 pip 可执行程序位于 `/usr/local/bin/pip`，这个路径在环境变量中优先级一般更高。

如果想让 python3.5 版本的 pip 作为默认 pip 使用，可以执行如下命令：

```
sudo ln -sf /usr/bin/pip3 /usr/local/bin/pip
sudo ln -sf /usr/bin/pip3 /usr/local/bin/pip3
```

这样当需要使用 python3.8 版本的 pip 时，就需要使用命令 pip3.8 了。