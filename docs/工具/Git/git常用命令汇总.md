| 命令                         | 作用                                  |
| -------------------------- | :---------------------------------- |
| **基本Git指令**                |                                     |
| git init                   | 初始化仓库                               |
| git add .                  | 将新建、修改的文件，添加暂存区                     |
| git add -u                 | 修改、删除的文件，添加暂存区                      |
| git add -A                 | 新建、修改、删除的文件，添加到暂存区                  |
| git commit -m '注释'         | 把文件从暂存区提交到仓库                        |
| git commit-amend 'message' | 合并上一次提交                             |
| git commit -am 'message'   | 将add和commit合并为一步                    |
| git status                 | 查看当前分支状态                            |
| git log                    | 查看提交信息(详细)                          |
| git  log --pretty=oneline  | 显示版本号和提交说明                          |
| git reflog                 | 查看提交信息（包括已删除的 commit 记录和 reset 的操作） |
| git reset --hard  版本号      | 切换到指定版本（清空工作区、暂存区）                  |
| git reset --soft           | 工作区、暂存区保留差异                         |
| git reset  --mixed         | 清空暂存区，工作区保留差异                       |
| git fetch                  | 从远程获取最新版本到本地                        |
| git merge 分支名              | 合并分支                                |
| git pull                   | fetch+merge                         |
| git push -u origin master  | 第一次推送到远程仓库                          |
| git push                   | 以后提交（简写）                            |
|                            |                                     |
| **分支相关**                   |                                     |
| git branch                 | 显示所有分支                              |
| git branch -v              | 查看每个分支的最后一次提交                       |
| git branch [name]          | 创建分支                                |
| git checkout [name]        | 切换分支                                |
| git checkout -b [name]     | 创建并切换到[name]分支                      |
| git merge [name]           | 将当前分支与[name]分支合并                    |
| git branch -d [name]       | 删除[name]分支（仅用于已经merge了的分支）          |
| git branch -D [name]       | 强制删除[name]分支                        |
| git branch -r              | 查看远程分支                              |
| git push origin [name]     | 创建远程分支（本地分支push到远程）                 |
| git push origin :[name]    | 删除远程分支                              |
|                            |                                     |

