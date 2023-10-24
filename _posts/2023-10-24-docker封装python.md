---
layout:     post
title:      windows系统下利用docker封装python
subtitle:   docker封装
date:       2023-10-24
author:     rain
header-img: img/the-first.png
catalog:   true
tags:
    - issue
---
### 1 Docker安装（Windows版）
 **1.1 准备环境**
至于为什么使用Windows版，我觉得使用虚拟机太麻烦了，而且下载虚拟机安装各种系统占用空间大，更主要的是我的虚拟机根本安装不下来很烦人，对他印象不好

条件：
1.必须运行Windows10版本2004及更高版本或Windows11
2.在下图的位置开启虚拟化，一般是开启的，最好看一眼，预防万一

![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/274463075-7eaca5eb-503f-4ac4-b3d2-373d0f9f64cb.png)


管理员的身份启动命令窗口，输入：`wsl --install`

可能下载的很慢，耐心等待，尽量不要让他中途终端（保持网络良好），如果看着好久没动了，可以按几下回车试试


![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/2.png)
安装完成
![image]([https://github.com/Tylen11/blog/assets/74086157/d2b04a7a-afe5-4ca0-a992-b7d35aabb17c](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/3.png))



记得重启一下电脑！！！

再次管理员打开命令窗口，输入下面的命令

`wsl --install -d Ubuntu`
可能会有点慢 ，长时间没反应就回车一下

![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/4.png)


输入用户名和密码（我这里输入用户名和密码的时候出了一点意外）

用户名：zhangjingqi

密码：123456（输入密码的时候不会显示，自己把握好就行了）
![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/5.png)
![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/6.png)

###  2 安装windows版本的docker-desktop
网址：

[https://desktop.docker.com/win/stable/Docker%20Desktop%20Installer.exe](https://desktop.docker.com/win/stable/Docker%20Desktop%20Installer.exe)

下载完成之后双击运行

- 下图的意思是添加桌面快捷方式，可以加也可以不加，看个人爱好，然后点击OK
- 
- 
- ![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/7.png)

![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/8.png)

 

下面提示安装成功，重启电脑，点击蓝色的就好
![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/9.png)


被迫接受条款

![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/10.png)


 

出现下面这个界面表示安装成功了


![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/11.png)

 

2.1 修改镜像
![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/12.png)

```
{
    "registry-mirrors" : [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn",
    "https://cr.console.aliyun.com",
    "https://mirror.ccs.tencentyun.com"
  ],
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "features": {
    "buildkit": true
  }
}
```

一直转转转，等一会就可以了，不转了就可以了
![image](https://raw.githubusercontent.com/rain966/rain966.github.io/master/img-post/docker%E5%B0%81%E8%A3%85/13.png)

### 3 docker容器环境搭建
**3.1 ubuntu基础镜像选择**
（1） [docker hub](https://hub.docker.com/_/ubuntu)镜像选取

（2）ubuntu 终端拉取ubuntu:20.04基础环境
`docker pull ubuntu:20.04`

（3）以ubuntu系统搭建ubuntu系统容器
`docker run -it ubuntu:20.04 /bin/bash`

**3.2 安装conda**

（1）下载最新版conda（注：是linux版本）
[https://docs.conda.io/projects/miniconda/en/latest/](https://docs.conda.io/projects/miniconda/en/latest/)

（2）将下载好的conda.sh文件拷贝到新建的ubuntu容器内
注：
a、容器id用`docker ps`查看

b、/mnt/d/路径为宿主机（windows）本地路径

`docker cp /mnt/d/downloads/*.sh 容器id:/home/`

（3）安装 conda

- 进入新建的ubuntu容器内
`docker exec -it 容器id bash`

- 安装Miniconda3-latest-Linux-x86_64.sh
`sh Miniconda3-latest-Linux-x86_64.sh`

- 回车、连续空格，yes
  
- 配置环境变量`vim ~/ .bashrc`

添加一行 
`export PATH=/root/miniconda3/bin$PATH`
`:wq`
`resource ~/ .bashrc`

- 重启容器
`docker exec -it 容器id bash`

- 安装python
`conda create -n mypy3.18 python=3.8`

- 环境激活
`conda activate mypy3.8`

- 查看python版本
`python -V`

（4）将搭建好的容器环境生成新的镜像

`10.25.23.1:31001/kty/nas-sim:0.1.0`

安装完运行环境后，不要关闭容器，使用命令“docker ps”查看容器id，再使用命令“`docker commit -m="add env" -a="ztx" xxxxxxxx pca:test2`”将容器保存为新镜像
-m="add env"为提交时的说明文字，-a="ztx"为提交的镜像作者，xxxxxxxx为查出的容器id，pca:test2为容器名和版本号
（5）导出镜像包

使用命令“`docker save -o pca-test2.tar.gz pca:test2`”将镜像pca:test2保存至本地pca-test2.tar.gz为包名称；pca:test2为容器名和版本号

（正文内容）
