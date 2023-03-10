按照[廖雪峰的 git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)进行学习。

## 安装

这一步就不说了， win 本和 Mac 之前都早就安装过 git 。

## 创建 respository

首先创建一个空文件夹（非空的也行）来作为版本库的存储目录。

```sh
$ mkdir learngit
$ cd learngit
```

然后用 `git init` 命令使其成为 git 的版本库。

```shell
$ git init
Initialized empty Git repository in D:/Programming/learngit/.git/
```

之后我们随便写一个文件 readme.md 放在 learngit 目录下，提交到 git 仓库需要三步：

第一步，告诉 git 把文件添加到仓库

```sh
$ git add readme.md
```

执行完 `git add` 命令后是不会有反馈的。

第二步，告诉 git 把文件提交到仓库

```sh
$ git commit -m "test——第一次使用git"
[master (root-commit) 15511b8] test——第一次使用git
 1 file changed, 3 insertions(+)
 create mode 100644 readme.md
```

其中 `-m` 参数表示本次文件改动的说明，后面的字符串就是说明的内容。

## 时光机穿梭

我们对 readme.md 文件做出修改之后，可以通过 `git status` 命令查看版本库内的状态。

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        "git\345\255\246\344\271\240\347\254\224\350\256\260.md"

no changes added to commit (use "git add" and/or "git commit -a")
```

上面出现了 Untracked files 部分是因为我添加了 git学习笔记.md 文件并且没有告诉 git 。

之后我们可以通过 `git diff` 命令查看文件修改前后的对比。

```sh
$ git diff readme.md
diff --git a/readme.md b/readme.md
index 8d7a2fb..a20531f 100644
--- a/readme.md
+++ b/readme.md
@@ -1,3 +1,3 @@
-Git is a version control system.
+Git is a distributed version control system.

 Git is free software.
\ No newline at end of file
```

然后提交修改的操作和提交新文件的操作是一样的。

```sh
$ git add readme.md
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        "git\345\255\246\344\271\240\347\254\224\350\256\260.md"
$ git commit -m "add distributed"
[master 4a9d1f9] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### 回溯

我们再一次修改 readme.md 文件并 commit 。

```sh
$ git add readme.md
$ git commit -m "append GPL"
[master ff5ada4] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)
```

通过 `git log` 查看历史 commit 的信息。

```sh
$ git log
commit ff5ada47aa935a52f4e02505cfa272ec1107fd5b (HEAD -> master)
Author: f1a3h <ihlwer@outlook.com>
Date:   Fri Mar 3 23:21:28 2023 +0800

    append GPL

commit 4a9d1f9362b598c8c002a4da369477d431887c5e
Author: f1a3h <ihlwer@outlook.com>
Date:   Fri Mar 3 23:19:23 2023 +0800

    add distributed

commit 15511b878a425ebdfa93a260efe3bc3bccbb233f
Author: f1a3h <ihlwer@outlook.com>
Date:   Fri Mar 3 22:54:35 2023 +0800

    test——第一次使用git
```

也可以加上 `--pretty=oneline` 参数使其更简洁。

```sh
$ git log --pretty=oneline
ff5ada47aa935a52f4e02505cfa272ec1107fd5b (HEAD -> master) append GPL
4a9d1f9362b598c8c002a4da369477d431887c5e add distributed
15511b878a425ebdfa93a260efe3bc3bccbb233f test——第一次使用git
```

git 使用 HEAD 指针指向当前版本，回溯使用到的命令是 `git reset` ，参数是 `--hard` 和 `HEAD^` ，使用几个 `^` 就是往前几个版本，如果往前版本数太多可以使用 `HEAD~100` 来往前回溯 100 个版本。

```sh
$ git reset --hard HEAD^
HEAD is now at 4a9d1f9 add distributed
```

这时候使用 `git log` 会发现第三个版本不见了，但实际上它并没有被删除，我们仍然可以通过它的版本号回到这个版本。

```sh
$ git reset --hard ff5a
HEAD is now at ff5ada4 append GPL
```

版本号编码不需要太多，只要能让 git 找到它即可，但是太短，比如 2 个，也是不行的。

如果找不到它的版本号，可以用 `git reflog` 来找到之前的每一次命令。

```sh
$ git reflog
ff5ada4 (HEAD -> master) HEAD@{0}: reset: moving to ff5a
4a9d1f9 HEAD@{1}: reset: moving to HEAD^
ff5ada4 (HEAD -> master) HEAD@{2}: commit: append GPL
4a9d1f9 HEAD@{3}: commit: add distributed
15511b8 HEAD@{4}: commit (initial): test——第一次使用git
```

### 工作区和版本库

在我们创建的 learngit 文件夹内能看到的文件就是工作区，而 git 创建的一个默认隐藏的 .git 文件夹里的文件则是版本库。

