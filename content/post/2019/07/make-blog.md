---
title: "Hugo搭建Githug静态Blog"
date: 2019-07-25T16:45:53+08:00
tags: ["Hugo"]
categories: ["杂技"]
---

一直都有搭建blog的想法，最近研究了静态博客系统。主要想法是依赖于github，其中有两个github pages和github issues思路。

那问题了来了，什么是github pages？ github pages是github提供一个在线服务，可以将github仓库里面的静态资源（html、markdwon）渲染成也页面，帮你生成一个二级域名，甚至可以绑定自己域名。所以使用pages的人最多。为什么有些框架可以使用github的issues能构建出漂亮的静态站点，甚至动态的呢？框架拉取你的issues信息，并转化成html保存到github上，然后在使用pages服务，具体如何使用，这里有介绍<https://www.v2ex.com/amp/t/566714>。更有一些激进的人直接issues系统，只做一些记录。我最终的选择直接pages服务来搭建，

大方向选定以后，开始选中框架，我喜欢一些从简，所以我选择hugo，编写工具使用Typora，图床使用阿里的oss，毕竟国内访问的多。 下面就开始我的搭建之路。



具体安装过程，参考<https://www.gohugo.org>，下面我只说我才过的一些坑，和我的系统构架。

# git仓库构建

## 仓库目录结构

``` go
   drwxr-xr-x  15 hexin  staff   480B  7 25 14:13 .
   drwxr-xr-x  18 hexin  staff   576B  7 25 13:21 ..
   drwxr-xr-x  14 hexin  staff   448B  7 25 16:28 .git
   -rw-r--r--   1 hexin  staff   100B  7 25 13:43 .gitmodules
   -rw-r--r--   1 hexin  staff     0B  7 25 14:13 .nojekyll
   drwxr-xr-x   3 hexin  staff    96B  7 25 14:02 archetypes
   -rw-r--r--   1 hexin  staff   615B  7 25 14:01 config.toml
   drwxr-xr-x   3 hexin  staff    96B  7 25 14:05 content
   drwxr-xr-x   2 hexin  staff    64B  7 25 13:21 data
   -rwxr-xr-x   1 hexin  staff   436B  7 25 14:13 deploy.sh
   drwxr-xr-x  19 hexin  staff   608B  7 25 13:21 docs
   drwxr-xr-x   2 hexin  staff    64B  7 25 13:21 layouts
   drwxr-xr-x   3 hexin  staff    96B  7 25 13:49 resources
   drwxr-xr-x   2 hexin  staff    64B  7 25 13:21 static
   drwxr-xr-x   3 hexin  staff    96B  7 25 13:43 themes
```

为了简单，我直接将真个仓库全部push到github，这样我本地文件和github完全一致，hugo官方正式将生成好的放到github，这样github缺少好多文件。pages服务是支持使用master分支的docs目录，只要我将最后生成html页面保存到docs里面，理论上是可行的。 

![github page](https://hexinme-blog.oss-cn-beijing.aliyuncs.com/blog/img/20190725141630.png)


config.toml 文件增加

> publishDir = "docs"

# 发布&部署

部署需要经历2步，先生成html，然后push到github。有些人借助github提供的ci服务，有些小题大做。我们只用一个脚本搞定。

deploy.sh 脚本

``` sh
#!/bin/sh

# If a command fails then the deploy stops
set -e

printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`


# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site $(date)"
if [ -n "$*" ]; then
        msg="$*"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master
```

#  遇见一些错误
##  Page build failure

>
The page build failed for the `master` branch with the following error:
>
Unable to build page. Please try again later.
>
For information on troubleshooting Jekyll see:
>
https://help.github.com/articles/troubleshooting-jekyll-builds

因为我没有使用Jekyll服务，使用的hugo。我们只用仓库根目录下面创建“.nojekyll”空文件即可。


# 后续更新如何开始https支持