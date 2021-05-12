---
date: 2021-01-20 09:34:07
title: python 版本管理
tags: python, pyenv, 版本管理
---

## 什么是pyenv

[pyenv](https://github.com/yyuu/pyenv) 是 Python 版本管理工具。 pyenv 可以

- 改变全局的 Python 版本
- 在系统中安装多个版本的 Python
- 设置目录级别的 Python 版本
- 创建和管理 virtual python environments 。

## pyenv与virtualenv的区别

pyenv 通过修改系统环境变量来实现不同 Python 版本的切换。

 virtualenv 通过将 Python 包安装到一个目录来作为 Python 包虚拟环境，通过切换目录来实现不同包环境间的切换。

## Pyenv安装

> 前提：系统已经安装 git

pyenv 提供了自动安装的工具，执行命令安装

```bash
curl https://pyenv.run
```

执行安装后提供了如下工具：

- `pyenv`: pyenv 工具自身
- `pyenv-virtualenv`: pyenv 的插件可以用来管理 vierual environments
- `pyenv-update`: 用来更新 pyenv 的插件
- `pyenv-doctor`: 验证 pyenv 和依赖是否安装的插件
- `pyenv-which-ext`: 用来寻找相同命令的插件

## 常用命令

```bash
pyenv install --list # 列出可安装版本
pyenv install <version> # 安装对应版本
pyenv install -v <version> # 安装对应版本，若发生错误，可以显示详细的错误信息
pyenv versions # 显示当前使用的python版本
pyenv which python # 显示当前python安装路径
pyenv global <version> # 设置默认Python版本
pyenv local <version> # 当前路径创建一个.python-version, 以后进入这个目录自动切换为该版本
pyenv shell <version> # 当前shell的session中启用某版本，优先级高于global 及 local
```

## python 安装与卸载

安装

```bash
pyenv install 3.6.8   # 安装 python
```

安装后可以检视：

```bash
ls ~/.pyenv/versions/
```

从本机卸载对应版本的 Python 也特别简单：

```bash
pyenv uninstall 3.6.8 # 卸载 python
```

或者直接删除掉 `~/.pyenv/versions/3.6.8` 对应的目录。

```bash
rm -rf ~/.pyenv/versions/3.6.8
```

## 查看可安装 Python 版本

使用如下命令查看可安装版本

```bash
pyenv install -l
```

## Python 版本切换

```bash
pyenv global 2.7.3  # 设置全局的 Python 版本，通过将版本号写入 ~/.pyenv/version 文件的方式。
pyenv local 2.7.3 # 设置 Python 本地版本，通过将版本号写入当前目录下的 .python-version 文件的方式。通过这种方式设置的 Python 版本优先级较 global 高。
```

pyenv 会从当前目录开始向上逐级查找 `.python-version` 文件，直到根目录为止。若找不到，就用 global 版本。

```
shell > local > global
```

```bash
pyenv shell 2.7.3 # 设置 shell 的 Python 版本。

pyenv shell --unset # 取消 shell的设置

pyenv rehash  # 创建垫片路径（为所有已安装的可执行文件创建 shims，如：~/.pyenv/versions/*/bin/*，因此，每当你增删了 Python 版本或带有可执行文件的包（如 pip）以后，都应该执行一次本命令）
```

## pyenv-virtualenv

使用**自动安装 pyenv** 后，它会自动安装部分插件，通过 pyenv-virtualenv 插件可以很好的和 virtualenv 结合：

```
cd ~/.pyenv/plugins
ll
total 24K
drwxr-xr-x 4 einverne einverne 4.0K Apr 22 10:55 pyenv-doctor
drwxr-xr-x 5 einverne einverne 4.0K Apr 22 10:55 pyenv-installer
drwxr-xr-x 4 einverne einverne 4.0K Apr 22 10:55 pyenv-update
drwxr-xr-x 7 einverne einverne 4.0K Apr 22 10:55 pyenv-virtualenv
drwxr-xr-x 4 einverne einverne 4.0K Apr 22 10:55 pyenv-which-ext
drwxr-xr-x 5 einverne einverne 4.0K Apr 22 10:54 python-build
```

### 创建虚拟环境

```
pyenv virtualenv 2.7.15 env-name
```

若不指定 python 版本，会默认使用当前环境 python 版本。如果指定 Python 版本，则一定要是已经安装过的版本，否则会出错。环境的真实目录位于 `~/.pyenv/versions` 下

### 列出当前虚拟环境

```
pyenv virtualenvs
pyenv activate env-name  # 激活虚拟环境
pyenv deactivate #退出虚拟环境，回到系统环境
```

### 删除虚拟环境

```
pyenv uninstall env-name
rm -rf ~/.pyenv/versions/env-name  # 或者删除其真实目录
```

使用 pyenv 来管理 python，使用 pyenv-virtualenv 插件来管理多版本 python 包。此时，还需注意，当我们将项目运行的 env 环境部署到生产环境时，由于我们的 python 包是依赖 python 的，需要注意生产环境的 python 版本问题。

## 可能碰到的问题

当在mac上使用pyenv install 3.6.8的时候，我曾碰到这样的错误

```
python-build: use readline from homebrew
python-build: use zlib from xcode sdk

BUILD FAILED (OS X 11.0 using python-build 20180424)

Inspect or clean up the working tree at /var/folders/gj/x6v5vwdx1v7741fdfcxwmr100000gn/T/python-build.20200830033458.15319

```

网络上有这样的解决办法

```bash
$ brew install zlib
$ export LDFLAGS="-L/usr/local/opt/zlib/lib" 
$ export CPPFLAGS="-I/usr/local/opt/zlib/include
```

但我安装后仍然存在这个问题，几经研究后，我发现可以通过下面的方法解决。

### 步骤1 command-line tools

在Xcode检查 command-line tools 的版本

1. 运行 Xcode.app
2. 打开Preference > Locations
3. 选择正确的command-line tools版本

![Image for post](./python%20版本管理/1*I4eATihe-lAIOxpZ8tINrg-20210127172329408.png)

![Image for post](./python%20版本管理/1*I4eATihe-lAIOxpZ8tINrg.png)

### 步骤2 安装 python

使用下面的命令来安装

```bash
$ CFLAGS="-I$(brew --prefix openssl)/include -I$(brew --prefix bzip2)/include -I$(brew --prefix readline)/include -I$(xcrun --show-sdk-path)/usr/include" LDFLAGS="-L$(brew --prefix openssl)/lib -L$(brew --prefix readline)/lib -L$(brew --prefix zlib)/lib -L$(brew --prefix bzip2)/lib" pyenv install --patch 3.6.8 < <(curl -sSL https://github.com/python/cpython/commit/8ea6353.patch\?full_index\=1)

$ pyenv versions
* system (set by /Users/alvin/.pyenv/version)
  3.6.8
```

