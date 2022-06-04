## 序言
本地创建好仓库后，就可以使用Git进行版本控制工作了，一般步骤为
1. 修改工作区文件
2. 提交修改到缓存区
3. 提交修改到本地仓库
4. 同步修改到远程仓库

以下例子使用的Git版本为
```
> git --version
git version 2.35.1
```

### 修改工作区文件

> 修改包括新增、删除、修改文件。

使用`git init`初始化一个仓库后，新增一个文件`a.txt`
```
> touch a.txt
```
接着执行 `git status` 查看当前工作区状态，输出如下
```
> git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	a.txt

nothing added to commit but untracked files present (use "git add" to track)
```
根据提示，`a.txt` 是一个未被跟踪的文件，可以通过 `git add` 命令来跟踪。

> 文件修改、删除类似

### 提交到缓冲区
在工作区修改文件后，通过执行`git add`命令可以将修改提交到缓存区。

承接上面的例子，输出如下。
```
> git add .
> git status

On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   a.txt
```
输出有两点提示

1. 修改已经可以被提交
2. 通过命令`git rm --cached`将修改回退到工作区。


### 提交修改到本地仓库
```
> git commit -m "add a.txt"
[master (root-commit) a350eda] add a.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 a.txt
```

### 同步修改到远程仓库
首先执行以下命令，检查是否存在远程仓库
```
> git remote -v
```
如果存在的话，假设远程仓库名称为origin，那么有必要将修改同步到远程仓库（前提是有推送权限）
```
> git push origin
```
远程仓库默认名称为 `origin`, 一般可以省略掉
```
> git push
```