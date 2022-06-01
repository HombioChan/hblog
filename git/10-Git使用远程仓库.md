## 序言
为了能在任意 Git 项目上协作，你需要知道如何管理自己的远程仓库。

### 查看远程仓库
```
git remote -v
```

### 新增远程仓库
```
git remote add <name> <url>
```
比如
```
git add pb https://github.com/paulboone/ticgit
```

### 删除远程仓库
```
git remote remove <name>
```

### 重命名远程仓库
```
git remote rename <old> <new>
```
### 查看某个远程仓库的详细信息
```
git remote show <name>
```

### 从远程仓库拉取信息
```
git fetch <name>
```

### 推送提交到远程仓库
```
git push <name>
```
