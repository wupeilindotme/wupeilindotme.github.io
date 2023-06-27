---
title: nvm
date: 2023-06-23 22:59:01
tags: 
  - nodejs
  - 开发环境管理
---





# 前言

在使用Hexo搭建博客时，推荐安装16.x以上版本的nodejs。 但是自己电脑工作时所用的node版本是14.x， 如果为了搭建博客又去安装其他版本的node，需要手动去切换环境变量。 切换来回避免不了麻烦甚至出错，于是想着有没有一个更加好的解决方案。   



这就是本文要介绍的 NVM (Node enVironment Manager) —— 一个node环境管理工具。



---



# NVM安装

## windows用户

- 安装

  windows用户可以通过 <a href="https://github.com/coreybutler/nvm-windows/releases" target="_blank">Releases nvm-windows</a>下载 setup.exe 进行安装

1. 进入上路链接github仓库， 找到 `v1.1.11` 下的 Assets， 找到 nvm-setup.exe 下载

2. 双击启动 .exe 进行安装

3. 选择nvm 的安装路径

4. 选择node的安装路径

5. 打开控制台，输入 `nvm -v` 如果正确展示版本表示安装成功

   

**注意**：两者路径都可以自定义，一定不要带中文，最好不要带空格（大部分开发软件都是如此）







## linux用户

- 安装

  1. 使用curl 或者 wget

     ```shell
     curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
     ```

  2. 安装脚本运行后，会在您的主目录下创建一个 `.nvm` 文件夹，并在您的 shell 配置文件（例如 `.bashrc`、`.zshrc` 或 `.profile`）中添加必要的配置。

  3. 执行 `nvm --version` 查看安装情况

     

---





# NVM使用

接下来介绍NVM的一些常用指令



```shell
nvm -v # 查看nvm版本
nvm list # 查看目前可用的node版本

nvm install <node version> # 使用nvm安装指定版本的nodejs
nvm install stable # 使用nvm安装稳定版nodejs


```

