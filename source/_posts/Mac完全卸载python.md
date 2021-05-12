---
date: 2021-01-20 09:34:07
title: Mac完全卸载python
tags: python, Mac
---

Python安装之后，散落在电脑各处，删除起来比较麻烦！

在安装 Python 时，会自动建立如下目录：

- Python framework，即 Python 框架；
  Mac 自带的 Python，其框架目录为：
  System/Library/Frameworks/Python.framework
  另外安装的 Python，其（默认）框架目录为：
  /Library/Frameworks/Python.framework

- Python 应用目录

- 指向 Python 的连接

  

以下步骤都是在 Mac 终端进行，完整的删除python。

### 查看python版本

```text
$ python3 --version
Python 3.7.1
```

### 删除框架

```text
$ ls /Library/Frameworks/Python.framework/Versions/
3.7

$ sudo rm -rf /Library/Frameworks/Python.framework/Versions/3.7
```

### 删除应用目录

```text
$ cd /Applications
$ sudo rm -rf Python\ 3.7/ #Python 3.7存在空格
```

查看launchpad中python3的IDLE就被删除了

### 删除/usr/local/bin 目录下指向的Python3的连接

```text
$ cd /usr/local/bin/ 
$ ls -l /usr/local/bin
$ rm Python3.7相关的文件和链接 #Python3.7相关的文件和链接需要你自行确认
```

Python3.7 对应的文件和链接最好删除干净；无法确认的文件和链接就上网搜一下bing.

### 删除环境路径

```text
$ vi ~/.bash_profile
```

删除Python3.7设置的环境路径。

### 确认是否已经删除

```text
$ python3
-bash: python3: command not found
```