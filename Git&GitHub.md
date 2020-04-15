# Git&GitHub

## 一、Git

### 1、版本控制

1. 集中式（svn）

   svn因为每次存的都是差异，所以需要的硬盘空间会小一点，而是回滚的速度会很慢

   优点：代码存放在单一的服务器上，便于项目管理

   缺点：服务器宕机：员工写的代码得不到保障

   ​			服务器炸了：整个项目历史记录都会消失

2. 分布式（git）

   git每次存的都是项目的完整快照，需要的硬盘空间会相对大一些。

   （git团队对代码做了极致的压缩，最终需要的硬盘空间比svn大不了多少，但是git的回滚速度极快）

### 2、学习git的两个主线

1. 三个区域
   1. 工作区
   2. 暂存区
   3. 版本库
2. 三个对象
   1. git对象
   2. 树对象
   3. 提交对象 

### 3、安装

使用镜像地址下载

https://npm.taobao.org/mirrors/git-for-windows/

安装：修改安装地址，然后下一步就OK了。

初始化仓库：git init

仓库目录介绍：

==hooks==：包含客户端或服务端的钩子脚本

info：包含一个全局性排除文件

logs：保存日志信息

==objects==：存储所有数据内容

==refs==：存储指向数据（分支）的提交对象的指针

config：包含项目特有的配置选项

description：显示仓库的描述信息

==HEAD==：指示目前被检出的分支

index：保存暂存区信息

### 4、git底层概念

**git对象：**

​		Git的核心部分是一个简单的键值对数据库。可以向数据库中插入任何类型的内容，它会返回一个键值，通过该键值可以在任意时刻检索该内容.

​		git对象的key是存放数据对应的hash值，存放文件类型是blob类型

```linux
 echo 'mynode ve' | git hash-object -w --stdin
```

该命令将输入的字符串保存到版本库其中：

   	-w  表示：存储到版本库中，不加-w则只会返回hash值

​	   --stdin 表示：标准input输入流，接收输入数据

```linux
//通过键值查看文件内容。。直接查看的话显示的是被压缩过的文件，会乱码
git cat-file -p eda43956ee37cc1a0d1950666871055f4ae8f5b5
```

```linux
//直接对文件进行存储
git hash-object -w test.txt
```

**底层命令：**

**git对象**

​	git hash-object -w 文件名

**tree对象**

​	git update-index --add --cacheinfo 100644 hash 文件名		 ：将文件放入暂存区(让git对象对应上文件名)，																												（存到index文件中）

​	git write-tree 			：生成树对象（存到objects目录中）

**commit对象**

​	echo 'msg' | git commit-tree  treeHash		  ：生成提交对象

 对以上对象的查询命令：

​	git cat-file -p 文件hash值			：查看文件内容

​	git cat-file -t 文件hash值				：查看文件类型

**注意：**

​		git对象只是对数据进行了存储，并未对文件名进行存储。并且一个git对象不能代表一个版本。

总结：项目的版本应该是一个提交对象，项目的快照是一个树对象

### 5、git操作最基本流程

- 创建工作目录，进行代码编写操作
- git  add  ./    命令
  - git hash-object -w 文件名    将文件保存到版本库
  - git update-index    将文件保存到暂存区
- git  commit -m  '注释'
  - git  write-tree  创建树对象
  - git  commit-tree  创建提交对象

### 6、git高层命令

工作目录的所有文件都不外乎一下两种状态：1、已跟踪。2、未跟踪

已跟踪文件的状态包括:已提交、已修改、已暂存

git --version 				  查看git版本

git init 							初始化仓库

git config --global user.name "jucunqi"				配置用户名

git config --global user.emall jucunqi@qq.com	配置邮箱

git config --list																		初始化配置

git add ./    					将修改添加到暂存区（理解底层操作）

git commit '注释'   		将暂存区提交到版本库

git commit -a   

git commit -a -m 

git status						查看文件状态

git diff	  						查看哪些修改还没有暂存

git  diff  --cache			  查看为提交的暂存

git log --oneline   			查看提交的历史记录

git log --oneline --decorate --graph --all  		查看所有历史分支命令

git rm  文件名 				删除文件目录中对应的文件，再讲修改添加到暂存区

git mv  原文件民    新文件名		将工作目录中的文件重命名，再将修改添加到暂存区

git ls-files -s 					查看暂存区

### 7、git分支操作

**分支的本质是：**其实就是一个提交对象

HEAD是一个指针，默认指向master分支

​		切换分支时，其实就是让HEAD指向不同的分支

​		每次有新的提交时，HEAD都会带着当前指向的分支一起向前移动

git branch						 显示分支列表

git branch -v					查看分支指向最新的提交

git branch 分支名			 新建分支

git checkout 分支名		 切换分支

git branch -d 分支名  	   删除空的分支，或者已经合并过的分支

git branch -D 分支名		强制删除分支

git branch 分支名 commithash	新建分支并指向提交对象

git checkout -b 分支名     新建分支并切换到该分支

**给命令起别名**

简单操作命令直接去掉在命令处去掉git即可，复杂命令需要使用“”包裹

