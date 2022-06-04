## 序言
在团队协作环境中，仅仅掌握本地分支操作是不够的，还需要与远程分支打交道。

远程分支命名规范：`<remote>/<branchName>`

- remote：远程仓库地址简称
- branchName：分支名称

克隆仓库时，默认的分支名称一般是：`origin/master`

### 推送本地分支到远程分支
如果本地新建了一个分支，远程仓库并没有，可以通过以下命令共享
```
git push <remote> <brnachName>
```
比如，推送一个新的`develop`分支
```
git push origin develop
```

### 拉取远程分支
```
git fetch <remote>
```
承接上面的例子，运行
```
git fetch origin
```
那么本地会生成一个不可变的`origin/develop`指针，如果想在这个分支上工作
```
> git checkout -b develop origin/develop
> git pull
```

### 跟踪远程分支
比如本地新增分支`develop`，并跟踪远程分支`origin/develop`。
```
> git branch -a develop
> git checkout develop
> git checkout --trace origin/develop
```
等价于
```
> git checkout -b develop
> git branch -u origin/develop
```
等价于
```
> git checkout -b develop origin/develop
```
等价于
```
> git checkout develop
```
如果本地分支都关联了远程分支后，`git pull`会自动地识别去哪个服务器上抓取、合并到哪个本地分支。

#### 如何查看本地分支跟踪的远程分支
```
git branch -vv
```
### 删除远程分支
```
git push <remote> --delete <branchName>
```
