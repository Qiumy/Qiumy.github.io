layout: post
title: Github Pages + Hexo to Bulid Blog
comment: true
tags: [技术, Hexo, Git, Github Pages, 静态博客]
date: 2016-01-11 14:59:51
---

## 一、前言
这是一篇使用GitHub Pages和Hexo搭建免费独立博客的总结。
使用github纯粹是因为课程需要，在大三下准备找实习的时候本来想着往前端方向发展，开始接触到各种大神搭建的独立博客，于是当时就很想自己弄一个玩玩，无奈当初自己太天真，而且不够毅力，也就不了了之。现在，也算是机缘巧合吧，让自己有机会体验一把。

本文关注的主要是单纯使用GitHub Pages搭建自己独立博客的过程，并没有额外使用独立域名之类的（等以后真的弄了再补充吧）

<!-- more -->

知识基础：
- [Git](http://git-scm.com/book/zh/v2)
- [Github](https://github.com/)
- [Github Pages](https://pages.github.com/)
- [Hexo](https://hexo.io/zh-cn/)
- [Markdown](http://www.appinn.com/markdown/#autoescape)

## 二、准备环境
### 2.1 GitHub Pages 仓库
#### 2.1.1 创建Github仓库

在自己的GitHub账号下创建一个新的仓库，命名为username.github.io（username是你的账号名)。
附：GitHub Pages有两种类型：User/Organization Pages 和 Project Pages，而我所使用的是User Pages。
简单来说，User Pages 与 Project Pages的区别是：
User Pages 是用来展示用户的，而 Project Pages 是用来展示项目的。
用于存放 User Pages 的仓库必须使用username.github.io的命名规则，而 Project Pages 则没有特殊的要求。
User Pages 将使用仓库的 master 分支，而 Project Pages 将使用 gh-pages 分支。
User Pages 通过 http(s)://username.github.io 进行访问，而 Projects Pages通过 http(s)://username.github.io/projectname 进行访问
#### 2.1.2 相关资料
[User, Organization, and Project Pages](https://help.github.com/articles/user-organization-and-project-pages/)

### 2.2 Git 安装
#### 2.2.1 安装Git
Windows下安装git的方式可以参考：
1. [Git官方版本安装](http://git-scm.com/download/win)
2. [Github For Windows](https://desktop.github.com/)(这里安装可能会不成功，具体可以自己查找相关的资料)

#### 2.2.2 Git配置SSH key

本地配置SSH keys，使用SSH keys验证Github的好处就是不用每次提交代码的时候都验证用户名和密码
1.**检查本地是否已经存在SSH key**。如果提示`No such file or directory` 说明不存在，继续第二步；否则，直接跳到第三步
```
$ cd ~/.ssh 检查本机的ssh密钥
```
2.**生成新的SSH key**。 
```
$ ssh-keygen -t rsa -C "你的邮件地址"
```
- 输入命令后会出现
```
Enter file in which to save the key (/your_home_path/.ssh/id_rsa):
```
意思是默认在相应路径下（/your_home_path）生成id_rsa和id_rsa.pub两个文件，直接回车确认就行。
- 接着会出现
```
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```
意思是设置passphrase，这个密码的作用在于进行版本控制时，每次与GitHub通信都会要求输入passphrase，以避免某些“失误”，如果没有这个安全需要，建议为空，直接回车确认。

3.**将SSH Key添加到GitHub中**。
用你喜欢的方法打开id_rsa.pub文件，里面的内容就是SSH Key。登陆Github网站，点击Settings—->SSH keys —-> Add SSH key。然后把文件中的内容复制到Key文本框里面，Title自定义。

4.**检查是否设置成功**。输入命令：
```
$ ssh -T git@github.com
```
如果出现
```
Are you sure you want to continue connecting (yes/no)?
```
不用紧张，直接输入yes就行，接着就会看到
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```
说明设置成功了～

5.**最后，设置一下用户信息**。
```
$ git config --global user.name "username"//Github用户名，不是昵称
$ git config --global user.email  "username@gmail.com"//自己的邮箱
```
就这样，我们就成功完成安装Git与配置SSH key了。
#### 2.2.3 相关资料
- [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/)

### 2.3 Hexo
#### 2.3.1 安装Hexo
安装Hexo相当简单。在安装之前，必须检查电脑中是否已经安装下列应用程序：
- [Node.js](http://nodejs.org/)
- [Git](http://git-scm.com/)
如果您的电脑中已经安装上述必备程序，那么恭喜您！接下来只需要使用 npm 即可完成 Hexo 的安装。
```
$ npm install -g hexo-cli
```
#### 2.3.2 使用Hexo建站
安装完后，在你喜欢的文件夹内（例如D：\Hexo），点击鼠标右键选择Git bash，输入以下指令：
```
$ hexo init
```
该命令会在目标文件夹内建立网站所需要的所有文件。接下来是安装依赖包：
```
$ npm install
```
这样，我们就已经搭建起本地的Hexo博客了。可以先执行以下命令（在对应文件夹下），然后再浏览器输入localhost:4000查看。
```
$ hexo generate
$ hexo server
```
这个博客只是本地的，别人是浏览不了的，之后需要部署到GitHub上。
#### 2.3.3 相关资料
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)

## 三、网站搭建
### 3.1 使用默认的theme
我们继续使用上面的文件夹D:\Hexo（也可以新建一个文件夹重新生成），然后编辑该文件夹下的_config.yml。
默认生成的_config.yml：
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:
```
修改后的_config.yml：
```
deploy:
  type: git
  repo: 对应仓库的SSH地址（可以在GitHub对应的仓库中复制）
  branch: 分支（User Pages为master，Project Pages为gh-pages）
```
为了能够使Hexo部署到GitHub上，需要安装一个插件：
```
$ npm install hexo-deployer-git --save
```
然后，执行下列指令即可完成部署：
```
$ hexo generate
$ hexo deploy
```
之后，可以通过在浏览器键入：username.github.io进行浏览

### 3.2 其他theme
从Hexo的主题列表Hexo Themes中找到自己喜欢的主题。我选择了其中的Yilia，小清新的色调加上新颖的响应式布局，对我十分有吸引力。安装主题的方法就是一条简单的git命令，如下：
```
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
##### （使用git clone命令后记得删除里面的.git文件夹，不然放在自己的博客仓库后再push到Github上会发现无法访问）
打开hexo/目录下的_config.yml文件，修改主题为Jacman
```
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: yilia
```

### 3.3 常用Hexo命令
常用的命令：
```
$ hexo init #初始化
$ hexo new "name" #新建文章
$ hexo generate #生成静态页面至public目录
$ hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
$ hexo deploy #将.deploy_git目录部署到GitHub
```
常用的复合命令：
```
$ hexo deploy -g
$ hexo server -g
```
命令的简写：
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

## 四、优化部署与管理
具体参照了网上的教程，利用Github分支对网站进行管理，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。请戳->[点我](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2/)
在此也记录一下具体的搭建过程
### 4.1 搭建流程
1. 创建远程仓库 Username.github.io
2. 初始化本地仓库Username.github.io，并关联远程仓库，进行git pull同步本地和远程仓库
3. 本地基于master创建hexo分支`git branch hexo master`,并进行第一次git push操作，同步完成后Github的仓库也有两个分支，分别为hexo分支和master分支，此时在Github上设置hexo为默认分支
4. 在本地Username.github.io文件夹下通过Git bash依次执行npm install hexo、hexo init、npm install 和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;
5. 修改_config.yml中的deploy参数，设置的分支应为master；
6. 依次执行git add .、git commit -m “…”、git push origin hexo提交网站相关的文件；
7. 执行hexo generate -d生成网站并部署到GitHub上。

### 4.2 管理流程
#### 4.2.1 在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理：
1. 依次执行git add .、git commit -m “…”、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；
2. 执行hexo generate -d发布网站到master分支上。

#### 4.2.2 本地资料丢失
当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：
1. 使用git clone git@github.com:Username/Username.github.io.git拷贝仓库（默认分支为hexo）；
2. 在本地新拷贝的Username.github.io文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。

### 4.3 相关资料
- [CrazyMilk的博客](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2/)
- [eleveneat的博客](http://eleveneat.com/2015/04/24/Hexo-a-blog/#more)
- [Hexo 静态博客使用指南](http://www.jianshu.com/p/73779eacb494)




