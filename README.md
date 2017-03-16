## 初始配置

* `git config --global user.name "Your Name"`，`git config --global user.email "email@example.com"`用来配置用户名和邮箱，一般在第一次操作时会提醒配置。
* `ssh-keygen -t rsa -C "youremail@example.com"`执行后默认会在`C:\Users\Administrator\.ssh`下生成几个文件，只要将其中的`id_rsa.pub`中的内容复制
到（gitlab，github）对应配置ssh的地方保存后就可对有权限的仓库进行代码的push操作。

## 创建仓库
##### 先本地后远程
* `git init`将当前文件夹初始化为git本地仓库
* `git remote add origin git@server-name:path/repo-name.git`要关联一个远程库(`git remote` 要查看远程库的信息，或者，用`git remote -v`显示更详细的信息)
* `git push -u origin master` 第一次推送master分支的所有内容；此后，每次本地提交后，只要有必要，就可以使用命令`git push`推送最新修改；

##### 先远程后本地
* `git clone git@server-name:path/repo-name.git` 将远程仓库克隆到本地

## 代码的提交

* `git add file`将工作区的代码提交到暂存区，在项目多文件提交是可以使用`git add .`,`git add src/.`其中（.）表示文件夹下的所有文件。
* `git commit -m '注释'`将暂存区中的代码提交到本地仓库。
* `git push`将本地仓库的代码修改提交到远程仓库，可以指定提交的仓库，默认为origin

## 查看历史和状态

* `git status` 命令可以让我们时刻掌握仓库当前的状态，但是看不出修改了什么
* `git diff` 用来查看修改的细节`git diff HEAD filename`查看工作区和版本库里面最新版本的区别
* `git log` 命令显示从最近到最远的提交日志，`--pretty=oneline`参数可简化内容，`--graph`可以看到分支合并图,`--abbrev-commit`筛选出提交
* `git reflog` 用来记录你的每一次命令，当你用`git reset --hard HEAD^`回退到当前版本之前的版本时，再想恢复到当前版本，就必须找到对应commit id

## 关于git reset

* `git reset --hard  id` 回退版本，其中id为commit id（通过查找历史课查看），也可以用HEAD（其实为指针）表示当前版本，
上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。
* `git reset HEAD file` 可以把暂存区的修改撤销掉（unstage），重新放回工作区

1. 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
2. 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
3. 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。


## 关于git checkout

* `git checkout filename`把filename文件在工作区的修改全部撤销,情况一：自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
情况二：已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态
`git checkout` 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

## 关于git stash

* `git stash` 可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作
* `git stash apply` 恢复，但是恢复后，stash内容并不删除
* `git stash drop` 来删除stash内容；
* `git stash pop` 恢复的同时把stash内容也删了
* `git stash list` 查看，然后恢复指定的stash，用命令： git stash apply stash@{0}

## 分支操作

* `git branch` 查看分支
* `git branch <name>` 创建分支
* `git checkout <name>` 切换分支
* `git checkout -b <name>` 创建+切换分支
* `git merge <name>` 合并某分支到当前分支
* `git branch -d <name>` 删除分支

## 关于标签

* `git tag` 查看所有标签
* `git show <tagname>` 查看标签信息创建带有说明的标签，用-a指定标签名，-m指定说明文字：`git tag -a v0.1 -m "version 0.1 released" 3628164`
* `git tag -s <tagname> -m "blablabla..."`可以用PGP签名标签
* `git push origin <tagname>`可以推送一个本地标签；
* `git push origin --tags`可以推送全部未推送过的本地标签；
* `git tag -d <tagname>`可以删除一个本地标签；
* `git push origin :refs/tags/<tagname>`可以删除一个远程标签

## 常见问题及解决方法

* A从B那fork了一个项目，之后A对派生过来项目进行了修改，B对自己的项目也进行了修改，一段时间后A想合并B的最新代码怎么办？

> fork后的项目一般会clone到本地进行修改，在克隆的过程中确实就已经将你本地仓库可你fork后的项目进行了管理
> 也就是执行了 `git remote add origin git@server-name:path/repo-name.git`(通过`git remote`可以查看你本地仓库关联的远程仓库) 
> 此时只要执行 `git remote add oor git@server-name:path/repo-name.git` 关联B的原始项目仓库，再执行`git pull oor`将B修改过后
> 的代码更新到本地，然后在origin master分支上执行`git merge oor/master`就可以合并B修改后的代码了（主要在合并操作请需要将本地修改的代码add，commit）
> 最后解决冲突提交即可。

* 在git pull时出现`ssh: connect to host 114.55.92.162 port 22: Connection timed out fatal: Could not read from remote repository....`

> `vi /etc/ssh/sshd_config` 编辑配置文件，将port 22和protocol 2的注释去掉可以解决问题。

* 在git pull时提示`no tracking information`

> 说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream branch-name remote-name/branch-name`例如`git branch --set-upstream dev origin/dev`