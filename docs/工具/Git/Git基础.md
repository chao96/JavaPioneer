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