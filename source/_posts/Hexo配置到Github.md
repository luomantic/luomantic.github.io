---
title: Hexo配置到Github
date: 2018-06-20 10:47:55
tags: hexo
---

### 用hexo配置github-pages的步骤：
* 配置环境，win10 + nodejs + git + hexo
* github创建名为 username.github.io的仓库，用来存放hexo网页文件
* 绑定域名：先暂时不绑定
* 配置SSH key [百度：github配置ssh] [https://blog.csdn.net/u012984054/article/details/60423274](https://blog.csdn.net/u012984054/article/details/60423274 "github配置ssh")
* 选择存储博客的本地文件夹:`hexo init, hexo g, hexo s.`
* 然后修改主题跟_config.yml文件,然后`hexo clean,npm install hexo-deployer-git --save, hexo d`。到这里就上传到github仓库完成了。

### 注意事项：
* 修改_config.yml的正确格式，并且每个分号后面都要带一个空格。
> deploy:  
  type: git  
  repository: git@github.com:luomantic/luomantic.github.io.git  
  branch: master

* 所有的命令最好在git bash里面执行，cmd里面可能有些命令会没有权限。
* 如果有3个hexo项目，那么每个hexo文件夹下面都要执行`npm install hexo-deployer-git --save`，因为这个安装命令只对当前文件夹生效。