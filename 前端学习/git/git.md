##  Git `操作手册`

<img src="https://github.com/NorthwesternDirector/myGitBook/blob/master/%E5%89%8D%E7%AB%AF%E5%AD%A6%E4%B9%A0/git/git.png?raw=true"/>

### 前置知识

> 看之前需要搞清以下概念⚠️：

* 本地创建的分支：一切都只发生在你本地的 Git 版本库中，没有与服务器发生交互（**本地分支**）

* **master**：从远程跟踪分支检出一个本地分支会自动创建一个（**跟踪分支**），**跟踪分支是与远程分支有直接关系的本地分支 ✅**

  > 当克隆一个仓库时，git通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支

* **origin master**：origin 表示远端，master 表示分支名，接在origin 之后表示是远端分支名（**远程分支**）

* **origin/master**：远端分支在本地的拷贝（**远程跟踪分支**）

> **远程跟踪分支**是远程分支状态的引用；它们是你不能移动的本地引用，当你做任何网络通信操作时，它们会自动移动； 远程跟踪分支像是你上次连接到远程仓库时，那些分支所处状态的书签）

origin master （git fetch--->）origin/master（git merge--->）master

### 1. 新建分支

```js
git branch -a //查看所有分支
git branch //查看本地分支
git branch -vv //查看跟踪分支
git branch branchName
git checkout -b feat-tb-715 //检出分支 git branch branchName +git checkout branchName 
```

注意 ⚠️：

`git checkout branchName `（可在任意分支检出到目标分支并实现追踪远程分支✅）

(1) 会自动创建名为 branchName 的跟踪（本地）分支

(2) 并生成远程跟踪分支(origin/branchName )，并以 origin/branchName 为起点

(3) 跟踪与远程同名的远程分支

【该条命令生效的前提**是在 gitlab 中事先创建了远程分支**，该命令实际才会进行这3个操作】

> git checkout 1234 （一个远程不存在的分支时）会报：error：pathspec '1234' did not match any file(s) known to git

git checkout branchName 效果与 git checkout -b branchName origin/branchName 相同

`git checkout -b feat-tb-715 `

在当前分支上输入此命令，会在当前分支检出此新建的分支（一般需要在master上检出到目标分支✅）

### 2. 拉取代码

```js
git fetch --all // 拉取远程所有分支的更新（没有--all参数 ⚠️默认拉有跟踪分支的远程分支）
git fetch origin master //取回origin主机的master分支；返回一个FETCH_HEAD
git merge origin/master //将origin上的master分支 merge 到当前 branch 上 (git merge branchA branchB, branchB一般省略默认为当前branch,即 merge A B 指将 A merge到 B 上)
git merge --no-ff //无论如何都不进行fast-forward
```

fast-forward问题参考：[参考](https://qiita.com/vc7/items/6e06b0306c8a64a23263)

### 3. 暂存及恢复

```js
git stash //把所有未提交的修改（包括暂存的和非暂存的）都保存起来，用于后续恢复当前工作目录
//注意⚠️：stash的内容没有分支限制，可以在Abranch stash 后，checkout 到 Bbranch stash pop
git stash apply
git stash pop //重新应用缓存的stash，约等于 git stash apply && git stash drop
git stash pop stash@{0} //应用指定位置的stash，序号可用 git stash list 查看
git stash list  //查看stash了哪些存储
git stash branch branchName //stash内容恢复到一个新的分支
```

[Difference between git stash pop and git stash apply](https://stackoverflow.com/questions/15286075/difference-between-git-stash-pop-and-git-stash-apply)

### 4. 推送代码

```js
git add
git commit
git push -u origin feat-tb-715 // 将本地的feat-tb-715分支推送到origin主机，(-u) 同时指定 origin 为默认主机
```

### 5. 克隆代码

```js
git clone <远程仓库地址>
git clone -b <指定分支名> <远程仓库地址>// clone指定远程分支并在本地创建该分支
```

### 6. 修改提交

```js
git commit --amend
git rebase -i HEAD~4 (reword/fixup/squash/drop)
```

### 7. 设置用户名和邮箱

```js
git config --global user.email "your email"
git config --global user.name "your name"
```

### 8. Tag

```js
git tag prod-20190415 -m "上线内容"
git push origin prod-20190415
```

### 9. 将master上最新的代码合并到当前分支上

```js
//在当前分支上
git checkout master
git pull
git checkout 38-copy
git merge master
//在当前分支上 （良渝：本地master分支未更新）
git fetch
git merge origin/master
```

### 10. 撤销

*（1）撤销 本地 commit*

```js
git reset --soft HEAD^
//（默认）--mixed 撤销 commit，并且撤销 git add . 操作
// --soft 撤销 commit，不撤销 git add .
//HEAD^ HEAD~n 标志位：前n个（commit） 
```

*（2）撤销 gitlab commit*

```js
git log //找到回退的版本的 commitId
git reset --soft/--hard/--mix commitId //本地回退到指定版本
git push origin branchName -- force //强制覆盖更新到远程分支
```

### 11. Gitlab&Github

```
Host *github.com
    User git
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_hub
Host *xiaomi.com
    User git
    HostName git.n.xiaomi.com
    IdentityFile ~/.ssh/id_rsa_lab
```

### 12. 修改分支名

```
git branch -m new-name
git push (git push 后会有提示：git push origin HEAD:new-name)
生成新的 merge request ✅
```

