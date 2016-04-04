layout: post
title: Git学习笔记
comment: true
date: 2016-04-04 10:38:00
tags: [Git,技术,笔记] 
---
## Git相关概念
Git中的文件可能处于三个状态：已经提交(committed)、已经修改(modified)、已暂存(staged)。
基本的Git的工作流程：
1. 在工作目录中修改文件
2. 暂存文件，将文件的快照放入暂存区域
3. 提交更新，站到暂存区域文件，将快照永久存储到Git仓库目录

## Git命令——配置相关
```
$ git config -- list       ##列出Git所有的配置信息
$ git config user.name     ##检查Git的某项配置
```
<!-- more -->

## Git命令——基础
```
#初始化仓库
$ git init

#克隆仓库
$ git clone [url]

#查看文件所处的状态
$ git status

#将文件状态使用更紧凑的格式输出
$ git status -s

#查看尚未暂存文件更新了什么部分
$ git diff

#查看已暂存文件将要添加到下次提交的内容
$ git diff --staged

#将提交信息和命令放在同一行
$ git commit -m "commit message" 

#自动把所有已经跟踪过的文件暂存起来 一并提交，从而跳过 git add 步骤
$ git commit -a -m "commit message"

#将文件从暂存区域移除，并从工作目录中删除指定文件
$ git rm [filename]

#删除已经修改过并已经放到暂存区域的文件
$ git rm -f [filename]

#将文件从暂存区移除，但保留在工作目录
$ git rm --cached [filename]
  
#删除log/目录下扩展名为.log的文件
$ git rm log/\*.log  

#文件改名
$ mv <file_from> <file_to>

#查看提交历史
$ git log

#-p 显示提交内容的差异 -2显示近两次提交
$git log -p -2

#最终只会有一个提交，第二次提交将代替第一次提交的结果
$ git commit -m 'initial commit' 
$ git add forgotten_file 
$ git commit --amend

#取消暂存文件
$ git reset HEAD <file>

#撤销对未暂存文件的修改
$ git checkout -- <filename>
```

## 远程仓库
```
#添加远程仓库
$ git remote add [remote-name] [url]

#查看远程仓库
$ git remote -v

#抓取克隆（或上一次抓取）后新推送的所有工作——不会自动合并或修改当前工作
$ git fetch [remote-name]
#自动抓取然后合并远程分支当当前分支
$ git pull [remote-name]

#推送到远程仓库
$ git push [remote-name] master

#查看远程仓库的更多信息
$ git remote show [remote-name]

#远程仓库的重命名
$ git remote rename [from-name] [to-name]

#远程仓库的移除
$ git remote rm [remote-name]
```

## Git分支
```
#创建分支
$ git branch [brach-name]

#切换分支
$ git checkout [brach-name]

#新建分支并切换到那个分支上
$ git checkout -b [brach-name]

#删除分支
$ git branch -d [brach-name]

```

## 忽略文件
当我们有些文件无需纳入Git的管理时（例如一些日志文件或者编译中产生的临时文件），我们可以创建一个名为`.gitignore`的文件，列出要忽略的文件模式。一个实际的例子如下：
```
$ cat .gitignore
*.[oa]                   ##忽略所有以.o和.a结尾的文件
*~                       ##忽略所有以波浪符(~)结尾的文件
```
文件`.gitignore`的格式规范如下：
- 所有空行或者以 ＃ 开头的行都会被 Git 忽略。 
- 可以使用标准的 glob 模式匹配。 
- 匹配模式可以以（/）开头防止递归。 
- 匹配模式可以以（/）结尾指定目录。 
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取 反。

例外一个`.gitignore`文件的例子
```
# no .a files 
*.a
# but do track lib.a, even though you're ignoring .a files above 
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO 
/TODO
# ignore all files in the build/ directory 
build/
# ignore doc/notes.txt, but not doc/server/arch.txt 
doc/*.txt
# ignore all .pdf files in the doc/ directory 
doc/**/*.pdf
```

## 服务器上的Git
### Git用来传输资料的四种主要协议：本地协议、HTTP协议、SSH协议和Git协议。
- 本地协议：远程版本就是硬盘内的另一个目录
```
$ git clone file://opt/git/project.git
```
- HTTP协议
```
$ git clone https://example.com/gitproject.git
```
- SSH协议
```
$ git clone ssh://user@server/project.git
$ git clone user@server:project.git
```
- Git协议
这是包含在 Git 里的一个特殊的守护进程；它监听在一个特定的端口（9418），类似于 SSH 服务，但是访问无需任何授权。

## 分布式Git
集成管理者工作流程
1. 项目为辅着推送到主仓库
2. 贡献者克隆此仓库，做出修改
3. 贡献者将数据推送到自己的公开仓库
4. 贡献者给维护者发送邮件，请求拉取自己的更新
5. 维护者在自己本地的仓库中，将贡献者的仓库加为远程仓库并合并修改
6. 维护者将合并后的修改推送到主仓库

## 参考资料
[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)