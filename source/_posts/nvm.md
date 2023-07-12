---
title: nvm
date: 2023-06-23 22:59:01
tags: 
  - Node.js
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

   

**注意**：两者路径都可以自定义，一定不要带中文（大部分开发软件都是如此）







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



## 查看版本

```shell
# 查看nvm版本
nvm -v 
# 查看目前（已安装）可用的node版本
nvm list 

# 查看可安装的node版本
nvm list available 
```



## node 安装/卸载

安装

```powershell
# 使用nvm安装指定版本的nodejs
nvm install <node version> 

# 安装16.14.0 版本的nodejs
nvm install 16.14.0 

# 使用nvm安装稳定版nodejs
nvm install stable
```



卸载

```powershell
# 指定卸载版本
nvm uninstall <version> 

# 指定卸载16.14.0版本的nodejs
nvm unistall 16.14.0 
```



## node 版本切换

```powershell
# 指定node版本为当前node环境
nvm use <node version>

# 指定使用 16.14.0的node版本
nvm use 16.14.0 

# 指定默认版本
nvm alias default <version> 
```



## 示例

```powershell
# 安装完成后执行
nvm -v
>> 
1.1.10

# 查看可安装的node版本
nvm list available
>> 
|   CURRENT    |     LTS      |  OLD STABLE  | OLD UNSTABLE |
|--------------|--------------|--------------|--------------|
|    20.3.1    |   18.16.1    |   0.12.18    |   0.11.16    |
|    20.3.0    |   18.16.0    |   0.12.17    |   0.11.15    |
|    20.2.0    |   18.15.0    |   0.12.16    |   0.11.14    |
|    20.1.0    |   18.14.2    |   0.12.15    |   0.11.13    |
|    20.0.0    |   18.14.1    |   0.12.14    |   0.11.12    |
|    19.9.0    |   18.14.0    |   0.12.13    |   0.11.11    |
|    19.8.1    |   18.13.0    |   0.12.12    |   0.11.10    |
|    19.8.0    |   18.12.1    |   0.12.11    |    0.11.9    |
|    19.7.0    |   18.12.0    |   0.12.10    |    0.11.8    |
|    19.6.1    |   16.20.1    |    0.12.9    |    0.11.7    |
|    19.6.0    |   16.20.0    |    0.12.8    |    0.11.6    |
|    19.5.0    |   16.19.1    |    0.12.7    |    0.11.5    |
|    19.4.0    |   16.19.0    |    0.12.6    |    0.11.4    |
|    19.3.0    |   16.18.1    |    0.12.5    |    0.11.3    |
|    19.2.0    |   16.18.0    |    0.12.4    |    0.11.2    |
|    19.1.0    |   16.17.1    |    0.12.3    |    0.11.1    |
|    19.0.1    |   16.17.0    |    0.12.2    |    0.11.0    |
|    19.0.0    |   16.16.0    |    0.12.1    |    0.9.12    |
|   18.11.0    |   16.15.1    |    0.12.0    |    0.9.11    |
|   18.10.0    |   16.15.0    |   0.10.48    |    0.9.10    |

This is a partial list. For a complete list, visit https://nodejs.org/en/download/releases

# 从上面选择一个你需要的版本安装
nvm install 16.14.0
nvm install 14.13.3

# 安装成功后， nvm list 查看已安装版本
nvm list
>>
  16.14.0
* 14.15.3 (Currently using 64-bit executable)

# 切换node版本
nvm use 16.14.0
>>
  Now using node 16.14.0（64 bit）
  
# 验证是否切换成功
node -v
npm -v
```

这样就可以实现node版本控制了。 



# 最后

**需要注意的是**，此时只是切换了系统中的node环境变量，此时你的开发编辑器中的需要 <u>手动切换</u> 一下。 

nvm使用 *symlink* 实现node版本控制， 在安装nvm的时候，会让你指定一个 *symlink* 的本地文件路径， 这个 *symlink* 会指向你当前 `nvm use ` 版本的node根目录； 打开电脑的环境变量配置其实可以看见 `NVM_SYMLINK` ， 这个其实就是你的nodejs环境变量，只不过是由nvm进行管理的。

*symlink* - 符号链接，属于软连接

只需要在编辑器中设置 `NVM_SYMLINK` 指向的路径即可， 如我的路径是 `C:\Program Files\nodejs` 



---



以上。

By Peilin

--- ***Life Oriented Programming*** ---

