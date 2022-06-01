## 序言
与远程仓库交互，一般有两种身份认证方式
1. 使用HTTPS协议，每次提交都输出用户名和密码
2. 使用GIT协议，将公钥配置到远程仓库

这两种方式的仓库地址是不一样的，如下例子
- HTTPS：https://github.com/xxx/xxx.git
- GIT：git@github.com/xxx/xxx.git

### HTTPS
本质上每次提交都是要提供用户名和密码，不过可以通过启用缓存来存储用户名和密码，这样每次提交程序会自动从缓存获取，用户只需要在第一次或者密码发生改变的时候输入一次。
```
git config --global credential.helper store
```

### GIT
1. 本地使用`ssh-keygen`先生成公私钥，默认输出路径`~/.ssh`
2. 找到公钥文件 `~/.ssh/id_rsa_pub`
3. 将公钥配置到服务器