打开 .git 文件夹可以看到很多东西，其中最重要的是 index 暂存区，还有 git 为我们创建的第一个分支 master 的指针文件 HEAD 。

当我们用 `git add` 添加文件时，实际是把文件添加进 index 暂存区。

用 `git commit` 提交文件时，实际是把暂存区的**所有**文件提交到当前分支。

### 管理与撤销修改

关于管理修改，我们只需要知道，git 管理的是文件的修改而不是文件本身。

如果我们在工作区修改了文件，此时还没有提交到暂存区，我们可以使用 `git checkout -- <file>` 来撤销修改，它会让文件回到最近一次 `git add` 或 `git commit` 时的状态，同时要注意 `--` 不能少，否则会变成 “切换到另一个分区” 的命令。

如果我们修改了文件并且已经 `git add` 了却还没有 `git commit` 的时候，我们仍然可以通过 `git reset HEAD <file>` 命令撤销缓存区的修改，此时 `git status` 查看文件状态会发现缓存区没有修改，而工作区存在修改。

### 删除文件

在 git 中，删除也是一次修改。

我们新建 test.md 文件，然后 `git add` 添加该文件，之后手动删除它，再查看一下 `git status` 。

```sh
$ touch test.md
$ git add test.md
$ rm test.md
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   test.md

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.md
```

可以发现 git 追踪到了 test.md 的删除，如果我们确实需要删除 test.md 文件，可以通过 `git rm` 来删除，然后再 `git commit` 提交。

```sh
$ git rm test.md
rm 'test.md'
$ git commit -m "remove test.md"
```

如果我们是删错了，希望恢复 test.md 文件，则可以通过 `git checkout -- <file>` 来恢复。

## 远程仓库

### 添加远程库

我们将本地 git 内容推送到 GitHub 上并远程同步，首先我们再 GitHub 上创建一个新的 repo ，然后根据 GitHub 给出的提示走就可以了。

其中，

`git remote add origin git@server-name:path/repo-name.git` 关联远程库，origin 是默认远程库的名称，

`git push -u origin master` 是第一次推送 master 分支的内容。

推送成功之后，如果我们本地进行了提交，只需用 `git push origin master` 推送至 GitHub 即可。

如果要删除远程库，可以先用 `git remote -v` 查看远程库的信息，然后再用 `git remote rm origin` 删除。

## 分支管理

### 创建与合并分支

创建分支 dev ：`git branch dev`

切换到分支 dev ：`git checkout dev` 或 `git switch dev`

二者可以合并成一条指令： `git checkout -b dev` 或 `git switch -c dev`

其中 `-b` 或 `-c` 参数表示创建。

然后，可以使用 `git branch` 查看当前所有分支。

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

### 解决冲突

当 git 无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用 `git log --graph` 命令可以看到分支合并图。

### 分支管理策略

实际开发中，我们平时应该在分支上干活，知道要发布一个稳定的新版本时再合并到 master 上。

合并分支时，加上 `--no-ff` 参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而 `fast forward` 合并就看不出来曾经做过合并。

### bug 分支

修复 bug 时，我们会通过创建新的 bug 分支进行修复，然后合并，最后删除。

当手头工作没有完成时，先把工作现场 `git stash` 一下，然后去修复 bug ，修复后，再 `git stash pop` 或者 `git stash apply` ，回到工作现场，但是 `git stash apply` 指令不会删除 `git stash list` 中的记录，需要再用 `git stash drop` 删除。我们也可以多次 `git stash` ，恢复的时候先用 `git stash list` 查看，然后用 `git stash apply stash@{0}` 来恢复指定的记录。

在 master 分支上修复的 bug ，想要合并到当前 dev 分支，可以用 `git cherry-pick <commit>` 命令，把 bug 提交的修改“复制”到当前分支，避免重复劳动，其中 `<commit>` 是在 master 分支上修复后 commit 的代号。

### Feature 分支

开发一个新 feature ，最好新建一个分支。

如果要丢弃一个没有被合并过的分支，可以通过 `git branch -D <name>` 强行删除。

### 多人协作

查看远程库信息，使用 `git remote -v` 。

本地新建的分支如果不推送到远程，对其他人就是不可见的。

从本地推送分支，使用 `git push origin branch-name` ，如果推送失败，先用 `git pull` 抓取远程的新提交。

在本地创建和远程分支对应的分支，使用 `git checkout -b branch-name origin/branch-name `，本地和远程分支的名称最好一致。

建立本地分支和远程分支的关联，使用 `git branch --set-upstream branch-name origin/branch-name` 。

从远程抓取分支，使用 `git pull` ，如果有冲突，要先处理冲突。

### Rebase

`git rebase` 操作可以把本地未 push 的分叉提交历史整理成直线。

rebase 的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
