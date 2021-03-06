title: 建站流程
tags:
  - 运维
categories: []
date: 2020-02-17 22:13:00
---
# 博客站点建立流程  
## 设置到的知识内容
1. Linux 命令操作
2. 域名相关知识
3. nginx配置
4. [Hexo](https://hexo.io/)框架
5. git相关知识
6. [Jenkins](https://jenkins.io)自动CI

## 简单说明
利用hexo框架，在现有**自己的服务器**和**自己的域名**下，在 服务器上配置博客并且用jenkins自动完成ci流程。

## 特色
（适合我这种后端不满足于一键自动的）
- 不同于其他git托管的形式
- 完成自己的自动ci流程


## 正文

### 1. 前期准备环节  
&emsp; 首先需要有一台服务器（和一个自己的域名）  
    &emsp;没有域名的话可以用端口  
    &emsp;如何创建服务器和登录服务器不在此处说明

### 2. 安装环境环节  
 &emsp; 我自己用的是centos 8.0，包管理工具是dnf，centos7以及以下的版本可以使用yum，debian系的可以用apt，如有操作系统区别，请自行替换  

- 安装 nginx   
`dnf install -y nginx`  
- 安装 git  
`dnf install -y git`
- 安装 jenkins  
`dnf install -y jenkins`  
如果安装慢的话建议从[清华源](https://mirrors.tuna.tsinghua.edu.cn/jenkins/)下载rpm包（或者deb包）  
安装方式同理  
`dnf install -y jenkins.rpm`  
- 安装node(npm同时会被安装)  
`dnf install -y nodejs`  
&emsp; 安装hexo `npm install -g hexo-cli`

### 3.构建博客环节  
&emsp; 这一节简单的说明下，因为这篇重点并不是普通的安装hexo框架流程

- 初始化  
`hexo init blog`
- 安装依赖  
`npm install`
- 运行demo  
`hexo server`缩写`hexo s` 能看到 localhost:4000 就说明成功了

- 初始化git仓库 并且上传  
&emsp; 这里假设git仓库url为  
&emsp;https://github.com/user/resp

### 4.配置服务器

- 服务器配置clone仓库代码  
`git clone https://github.com/user/resp.git`

- 手动构建  
假定当前目录为/path/resp/  
在该目录下为代码,服务器上执行  
`hexo generate` 缩写 `hexo g`
在当前目录下创建 public目录  
路径/path/resp/public/ 下就有nginx需要的index.html

- 配置nginx  
在我们的服务器/etc/nginx/conf.d/下创建一个叫做blog.conf的配置文件用于运行blog网站  
示例如下  
```conf
server {
	listen 80;
	ignore_invalid_headers off;
	server_name 你的域名或者ip;
	location / {
		root /path/resp/public;
		index index.html;
		access_log /root/hexo-access.log;
		error_log /root/hexo-error.log;
	}
}
```

配置完成之后用
`nginx -t`测试配置文件是否正确，然后重启nginx  
在能访问到你的ip或者域名的情况下用浏览器打开应该就能访问站点了

### 5. Jenkins 自动CI
参考链接  
[通过jenkins持续集成 github中的代码到服务器](https://www.jianshu.com/p/9db976b675b2)

构建的shell脚本
```bash
cd /path/resp
hexo clean
git pull
npm install --registry=https://registry.npm.taobao.org
hexo g
```