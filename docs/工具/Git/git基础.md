# 目录

  * [一、生成Git仓库](#一生成git仓库)
    * [1.在工作目录初始化新仓库](#1在工作目录初始化新仓库)
    * [2.从现有仓库克隆](#2从现有仓库克隆)
  * [二、记录每次更新提交到仓库](#二记录每次更新提交到仓库)
    * [1.检查当前文件状态](#1检查当前文件状态)
    * [2.跟踪新文件](#2跟踪新文件)
    * [3.修改已暂存文件](#3修改已暂存文件)
    * [4.忽略某些文件](#4忽略某些文件)
    * [5.查看更新内容](#5查看更新内容)
    * [6.提交更新](#6提交更新)
    * [7.移除文件](#7移除文件)
  * [三、查看提交历史](#三查看提交历史)
  * [四、撤销提交](#四撤销提交)
    * [1.修改最后一次提交操作](#1修改最后一次提交操作)
    * [2.取消已经暂存的文件](#2取消已经暂存的文件)
  * [五、远程仓库的使用](#五远程仓库的使用)


* [一、生成Git仓库](#%E4%B8%80%E7%94%9F%E6%88%90git%E4%BB%93%E5%BA%93)
  * [1\.在工作目录初始化新仓库](#1%E5%9C%A8%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95%E5%88%9D%E5%A7%8B%E5%8C%96%E6%96%B0%E4%BB%93%E5%BA%93)
  * [2\.从现有仓库克隆](#2%E4%BB%8E%E7%8E%B0%E6%9C%89%E4%BB%93%E5%BA%93%E5%85%8B%E9%9A%86)
* [二、记录每次更新提交到仓库](#%E4%BA%8C%E8%AE%B0%E5%BD%95%E6%AF%8F%E6%AC%A1%E6%9B%B4%E6%96%B0%E6%8F%90%E4%BA%A4%E5%88%B0%E4%BB%93%E5%BA%93)
  * [1\.检查当前文件状态](#1%E6%A3%80%E6%9F%A5%E5%BD%93%E5%89%8D%E6%96%87%E4%BB%B6%E7%8A%B6%E6%80%81)
  * [2\.跟踪新文件](#2%E8%B7%9F%E8%B8%AA%E6%96%B0%E6%96%87%E4%BB%B6)
  * [3\.修改已暂存文件](#3%E4%BF%AE%E6%94%B9%E5%B7%B2%E6%9A%82%E5%AD%98%E6%96%87%E4%BB%B6)
  * [4\.忽略某些文件](#4%E5%BF%BD%E7%95%A5%E6%9F%90%E4%BA%9B%E6%96%87%E4%BB%B6)
  * [5\.查看更新内容](#5%E6%9F%A5%E7%9C%8B%E6%9B%B4%E6%96%B0%E5%86%85%E5%AE%B9)
  * [6\.提交更新](#6%E6%8F%90%E4%BA%A4%E6%9B%B4%E6%96%B0)
  * [7\.移除文件](#7%E7%A7%BB%E9%99%A4%E6%96%87%E4%BB%B6)
* [三、查看提交历史](#%E4%B8%89%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)
* [四、撤销提交](#%E5%9B%9B%E6%92%A4%E9%94%80%E6%8F%90%E4%BA%A4)
  * [1\.修改最后一次提交操作](#1%E4%BF%AE%E6%94%B9%E6%9C%80%E5%90%8E%E4%B8%80%E6%AC%A1%E6%8F%90%E4%BA%A4%E6%93%8D%E4%BD%9C)
  * [2\.取消已经暂存的文件](#2%E5%8F%96%E6%B6%88%E5%B7%B2%E7%BB%8F%E6%9A%82%E5%AD%98%E7%9A%84%E6%96%87%E4%BB%B6)
* [五、远程仓库的使用](#%E4%BA%94%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)

## 一、生成Git仓库

生成Git项目仓库有两种方法。第一是在现存的目录下，通过导入所有文件来创建新的Git仓库。第二是从已有的Git仓库克隆出一个新的镜像仓库。

### 1.在工作目录初始化新仓库

对现有某个项目开始Git管理，在项目所在目录，执行

```java
git init
```

初始化后，会出现.git目录，所有Git需要的数据和资源都存放在此目录，此时还没有开始跟踪项目中的任何文件。

需先用git add命令告诉Git开始对这些文件进行跟踪，然后提交

```
git add *.c
git add README
git commit -m 'initial project'
```

现在已得到维护着若干文件的Git仓库

### 2.从现有仓库克隆

克隆仓库的命令如：

```
git clone https://github.com/chao96/JavaPioneer.git
```

会在当前目录下创建JavaPioneer目录，其中包含.git目录

## 二、记录每次更新提交到仓库

工作目录下所有文件分为两种模式：已跟踪、未跟踪。已跟踪文件是指被纳入版本控制管理的文件，文件的状态分为（未修改、已修改、已放入暂存区）。初次克隆某个仓库时，工作目录所有文件都属于已跟踪文件。

### 1.检查当前文件状态

git status

如果在克隆仓库之后立即执行此命令，会看到类似这样的输出：

```
$ git status
On branch master
nothing to commit, working directory clean
```

说明所有已跟踪文件在上次提交后都未变更过，当前所在分支是master。

新建一个文件README，保存退出后执行git status

```
$ vim README
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        README

nothing added to commit but untracked files present (use "git add" to track)
```

可以看到新建的README文件在“Untracked files”下面，表示未跟踪的文件。

### 2.跟踪新文件

使用`git add ` 开始跟踪新文件

```
$ git add README
```

再运行 `git status` 命令，会看到 README 文件已被跟踪，并处于暂存状态：

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   README
```

### 3.修改已暂存文件

修改之前已跟踪的`文件.txt`，再次执行git status

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   文件.txt
```

 “Changes not staged for commit” 表示 `文件.txt` 内容发生变化，但还没有放到暂存区。

### 4.忽略某些文件

有些文件无需纳入Git管理，如日志文件等。我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件模式。例：

```
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。

第二行告诉 Git 忽略所有以波浪符（`~`）结尾的文件.

文件 `.gitignore` 的格式规范如下：

- 所有空行或者以注释符号 `＃` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式最后跟反斜杠（`/`）说明要忽略的是目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（`!`）取反。

> 所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。星号（`*`）匹配零个或多个任意字符；`[abc]`匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。

再看一个 `.gitignore` 文件的例子：

```
# 忽略所有 .a 结尾的文件
*.a
# 但 lib.a 除外
!lib.a
# 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
/TODO
# 忽略 build/ 目录下的所有文件
build/
# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录下所有扩展名为 txt 的文件。双星号代表可以匹配后代所有子目录。
doc/**/*.txt
```

### 5.查看更新内容

要查看尚未暂存的文件更新了哪些内容，输入 `git diff`，此命令比较的是工作区中当前文件和暂存区之间的差异。

若要看已经暂存起来的文件和上次提交时的快照之间的差异，使用 `git diff --staged`

### 6.提交更新

使用`git commit -m "提交说明"`，将更新提交到本地仓库

> 记住，提交时记录的是放在暂存区的快照，任何还未暂存的仍然保持已修改状态，可以在下次提交时纳入版本管理。每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。

Git 提供了一个跳过使用暂存区的方式，只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤.

```
$ git commit -a -m 'added new file'
```

### 7.移除文件

删除指定文件

```
git rm 文件名称
```

删除文件夹（递归删除其子文件）

```
git rm -r 文件夹/
```

> --cached              只从暂存区删除（工作区仍保留）
> -f, --force             忽略文件更新状态检查
> -r                          允许递归删除

## 三、查看提交历史

**1.git log**

显示最近提交的版本号、作者名、邮箱、提交时间、提交说明

**2.git log --pretty=oneline**

只显示版本号和提交说明

**3.git reflog**

查看提交信息（包括已经被删除的 commit 记录和 reset 的操作）

> 例如执行 git reset --hard HEAD~1，退回到上一个版本，用git log则是看不出来被删除的commitid，用git reflog则可以看到被删除的commitid，可恢复到被删除的那个版本。

## 四、撤销提交

### 1.修改最后一次提交操作

有时候我们提交完了发现漏掉了几个文件，或者提交信息写错了。想要撤销刚才的提交操作，可以使用--amend选项重新提交

①如重新编辑提交说明

```
git commit --amend
```

> 执行后会启动文本编辑器，会看到上次提交时的说明，重新编辑（按i进行编辑）确认没问题后保存退出（按esc后，输入`:wq`保存退出），就会使用新的提交说明覆盖之前的提交说明。

②如果提交时忘了暂存某些修改，可以先补上暂存操作，然后再运行 `--amend` 提交：

```
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

> 上面的三条命令最终只是产生一个提交，第二个提交命令修正了第一个的提交内容。

### 2.取消已经暂存的文件

如果有两个修改过的文件，我们想要分开提交，但不小心全加到了暂存区。该如何撤消暂存区中的一个文件？可以使用 `git reset HEAD <file>...` 的方式取消暂存

```
git reset HEAD 1.txt
```

> 这样1.txt就回到了之前已修改未暂存的状态

## 五、远程仓库的使用

**1.查看当前的远程库**

要查看当前配置有哪些远程仓库，可以用 `git remote` 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库

`git remote -v` 会显示出对应的克隆地址

> $ git remote -v
> origin  git://github.com/schacon/ticgit.git (fetch)
> origin  git://github.com/schacon/ticgit.git (push)

**2.从远程仓库抓取数据**

```
git fetch [remote-name]
```

fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支。

而`git pull` 则是将远程主机的最新内容拉下来后直接合并，即：`git pull = git fetch + git merge`，这样可能会产生冲突，需要手动解决。

> 如果设置了某个分支用于跟踪某个远端仓库的分支，可以使用 `git pull` 命令自动抓取数据下来，然后将远端分支自动合并到本地仓库中当前分支。在日常工作中我们经常这么用，既快且好。实际上，默认情况下 `git clone` 命令本质上就是自动创建了本地的 master 分支用于跟踪远程仓库中的 master 分支（假设远程仓库确实有 master 分支）。所以一般我们运行 `git pull`，目的都是要从原始克隆的远端仓库中抓取数据后，合并到工作目录中的当前分支。

**3.推送数据到远程仓库**

将本地仓库中的数据推送到远程仓库。 `git push [remote-name] [branch-name]`。如果要把本地的 master 分支推送到 `origin` 服务器上（克隆操作会自动使用默认的 master 和 origin 名字），可以运行下面的命令：

```
$ git push origin master
```

**4.远程仓库的删除和重命名**

Git 中可以用 `git remote rename` 命令修改某个远程仓库在本地的简称，比如想把 `origin` 改成 `xc`，可以这么运行：

```
$ git remote rename origin xc
$ git remote
xc
```

> 注意，对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 `origin/master` 分支现在成了 `xc/master`。

碰到远程仓库服务器迁移等，需要移除对应的远程仓库可以执行

```
git remote rm xc
```