git config --global alias.别名 status

git config --global alias.别名 “log --oneline --decorate --graph --all”			给查看所有历史分支命令起别名

### 分支操作小案例

1、master已经提交过两个版本

2、早上上班，通知有个53号bug需要修改，创建分支进行修改。

3、修改到50%，老板说有重大bug需要修改。创建hotbug分支先去修改重大bug。

​		先将已经修改50%的iss53分支代码提交，然后再在master下创建分支，修改重大bug。

​		修改完重大bug后，与master分支进行合并。合并后将hotbug分支删除

​		再切换到iss53分支，继续修复53bug。

​		修复完成后切换到master分支进行合并

4、进行分支合并，并处理冲突

​		切换到主分支，先执行git add ./ 命令 表示处理冲突

​		然后找到发生冲突的代码部分，因为代码都是自己修改的，自己将冲突部分进行修改。

​		修改完成后git commit 并将iss53分支删除。

### 8、存储

​		比如像上述的案例中，iss53号bug只处理到了一半，就必须提交才可以切换分支。使用存储命令，即可解决这种问题。

​		使用命令：git stash		存储

​		git stash list 					查看存储列表

​		git stash apply 栈中的键 	从栈中获取存储信息

​		git stash drop  栈中的键	 删除栈中指定的存储信息

### 9、撤回

1、工作区

​		如何撤回在工作区中的修改：git restore 文件名

2、暂存区

​		如何撤回在暂存区中的修改：git restore --staged 文件名

3、版本库

​		如何撤回对已提交对象的修改：

​		先将文件修改好，使用git add ./  命令

​		然后使用		git commit --amend  命令  重新修改注释在提交

### 10、reset三部曲

​		一、git reset --soft HEAD~

​				只动HEAD（带着分支一起移动）

​		二、git reset [--mixed] HEAD~

​				动HEAD（带着分支一起移动）

​				动暂存区

​		三、git reset --hard HEAD~

​				动HEAD（带着分支一起移动）

​				动工作目录

### 11、tag标签

​		查看所有版本信息：git tag

​		为当前指向添加版本信息：git tag 版本号

​		为指定hash对象添加版本号：git tag 版本号  hash

​		删除指定标签：git tag -d 版本号

​		查看指定版本信息：git show 版本号

​		将HEAD指向跳转到指定版本： git checkout 版本号（这样做会报警告，因为没有建立分支）

​															  git checkout 分支名 （在这个指向上创建一个分支）

### 12、团队协作

1、创建远程仓库（github）

  		一定要初始化空仓库

2、创建本地仓库

​		git init 初始化仓库

​		git remote add 别名  仓库地址

​		修改用户名、邮箱

​		git add

​		git commit

3、将本地仓库推送到远程仓库

​		git push 别名 分支	（输入用户名、密码，之后会附带生成远程跟踪分支）

​		上传时需要创建SSH秘钥 命令ssh-keygen -t rsa -C '1434002120@qq.com'，复制公钥

​		![](E:\笔记\Git\Git&GitHub.assets\20180309203907257.png)

4、项目经理邀请成员进入项目

​		成员同意邀请

5、成员clone克隆远程文件

​		git clone 仓库地址	（在本地生成.git文件	默认为远程仓库配置了别名	orgin）

6、成员作出贡献

​		修改源码文件

​		git add

​		git commit

​		git push 别名 分支名	（输入用户名、密码，之后会附带生成远程跟踪分支）

7、项目经理更新修改

​		git fetch 别名  （将修改同步到远程跟踪分支上）

​		git merge  合并远程跟踪分支。

## 二、GitHub

### 1、目的

使用GItHub托管代码

### 2、基本概念

#### 仓库（Repository）

仓库用来存放项目代码，每个项目对应一个仓库，多个开源项目则有多个仓库

#### 收藏（Star）

收藏项目，便于下次查看

#### 复制克隆项目（fork）

注意：克隆项目后，修改代码是不与其他人共享的。

#### 发起请求（pull request）

fork仓库并修改代码后，可以发送请求给克隆的人，克隆的人同意请求后，便可同步仓库。

![](E:\笔记\Git\Git&GitHub.assets\GitHub.png)

#### 关注（Watch）

关注项目，项目更新是可以接收到通知

#### 事务卡片（issue）

发现代码bug，但是目前没有成型代码，需要讨论是使用；

#### 三个重要页面

- GitHub主页
- 仓库主页
- 个人主页	

### 3、创建仓库

#### 1、仓库管理

对文件的简单操作

#### 2、GitHub Issue

可以给别人提交bug信息，别人也可以给你提交bug信息，可以在GitHub主页查看到相关信息。并且可以对提交信息的用户进行回复。

#### 3、开源项目贡献流程

1、fork仓库

2、创建issue

​		给创建者提供意见

3、clone复制项目、修改代码

4、poll request请求

5、等待作者审核合并项目



**修改仓库类型**

在仓库根目录下创建文件，名为.gitattributes

**添加内容：**

*.js linguist-language=java
*.css linguist-language=java





