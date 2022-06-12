## 序言
与远程仓库交互，一般有两种身份认证方式
1. 使用HTTPS协议(SSL)，每次提交都输入用户名和密码，即凭证
2. 使用GIT协议(SSH)，将公钥配置到远程仓库

这两种方式的仓库地址是不一样的，如下例子

- HTTPS：https://github.com/xxx/xxx.git
- GIT：git@github.com/xxx/xxx.git

### HTTPS
Git 拥有一个凭证系统来处理这件事情，即`git credential`，配置命令如下
```
git config --global credential.helper <type>
```

`type` 有下面5种选择

- 不填，默认不缓存，每次连接都会询问凭证
- cache，凭证存储在内存中，15min后清除
- store，以明文的方式永久存储在硬盘中，除非密码发生修改，否则连接不需要重新输入凭证。
- osxkeychain， Mac专用，以密文的方式永久存储在硬盘中
- manager，Windows专用，以密文的方式永久存储在硬盘中，需要安装`Git Credential Manager for Windows`辅助工具

其中，`store` 可以指定凭证存储的文件路径，默认为`~\.git-credentials`
```
git config --global credential.helper 'store --file ~/.my-credentials'
```
`cache` 可以指定凭证超时时间，单位 `s`，默认 900s，即15min
```
git config --global credential.helper 'cache --timeout 30000'
```

Git允许同时配置多种凭证类型，在查找特定服务器的凭证时，Git会按顺序查找，直到找到第一个回答。
```
[credential]
    helper = store --file /mnt/thumbdrive/.git-credentials
    helper = cache --timeout 30000
```

### GIT
与使用 `SSH` 协议登录 `Linux`系统一样，在服务器配置好本机公钥之后，每次连接服务器无需任何操作。

在服务器配置本机公钥的步骤如下：

1. 本地使用`ssh-keygen`先生成公私钥，默认输出路径`~/.ssh`，如果已存在，跳到步骤2
2. 找到公钥文件 `~/.ssh/id_rsa_pub`
3. 将公钥配置到服务器