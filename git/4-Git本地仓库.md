## 序言
熟悉了Git配置并配置了基本信息之后，接下来就可以开始正式使用Git了，首先需要有一个本地仓库。

有两种方式可以创建一个本地仓库
1. 本地初始化
2. 从远程仓库克隆

### 本地初始化
创建好项目文件夹，执行`git init`即可，比如在我的电脑
```
> cd ~/workspace/git
> mkdir git-demo1
> cd git-demo1
> git init
```
执行完命令后，在当前目录下会生成一个`.git`的目录，这个就是本地仓库。

### 从远处仓库克隆
可以到公共的代码仓库平台，比如`gitee`，`github`，`gitlab`，创建一个项目，或者选择一个开源的项目，获取到下载链接，然后使用 `git clone`命令克隆到本地，比如

```
git clone https://github.com/libgit2/libgit2
```