## 序言
我们的修改会经历工作区->暂存区->本地仓库三个阶段，在每个阶段，都有可能要撤销某些操作，本节来看看各个阶段的撤销操作。

### 工作区中的撤销
假设当前工作区有文件`a.txt`，打开文件进行修改，添加几行文字，运行以下命令，可以看到输出
```
> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   a.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
输出的内容已经提示了我们如何撤销文件的修改，每个版本的Git提供的命令不尽相同，这里的是`git restore <file>`，所以我们只需要执行下面的命令就可以撤销对文件`a.txt`的修改，不过要注意加谨慎，**工作区的撤销是不可恢复的**。
```
> git restore a.txt
```

### 暂存区的撤销
把`a.txt`的修改从工作区提交到暂存区，执行以下命令
```
> git add a.txt
> git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   a.txt
```
和工作区中一样，这里的输出也提示了我们如何撤销从工作区到暂存区的提交，即
```
> git restore --staged a.txt
```

### 本地仓库的撤销
#### 情况1：丢弃最新版本，回退到上个版本
```
> git reset --hard HEAD~
```
这只是改动指针的指向，实际上最新的版本还是存在于本地仓库的，如果撤销后悔了，可以通过`git reflog`查询HEAD指针日志，找到最新索引的哈希值，再reset回去。
```
> git reflog 
c9e0adf (HEAD -> master) HEAD@{2}: reset: moving to HEAD~
1e8a24c HEAD@{3}: commit: update a.txt
...
```
一般即为第二条，如上最新提交的索引为`1e8a24c`，执行下面命令即可撤销回去
```
> git reset 1e8a24c
> git restore .
```

#### 情况2：只是撤销提交的某个文件
比如撤销文件`a.txt`
```
> git restore HEAD~ a.txt
> git restore a.txt
> git commit -m "reupdate a.txt"
```
第一条命令，会将`a.txt`最新的内容回滚到工作区，这部分内容是我们要撤销的，所以要再执行第二条命令，执行完后需要再次提交一下，最后的结果虽然新增了一个提交，但是达到了撤销某个文件修改的目的。

> 在某些情况下，我们需要撤销某个文件的修改，而又不知道修改是什么的时候，这种撤销方式是非常有用的。

### 覆盖最新提交
覆盖最新提交在某种角度上也能起到撤销的作用。有时候，我们提交之后，发现漏了一些文件、或者少了一些修改，或者提交说明不合适，这个时候就可以使用覆盖最新提交。

```
> git commit --amend -m "new comment"
```

覆盖最新提交会以覆盖的方式创建一个新的提交，看上去之前最新的提交被撤销了一样。

