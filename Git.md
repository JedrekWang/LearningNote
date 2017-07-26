# 介绍如何将本地的项目通过Git上传到GitHub远程仓库中

* 首先在本地的项目中添加Git配置文件，使得Git可以来控制文件
    > cd <本地文件目录>
    > git init
* 将本地的项目的改动添加到缓存区，并提交修改
    > git add
    > git commit -m "为这次提交写相关的文档"
* 将本地项目与远程仓库建立连接
    > git remote add origin git@github.com:<GitHub用户名>/<Github仓库名>.git
* 如果遇到 remote origin already exists,可以先删除当前连接然后在建立连接
    > git remote rm origin
* 最后上传
    > git push -u origin master

### by Jedrek