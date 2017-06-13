---
title: 用hexo和github搭建独立blog
date: 2017-06-13 10:29:28
tags: [hexo,github,yilia]
---

用hexo搭建独立blog，本地预览，并推到github用github pages托管静态页面

## 安装nodejs

[nodejs官网](https://nodejs.org/en/)

#### 验证安装

``` bash
node -v
npm -v
```

## 安装git

[git官网](https://git-scm.com/downloads)

## 安装hexo

首先进入到希望存放blog源文件的目录

``` bash
$ npm install hexo-cli -g
$ hexo init
$ npm install
$ hexo g
$ hexo s
```

安装hexo，初始化了一个blog，并部署在了local server上，可以在http://localhost:4000 查看

#### hexo的基本操作和命令

* `hexo generate (hexo g)`，根据源md文件生成静态html文件到/public/中
* `hexo server (hexo s)`，部署在本地web server，用于实时预览
* `hexo deploy (hexo d)`，将/public/中的静态资源推到远端
* `hexo new (hexo n) "title"`，创建一篇文章
* `hexo new page "title"`，创建一个页面
* `hexo clean`，清空/public/中的静态资源

#### hexo主要文件结构

> /source/ 存放最原始的源md文件和其他源文件
> /public/ 存放由源文件生成的静态web文件，`hexo d`将这个文件夹推到远端
> /themes/ 主题

## hexo主题yilia

``` bash
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

更改hexo根目录下的_config.yml:

```
theme: yilia
```

### search sidebar模块

``` bash
npm i hexo-generator-json-content --save
```

根目录下_config.yml加入：
```
jsonContent:
    meta: false
    pages: false
    posts:
      title: true
      date: true
      path: true
      text: false
      raw: false
      content: false
      slug: false
      updated: false
      comments: false
      link: false
      permalink: false
      excerpt: false
      categories: false
      tags: true
```

## github pages配置

注册github与配置github ssh，不是此文重点，略过。

创建一个repository，进入repository的settings，在GitHub Pages下面，选择一个branch，github的配置就完成了。

看了一些教程，都说repository名字必须是`<username>.github.io`，其实并不完全对。

Github Pages分为三种：User， Organization，Project Pages。

User和Organization Pages要求repository以`<username>.github.io`和`<orgname>.github.io`命名，repository名就是访问的域名，但是静态web资源必须放在master branch上，不能选择其他分支。

Project Pages可以任意命名repository，访问的域名是`username.github.io/repositoryname`或`orgname.github.io/repositoryname`，取决于这个repo属于个人还是组织，静态web资源可以放在master，gh-pages分支或者master的/docs文件夹中。

我选择用Project Pages，分支使用起来更灵活，gh-pages用来存放生成web页面，master用来存放所有的源文件，方便多个设备同时管理blog。至于域名的问题无所谓了，反正后面都会用我自己的域名来做CNAME。

reference：<https://help.github.com/articles/user-organization-and-project-pages/>

#### deploy到github pages

到此已经配好github ssh，github pages，创建了repository，以及gh-pages分支来存放静态页面。

先安装
``` bash
$ npm install hexo-deployer-git --save
```
修改_config.yml
```
deploy:
  type: git
  repo: git@github.com:hk215/hkblog.git
  branch: gh-pages
```

`hexo g`生成、更新/public/静态资源

`hexo d`将/public/的所有静态资源推到gh-pages里

#### github pages绑定个人域名

1. 在个人域名提供商添加一个域名解析CNAME Record到`<username>.github.io`
	> 我的域名是`huangkai.us`（提供商namecheap），想用`blog.huangkai.us`作为我的博客地址，于是登录namecheap，添加一个CNAME Record，`Host：blog`，`Value：hk215.github.io`
2. 在本地hexo跟目录下的/source/下创建一个文件CNAME，将`blog.huangkai.us`域名写进去
	> 在github settings里也可以设置Custom domain(原理也是生成CNAME文件)，但是每次用`hexo g`和`hexo d`重新生成页面与推到github的时候，会自动把CNAME删掉，原因是本地hexo并不知道设置了CNAME。
	> 所以正确的做法是把CNAME放在/source/下，每次`hexo g`都会把CNAME放到/public/里，然后`hexo d`将/public/推到远端的时候就可以保证CNAME一直存在了。
