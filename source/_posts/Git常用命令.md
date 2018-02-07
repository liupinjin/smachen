---
title: Git常用命令
date: 2017-01-19 10:42:45
categories:
- 技术
- Git
tags:
- git
- 命令
---

整理常用的git命令，以备记录
```shell
// 创建新仓库，初始化命令
git init 
// 将本地仓库连接到远程仓库 origin远程仓库对应的名称,默认都用这个，可以自定义, server_url远程仓库的地址
git remote add <origin> <server_url>
// 检出仓库(远程仓库),server_url仓库的地址
git clone <url>
// 更新本地分支代码
git pull
// 比较两个分支的区别 source_branch本地分支, target_branch目标分支
git diff <source_branch> <target_branch>
//合并其他分支到你的当前分支
git merge <branch>
// 添加到暂存区 filename需要添加的文件名, *全部改动
git add <filename>
git add *
// 提交改动注释到暂存区 Submit information改动的注释
git commit -m "<Submit information>"
// 推送当前分支改动到远程仓库 master你的分支名称，origin远程仓库名称
git push <origin> <master>

// 创建分支 branch_name 分支名称 
git branch <branch_name>
// 切换分支 branch_name要切换的分支名称
git checkout <branch_name>
// 删除分支 branch_name 分支名称 
git branch -d <branch_name>
```
[简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
