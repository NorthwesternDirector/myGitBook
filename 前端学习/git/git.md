## Git `操作手册`

<img src="https://github.com/NorthwesternDirector/myGitBook/blob/master/前端学习/git/git.png"/>

> 看之前需要搞清以下概念：

**注意！！！：**

* **master**：本地分支名。

* **origin master**：origin 表示远端，master 表示分支名，接在origin 之后表示是远端分支名
* **origin/master**：远端分支在本地的拷贝，因此称为本地分支

---

1. 新建分支

   ```js
    git branch -a //查看分支
    git checkout 
    git checkout -b feat-tb-715 //检出分支 git branch branchName +git checkout branchName 
   ```

2. 拉取代码

   ```js
   git fetch --all // 拉取远程所有分支的更新
   git fetch origin master //取回origin主机的master分支；返回一个FETCH_HEAD
   git merge origin/master //将origin上的master分支 merge 到当前 branch 上 (git merge branchA branchB, branchB一般省略默认为当前branch)
   ```

3. 暂存及恢复

   ```js
   git stash //把所有未提交的修改（包括暂存的和非暂存的）都保存起来，用于后续恢复当前工作目录
   git stash apply
   git stash pop //重新应用缓存的stash，约等于 git stash apply && git stash drop
   git stash list  //查看stash了哪些存储
   ```

   [Difference between git stash pop and git stash apply](https://stackoverflow.com/questions/15286075/difference-between-git-stash-pop-and-git-stash-apply)

4. 推送代码

   ```js
   git add
   git commit
   git push -u origin feat-tb-715 // 将本地的feat-tb-715分支推送到origin主机，(-u) 同时指定 origin 为默认主机
   ```

5. 克隆代码

   ```js
   git clone <远程仓库地址>
   git clone -b <指定分支名> <远程仓库地址>// clone指定远程分支并在本地创建该分支
   ```

6. 修改提交

   ```js
   git commit --amend
   git rebase -i HEAD~4 (reword/fixup/squash/drop)
   ```

7. 设置用户名和邮箱

   ```js
   git config --global user.email "your email"
   git config --global user.name "your name"
   ```

8. Tag

   ```js
   git tag prod-20190415 -m "上线内容"
   git push origin prod-20190415
   ```

   