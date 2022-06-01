## 序言
在使用Git之前，我们先了解一下Git的配置，并对一些必要的配置赋值。

### 配置分类
按照配置文件的所在，以Linux系统为例子，可以分为3类

- 系统级别：`/etc/gitconfig`
- 用户级别：`~/.gitconfig` 或 `~/.config/git/config`
- 项目级别：`.git/config`

### 如何更新配置
一般通过 `git config` 命令来更新配置

- 系统级别：`git config --system ...`
- 用户级别：`git config --global ...`
- 项目级别：`git config --local ...`

> 一般使用 `git config --global` 来更新配置。

### 配置的优先级
`项目级别 > 用户级别 > 系统级别`

### 查看配置
如下命令，默认展示用户级别的配置
```
git config --list 
```

### 基本配置
使用Git最基本的配置就两个，用户名和邮箱，用来作为提交信息。
```
git config --global user.name "hombiochen"
git config --global user.email "hombiochen@163.com"
```



