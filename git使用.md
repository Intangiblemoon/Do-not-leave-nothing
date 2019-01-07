#GIT使用
##命令作用
###git add
- git add . 
监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。

- git add -u

监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区。add -u 不会提交新文件（untracked file）。（git add --update的缩写）
- git add -A
监控状态树的所有变化，包括增加修改删除（git add --all的缩写）



##使用场景
###使用GIT上传一个本地工程至远端仓库
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