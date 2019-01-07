#GIT使用
##使用GIT上传一个本地工程至远端仓库
>git init

为工程初始化本地git仓库

>git remote add origin http://ip:port/pro_demo/demo.git

映射至远程仓库

>git add .

添加文件至缓存区（被删除的文件状态不会添加）

>git commit -m "msg"

提交文件至本地仓库

>git push -u origin master

提交本地改动至远端