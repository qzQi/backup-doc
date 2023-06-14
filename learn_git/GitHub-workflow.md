## GitHub工作流概述

GitHub工作流是一种常用的团队协作开发模式，它采用分支管理的方式，将开发人员的修改隔离开来，从而避免不同开发者之间的代码冲突和干扰，确保代码的可维护性和稳定性。

## GitHub工作流步骤

1. **Clone远程仓库并拉取需要修改的分支**

   使用`git clone`命令将远程仓库克隆到本地，然后使用`git switch`命令切换到需要修改的分支。例如：

   ```bash
   git clone git@github.com:username/repo.git
   git switch -c feature01 origin/feature01
   ```

2. **创建本地特性分支并进行开发**

   使用`git switch -c`命令创建本地特性分支，并在该分支上进行开发工作。例如：

   ```bash
   git switch -c my-feature-branch
   # 在my-feature-branch上进行代码修改和提交
   git add .
   git commit -m "Add new feature"
   ```

3. **将本地特性分支合并到本地目标分支**

   使用`git switch`命令切换到目标分支，然后使用`git merge`命令将本地特性分支合并到本地目标分支。例如：

   ```bash
   git switch feature01 
   git merge my-feature-branch
   ```

4. **将本地目标分支推送到远程仓库**

   使用`git push`命令将本地目标分支推送到远程仓库。例如：`git push origin feature01`

   无关：将本地分支推送到远程（并创建）`(local-branch)git push -u origin branchName`

5. **如果步骤3时候remote/feature01已经进行了修改**

   用`git pull`命令将远程目标分支更新到本地，`git pull origin feature01`

   之后切换到my-feature-branch进行rebase`git rebase feature01`



还是不熟悉啊，练练！



## git rebase

git rebase可以用来合并分支以及整理commit历史。

最好只在自己负责的（本地分支来进行rebase）
