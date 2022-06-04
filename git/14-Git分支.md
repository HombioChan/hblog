### 创建分支
```
git checkout -b <newBranchName> <commit>
```
比如从当前分支`master`的最新提交检出分支`develop`
```
git checkout -b develop master
```
其中，master可以省略
```
git checkout -b develop
```
如果从历史master分支的历史提交检出版本，先通过`git log`找到指定提交的引用值，假设为`1def12`，那么创建命令为
```
git checkout -b branch-new 1def12
```

### 删除分支
删除已经被合并的分支
```
git branch -d <branchName>
```
如果分支未被合并，那么上面的命令会提示无法删除，需要通过以下命令强制删除
```
git branch -D <branchName>
```

### 重命名分支
同理删除，如果使用`-M`，表示强制修改，及时新的标签名称已经存在。
```
git branch -m <oldBranchName> <newBranchName>
```

### 分支切换
下面两条命令等价
```
git checkout <toBranchName>
git switch <toBranchName>
```

### 分支查看
#### 简单的查看分支列表，前缀的`*`表示当前所在分支。
```
> git branch
  develop
* master
```
#### 查看每个分支最后一次提交
```
> git branch -v
  develop 8eb22e9 add b.txt
* master  c7219c1 reupdate a.txt
```
#### 查看哪些分已经被合并到当前分支
这在删除分支的时候有一定的辅助作用，如果分支已经被合并，那么就可以通过`git branch -d`放心地删除了。
```
git branch --merged
```
#### 查看哪些分支没有被合并到当前分支
```
git branch --no-merged
```
### 查看跟踪的远程分支
```
git branch -vv
```

### 分支合并
将当前分支与指定分支合并
```
git merge <targetBranch>
```

