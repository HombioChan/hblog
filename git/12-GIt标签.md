## 序言
像其他版本控制系统（VCS）一样，Git 可以给仓库历史中的某一个提交打上标签，**以示重要**。 比较有代表性的是人们会使用这个功能来标记发布结点（ v1.0 、 v2.0 等等）。

### 列出标签
下面三个命令是等价的，可以列出当前仓库的所有标签
```
git tag
git tag -l
git tag --list
```
这个命令也支持模糊查询，不过要带上`-l`或`-list`，比如只查询 `v1.8.5` 相关的提交。
````
git tag -l "v1.8.5*"
````

### 创建标签
Git支持两种类型的标签：`lightweight` 和 `annotated`

#### lightweight
和每次提交Git生成的摘要一样，只是一个指向提交的引用。
```
git tag <what>
```
例如
```
git tag v1.0
```

#### annotated
是存储在Git数据库中的一个完整对象，可以被校验，与提交一样，包括用户名、邮箱、日期、说明信息。
```
git tag -a <what> -m <comment>
```
例如
```
git tag -a v1.0 -m "add a tag"
```
#### 如何选择
如果标签是临时的，使用 `lightweight` 类型的标签。

#### 指定提交版本
默认情况下，不指定版本的话，是给当前最新的提交版本打标签的。

如果要给以前的提交版本打标签的话，首先需要拿到提交版本的引用值，可以通过查看提交历史获得
```
git log --pretty=oneline
c7219c1d43173db3821c170b577603cdadf0ba70 (HEAD -> master) reupdate a.txt
...
```
如上，`c7219c1d43173db3821c170b577603cdadf0ba70` 即为提交的引用值，接着就可以打标签了
```
//lightweight
git tag v1.0 c7219c1d43173db3821c170b577603cdadf0ba70
//annotated
git tag -a v1.0 c7219c1d43173db3821c170b577603cdadf0ba70 -m "add a tag"
```

### 共享标签
默认情况下，`git push`命令并不会把标签推送到远程仓库服务器上，所以如果要共享标签，需要手动推送
```
git push <serverName> <tagName>
```
比如
```
git push origin v1.0
```
如果想一次性推送
```
git push <serverName> --tags
```

### 删除标签
#### 删除本地标签
```
git tag -d <tagName>
```
#### 删除远程仓库标签
有两种方式
```
1. git push <serverName> --delete <tagName>
2. git push <serverName> :refs/tags/<tagName>
```
比如
```
git push origin --delete v1.0
或者
git push origin :refs/tags/v1.0
```

### 检出标签
如果只是查看某个标签指向的文件版本
```
git checkout <tagName>
```
如果除了查看之外，还要修改，一般会新建一个分支，避免修改丢失
```
git checkout -b <newBranchName> <tagName>
```

