---
title: hexo的安装及使用
date: 2019-05-17 16:21:19
tags:
---

### 下载node.js



###  安装博客框架

1. 安装cnpm npm install -g cnpm --registry=https://registry.npm.taobao.org
2. cnpm install -g hexo -cli

<!--more-->

### 生成博客

1. 创建博客的文件夹

2. 在文件夹中打开cmd,输入hexo init

### hexo生成

1. hexo n “标题”(生成的博客.md文件在source中)
2. 清理:hexo clean 
3. 生成:hexo g

4. 开启博客:hexo s

### 部署到远端

1. 在github中新建项目(用户部署个人博客的github仓库的命名必须是自己的昵称Bender65535.github.io)

2. 安装git的部署插件:cnpm install --save hexo-deployer-git
3. 设置_config.yml(注意空格)
   1. type: git
   2. repo :https://github.com/Bender65535/Bender65535.github.io.git
   3. branch: master
4. 部署到远端:hexo d



### 自定义主题

1. 主题: https://github.com/litten/hexo-theme-yilia

2. 在博客文件中输入 git clone https://github.com/litten/hexo-theme-yilia.git thems/yilia

3. 加载主题
   1. 改变_config.yml中的themes为主题的文件名
    2. hexo clean
    3. hexo g

4.主题推送远端: hexo d