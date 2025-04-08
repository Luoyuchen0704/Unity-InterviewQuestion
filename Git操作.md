# Git基本操作
![图片未显示](/Assets/git-command.jpg "Git-command")
* 四个仓库：workspace工作区，staging area缓存区，local repository本地仓库，remote repository远程仓库
* git init - 初始化仓库
* git add . - 添加文件到暂存区
* git commit -m "提交信息" - 提交暂存区文件到本地仓库
## 创建仓库命令
* git init - 初始化仓库
* git clone [url] - 克隆远程仓库
## 提交与修改
* git add [file1] [file2] ... - 添加指定文件到暂存区
* git status - 查看仓库当前的状态，显示有变更的文件
* git diff [file] - 比较文件的不同，即暂存区和工作区的差异
* git commit - 提交暂存区到本地仓库
* git reset [file] - 撤销暂存区的修改
* git rm [file1] [file2] ... - 删除暂存区和工作区文件
* git mv [file-original] [file-renamed] - 改名文件，并且将这个改名文件提交到暂存区
* git notes - 添加注释
* git checkout - 分支切换
* git show - 显示git对象的详细信息
### 提交日志
* git log - 查看历史提交记录
* git blame <file> 以列表形式查看指定文件的历史修改记录
* git shortlog 生成简洁的提交日志摘要
* git describe 生成一个可读字符串，基于Git的标签系统来描述
### 远程操作
* git remote - 远程仓库操作
* git fetch - 从远程仓库获取最新版本
* git pull - 从远程仓库获取最新版本并合并到本地
* git push - 将本地仓库的修改推送到远程仓库
* git submodule - 管理包含其他Git仓库的项目
## Git文件状态
* 三种，工作目录working directory，暂存区staging area，本地仓库local repository
### 工作目录
* 本地计算机看到的项目文件，实际操作文件的地方
## Git工作流程
1. 克隆仓库，git clone URL 
2. 创建新分支，git checkout -b new-branch
3. 在工作目录进行代码编辑、添加新文件或删除不需要的文件
4. 暂存文件，git add filename / git add .
5. 提交更改，git commit -m "Add new branch"
6. 拉取最新更改 git pull origin main
7. 推送更改 git push origin new-feature
8. 创建Pull Request(PR),邀请团队成员进行代码审查，PR合并到主分支
9. 合并更改，远程仓库主分支合并到本地分支, git checkout main, git pull origin main, git merge new-feature
1. 删除分支,git branch -d new-feature, 远程仓库删除分支git push origin --delete new-feature