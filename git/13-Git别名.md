## 序言
Git支持为命令起别名，提高用户使用体验。

### 如何起别名
比如，给`commit`取一个别名`ci`
```
git config --global alias.ci commit
```
之后就可以使用`git ci` 来替代 `git commit`了


### 常用的别名
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.lp "log --pretty=format:'%h [%cn](%ar) %s' --graph"
git config --global alias.cm "commit -a -m"
git config --global alias.cfg "config --global"
```