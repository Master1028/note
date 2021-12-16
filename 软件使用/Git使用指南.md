[toc]
## Git使用指南


Git操作的过程中突然显示Another git process semms to be running in this repository, e.g. an editor opened by ‘git commit’. Please make sure all processes are terminated then try again. If it still fails, a git process remove the file manually to continue…
翻译过来就是git被另外一个程序占用，重启机器也不能够解决。

原因在于Git在使用过程中遭遇了奔溃，部分被上锁资源没有被释放导致的。

解决方案：进入项目文件夹下的 .git文件中（显示隐藏文件夹或rm .git/index.lock）删除index.lock文件即可。
————————————————
版权声明：本文为CSDN博主「迟到的月亮」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_32193151/article/details/70792594

#### 引言![gitcommand](D:\Desktop\gitcommand.jpg)
git 的使用 分为三个区域，分别是，工作区，暂存区，本地库。
工作区就是你电脑中文件夹内；
暂存区是你将工作区的文件添加后暂时放置的地方；
本地库是存储版本和你的文件的地方；
只有将文件添加进了本地库才可以使用git对其进行管理。

#### 准备工作
在使用git前需要一些准备工作：
1.设置签名--目的是让git知道是谁在进行版本管理
git config --gobal user.name "你的名字"
git config --gobal user.email "你的邮箱"
2.初始化本地库--也就是建立本地库，只有文件夹内建立了本地库，才能进行一系列的操作
git init
3.移动位置--可以直接在文件夹内git bash here,也可以通过指令进入
cd 文件明或路径

#### 添加文件到本地库
1.先将文件夹内文件添加到暂存区
上传全部文件：git add *
上传部分文件:git add 文件名
2.再将暂存区的文件上传到本地库
git commit -m "你的注释"
注释的目的是让你查看的时候知道你是为什么进行上传，以及你的更改

#### 根据日志进行版本更换
查看日志：
1.git log
2.git log --pretty-oneline
3.git log --oneline
4.git reflog 查看回退版本  
第四种查看方式多了回到旧版本需要几步的信息
回退版本：
1.本地库、工作区、暂存区都移动git reset --hard 索引
2.本地库、暂存区移动，工作区不移动 git reset --mixed 索引
3.本地库移动，其余不懂 git reset --soft 索引

#### 本地库和原地库的交互
1.本地库推送至远程库
git remote add origin 你的远程库地址  // 把本地库与远程库关联
git push -u origin master    // 第一次推送时

git push origin master  // 第一次推送后，直接使用该命令即可推送修改

2.克隆远程库到本地库
git clone
3.拉取远程库到本地库






