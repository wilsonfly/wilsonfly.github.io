




####创建远端分支：
#####一：以当前分支为基础
git branch  new-branch-name  
git checkout  new-branch-name
//说明：如果当前在某一个分支上，则新建的分支继承此分支上的文件。如果想新建一个空的分支，可以从不在任一分支上的情况下删掉当前目录下所有文件，然后切到新分支上。  
git push  remote-branch-name   new-branch-name
//说明：一般会给远端分支起了个简短的别名，可以从 .repo/manifest.xml中remote name 看到，举例如下：   
例一：  
sunhuasheng@ubuntu:~/projects_code/sub_system_8040C/iptvmw$ git br -a  
* 8040C-emmc-zj-cu  
  remotes/**iptvmw**/2530-base  
  remotes/**iptvmw**/2540B_ChinaMobile  
  remotes/**iptvmw**/8000A-emmc-base  
例二：  
sunhuasheng@ubuntu:~/projects_code/sub_system_8040C/platform/release$ git br -a  
* master  
  remotes/m/8040C-emmc-zj-cu  
  remotes/platform/8000A-emmc-zj-ctc  
  remotes/platform/master  
#####二：新建空仓库  
mkdir on-project       
git init .  
git remote  add platform ssh://git@svn.sys.sunniwell.net/android-xxx.git/xxx/hi3716-xx-project.git
//cat platform/on-project/.git/config 而来  
git remote -v     // 可以查看对应的远端信息了  
touch .gitignore  
git add .gitignore  
git commit -m "test file"     // 需要有文件才能上传，才能建分支  
git push platform master  
//仓库创建完成，创建新分支  
git branch test_branch  
git checkout test_branch  
git push platform test_branch   // 完成空分支的创建  


####删除远端分支：  
git push  platform   :test_branch  
以某分支为基础提交新分支：  
git push  suniwell   develop:new_branch  


####配置颜色
git config --global color.status auto  
git config --global color.diff auto  
git config --global color.branch auto  
git config --global color.interactive auto 

####常用命令
git log  -2 swvdec/swvdec.c  
git log --stat -2  
git log -p  
git log --committer=sunhuasheng  
git log --author=sunhuasheng  
repo forall -c  git pull  
repo forall -c  "pwd;git branch"  
git diff HEAD^ HEAD swavstage/avporting.c  
git diff f75a756fba44a5354ad2:hi3716M/swavstage/avporting.c  e43e1d4915bcc760f437:hi3716M/swavstage/avporting.c  
git diff f75a756fba44a5354ad2:hi3716M   e43e1d4915bcc760f437  hi3716M/swavstage/avporting.c  
git remote show origin  
// 新建本地分支，未与远端分支挂钩   
git checkout -b tmp  
// 新建与远端分支挂钩的本地分支， 建议用第二条  
git checkout   -b my8845B   origin/8845B  
git checkout              --track    origin/8845B  
// 将本地分支tmp，合并到当前分支  
git merge tmp  
// commit 后发现冲突，reset一下  
git reset --hard HEAD^  
//git add -A 可以将删除动作一并纳入管理，在commit时候一并提交  
git add -A: [path]表示把中所有tracked文件中被修改过或已删除文件和所有untracted的文件信息添加到索引库。  



###svn部分:

svn log -v   //查看修改的文件  
svn log -r311 -v //查看311提交版本的修改文件  
svn diff  -r310:311  //查看310到311版本的修改差异  
svn diff  Makefile   -r310:311   //查看具体文件在310到311版本的修改差异  


####常见问题：
*1. repo init 过程中报错如下  
====id_rsa 权限太大，解决： chmod 600 id_rsa  
![svn_001](res/svn_git_gerrit/svn_001.png)  

*2. repo init 过程中报错如下：  
解决：执行红框中两个config即可  
![svn_002](res/svn_git_gerrit/svn_002.png)

