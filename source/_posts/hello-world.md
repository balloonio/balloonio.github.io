---
title: Hello world
tags:
  - hexo
  - nexT
  - blog
  - techstack
  - front-end
  - web
categories:
  - - TechStacks
date: 2018-10-30 08:00:36
---


搭建博客后的第一篇日志,按照业内惯例,自然是要记录下搭建的过程. 本文旨在用最简短的篇幅教你用hexo framework和github快速搭建博客. 本文基于macOS.

hexo是一个静态网站生成框架,简单来说,就是一个source code generator. 一个网站需要html css js这些元素才能被加载,而hexo所做的就是根据你所写的markdown格式日志和选择的主题,直接生成这些文件,免去了自己写html css js这样的麻烦. 哪怕你对网页前端一窍不通,也不要紧.

# 下载并安装Node.js与hexo

  由于hexo基于Node.js,第一步直接去官网下载并安装Node.js.  

  安装完Node.js后,使用`npm`安装hexo
  ```
  $ npm install -g hexo-cli
  ```
  验证安装成功
  ```
  $ hexo -v
  ```
  如果安装时遇到permission error,使用`sudo`.

# 生成网站source文件

  选择一个directory生成网站source文件,如果使用`GitHub Pages`,这里推荐创建source branch存放站点source文件,后面会说明原因
  ```
    > /Volumes/HDD/Developer/github
   $ hexo init blogsrc
   $ cd bolunzhang.github.io
   $ git checkout -b source
   $ cp -r ../blog/* ./
  ```
  注意添加`.gitignore`为以下
  ```
  node_modules/
  *.log
  db.json
  public/
  .deploy*/
  package-lock.json
  ```

# 测试站点

  至此为止其实已经有了一个可用的站点源文件,我们只需要跑以下命令,就可以在浏览器localhost:4000看到刚刚创建的基于landscape默认主题的网站了
  ```
  $ hexo server
  ```
  这个命令就是根据刚刚生成的站点源文件,再一次转化为html css js等网页文件的形式部署在了本地服务器上. 如果好奇这些网页文件都有什么,可以跑以下命令生成`public/`进行查看
  ```
  $ hexo generate
  ```

# 部署站点到GitHub Pages

  ## 安装`hexo-deployer-git`
  ```
  $ npm install hexo-deployer-git –save
  ```

  ## 编辑`_config.yml`网站配置文件,修改deployment参数  
  {% codeblock lang:yml %}
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
  type: git
  repository: git@github.com:bolunzhang/bolunzhang.github.io.git
  branch: master
  {% endcodeblock %}
  设置好之后,每次只需要跑以下命令,站点就会被直接部署到对应的GitHub Pages repo的master branch
  ```
  $ hexo deploy
  ```
  这时再进入master branch就可以看到部署的所有的网页文件了.

  ## 自定义域名 (Optional)
  如果你有自定义域名指向GitHub Pages的话,其必须的`CNAME`文件会被deployment覆覆盖掉. 解决办法是将`CNAME`文件复制到网站源文件,也就是source branch,下的source文件夹内.这样hexo部署站点的时候会一并将`CNAME` push到master branch