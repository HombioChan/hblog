## 序言
有时候，我们在工作区对文件做了很多修改之后，提交到暂存区之前想查看某个文件做了哪些修改，来决定是否要继续暂存，在提交修改到本地仓库之前也有这种需求，这个时候 `git diff`就派上用场了。

### 比较工作区与暂存区的文件差异
假设当前目录下有文件`a.txt`，内容如下，已暂存
```
Hello World.
```
使用`Vim`编辑，复制粘贴这一行(`yyp`)，内容如下
```
Hello World.
Hello World.
```
这个时候查看文件`a.txt`和暂存区的区别，输出如下
```
> git diff  a.txt
diff --git a/a.txt b/a.txt
index f534deb..ea9b79e 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 Hello World.
+Hello World.
```
为了方便说明输出的含义，给输出编一下行号。
```
1 diff --git a/a.txt b/a.txt
2 index f534deb..ea9b79e 100644
3 --- a/a.txt
4 +++ b/a.txt
5 @@ -1 +1,2 @@
6  Hello World.
7 +Hello World.
```
行1表示世纪使用`diff`命令来比对`a/a.txt`(暂存的文件)和`b/b.txt`(工作区的文件)

行2表示两个文件的哈希短索引，最后一个数字是文件描述符，100表示普通文件，644表示权限。

行3表示使用 `-` 代指 `a/a.txt`; 行4表示使用 `+` 代指 `b/a.txt`。

行5表示 `a/a.txt` 中的第1行和 `b/a.txt` 中的1-2行 产生差异。

行6表示 `a/a.txt` 和 `b/a.txt` 都有的行。

行7 `+` 号表示`b/a.txt` 独有的行，即新增的行，如果是`-`, 则是`a/a.txt`独有的行。

### 比较暂存区和本地仓库的差异
承接上面的例子，我们把修改提交的暂存区，再运行`git diff a.txt`，可以看到没有任何输出，表明工作区和暂存区的文件是一致的。
```
> git add .
> git diff a.txt
```
这时暂存区和本地仓库是有差异的，输出应该和上面的是一致的。
```
> git diff --cached a.txt
diff --git a/a.txt b/a.txt
index f534deb..ea9b79e 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 Hello World.
+Hello World.
```
当修改从暂存区提交到本地仓库后，再执行比对命令，是没有任何输出的，这个时候，工作区、暂存区和本地仓库的文件都是一致的。
```
> git commit -m "..."
> git diff a.txt
> git diff --cached a.txt
```

### 拓展
上述例子只是比对单个文件，当要比对所有修改的文件时，不需要指定文件名。
```
比对暂存区和工作区：git diff
比对本地仓库和暂存区：git diff --cached
```

除此之外，还可以比对两个版本，即比对两个不同版本的本地仓库。
```
git diff commit1 commit2 [file]
```