---
title: Git命令
date: 2018-11-30 10:37:44
tags:
  - Git
categories:
  - Git
---
#### Github上传本地项目到新建的远程仓库

1. 到Github新建远程仓库
2. 在本地项目文件夹根目录右键，点击`Git Bash Here`，打开git命令行
3. `git init`
4. `git add .`
5. `git commit -m 'first commit'`
6. `git remote add origin https://github.com/Jacen-Chou/HBaseDemo.git`（把地址改成自己的远程仓库地址）
7. `git pull`
8. `git push -u origin master`

---

<!-- more -->

#### 1. 切换到自己的分支
git checkout -b your_name
#### 2. 确认修改状态
git status
git add .
#### ====== 若代码是最新的则跳过以下步骤 ======
#### 2.5. 暂存修改状态, 并拉取最新代码（若本地代码已经是最新的则跳过这一步）
git stash
git pull origin master
#### 2.5. 重放修改状态
git stash apply
#### 2.5. 重新确认修改状态
git status
git add .
#### =============================
#### 3. 提交代码并push到远程的自己的分支
git commit -m "[add/update/bugfix/delete/other] 新增过滤功能"
git push origin your_name

#### Git Status 中文乱码解决
`git config --global core.quotepath false`

---
git更新远程分支列表:`git remote update origin --prune`

git查看远程仓库地址:`git remote -v`

更新远程参考代码:`git remote update`

查看当前所在分支:`git branch`

查看远程分支:`git branch -a`

创建新分支并切换到新分支:`git checkout -b $new_branch_name origin/$new_branch_name`

切换分支:`git checkout $branch_name`
