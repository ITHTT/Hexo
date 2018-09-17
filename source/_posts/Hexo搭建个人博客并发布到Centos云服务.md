---
date: 2018-05-07 20:55
status: public
title: Hexo搭建个人博客并发布到Centos云服务
tags: hexo
---

# 1.在本机电脑（Windows或Mac）上Hexo安装
Hexo是一个基于Node.js的开源的博客框架，Hexo使用Markdow（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。安装Hexo，需要先安装**Node.js**和**Git**。
- **Node.js** 下载地址：<https://nodejs.org/en/>
- **Git** 下载地址：<https://git-scm.com/>

安装Node.js和Git后，即可通过npm来完成Hexo的安装，代码如下：
``` 
$ npm install -g hexo-cli
```
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
新建完成后，指定文件夹下生产如下文件
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
Hexo文档地址：<https://hexo.io/zh-cn/docs/>
另外，可以自定义主题，这里将使用的是**next**。Next文档地址：<http://theme-next.iissnan.com/>
这里主要讲一下Hexo自动部署到Centos云服务器的配置，在hexo生成的配置文件**_config.yml**中，修改deploy部分的参数，例如：
```
deploy:
  type: git		
  repository: root@ithtt.xyz:/usr/myblog/hexo/hexo.git
  branch: master		
  message: hexo deploy
  ```
**type: git**:表示提交类型为Git
**repository**:为在Centos服务器上创建的提交仓库的地址。主要是通过SSH的方式，在这里的仓库地址是：
```
root@ithtt.xyz:/usr/myblog/hexo/hexo.git
```
**root**：是Centos云服务器上登录的用户名;**ithtt.xyz**:是主机域名或IP地址;**/usr/myblog/hexo/hexo.git**:是在Centos云服务器上创建的Git仓库地址目录路径。
另外，通过Git方式来部署，需要通过npm来安装部署工具[ hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)：
```
$ npm install hexo-deployer-git --save
```
然后在Hexo安装的当前目录下，执行命令：
```
$ hexo deploy
```
在部署之前，生成静态文件，可以先执行：
```
 hexo generate
 ```
这样便可以把生成的静态文件部署到在Centos指定的仓库地址目录下。
# 2.在Centos云服务器上生成用于部署Hexo的Git仓库
首先需要在Centos上安装Git。可通过**yum**指令安装。
```
yum install git
```
安装git成功后，可以通过git指令查看git版本，验证是否安装成功
```
yum --version
```
安装git成功后，执行如下指令创建Git仓库
```
mkdir /usr/myblog/hexo
cd /usr/myblog/hexo
git init --bare hexo.git
```
另外，假如需要将git仓库共享出来，被其他人使用，可以使用**git-daemon**将Git仓库变成共享的资源库。
```
yum install git git-daemon
```
安装git-daemon。然后执行以下命令，将创建的仓库共享出来：
```
git daemon --reuseaddr --base-path=/usr/myblog/hexo --export-all --verbose --enable=receive-pack &  
```
创建好git仓库后，则可以在安装Nginx服务器，将仓库目录配置到Nginx中
# 3.安装Nginx并配置Hexo博客的访问地址
Nginx介绍（百度百科）：<https://baike.baidu.com/item/nginx/3817705?fr=aladdin>
Nginx的安装及配置，可参考这篇文章：<https://www.cnblogs.com/taiyonghai/p/6728707.html>
在这里Nginx配置博客访问如下：
```
server {
        listen       80;
        server_name  ithtt.xyz
        location / {
            root   /usr/myblog/hexo;
            index  index.html index.htm;
        }
}
```
**listen**:端口号
**server_name**:域名或服务器IP地址
**location里的root**:是访问的页面资源的目录地址。


