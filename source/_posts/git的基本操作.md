---
title: Git的基本操作
tags: hexo
---

1.设置用户名

git config --global user.name '仓库名'

 </br>

2.设置用户名邮箱

git config --global user.email 'matchlessherovip@163.com'

  </br>

3.查看设置

git config --list

  </br>

4.初始化

git init

  </br>

5.查看状态

git status

 </br>

6.创建文件

touch test.java
 
 </br>

7.将文件添加到暂存区

git add test.java

  </br>

8.将文件添加到仓库

git commit -m'描述信息'

 </br>

8.1将本地仓库代码提交到远程仓库

git push

         1.先从远程仓库中clone代码到本地

         git clone https://github.com/MatchlessHeroVIP/ssmtest.git

 
 </br>
 

9.向文件中写内容

 vi或者是vim test.java

 </br>

10.查看文件内容

cat test.java

  </br>

11.删除文件

rm -rf test.java

  </br>

12.将文件从暂存区中删除

git rm test.java

  </br>

13.将文件从仓库中删除

git commit -m'描述消息'

  </br>

14.查看设置

git config --list

  </br>

15.查看当前文件夹里的文件

ls

  </br>

16.设置权限问题

将.git/config里的

[remote "origin"]

   url = https://github.com/用户名/仓库名.git

修改为：

[remote "origin"]

   url = https://用户名:密码@github.com/用户名/仓库名.git

 </br>
==================================================================

17、将本地仓库关联到GitHub仓库上

 

执行：git remote add origin https://github.com/heidao10duan/myLrose

  </br>

18、将GitHub仓库更新到本地

 

执行：git pull origin master

 

说明：因为可能GitHub仓库上可能有些变更，需要先同步到本地，才能将本地的改变提交到GitHub仓库上

  </br>

19、将本地仓库同步到GitHub仓库

 

执行：git push origin master

 
 </br>
 

解决github里的readme文件没有下载下来的问题

$ git pull --rebase origin master

 

 

然后执行

git push origin master

即可成功把本地的文件都上传到github上面去了。

  </br>

20.此时再去github网站上也就可以看到对应文件了