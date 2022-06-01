## 序言
在提交了若干更新，又或者克隆了某个项目之后，你也许想回顾下提交历史。 完成这个任务最简单而又有效的工具是`git log`命令。

### 使用
默认情况下，只会列出每次提交的3个基本信息：作者、日期、提交说明。
```
> git log
Author: hombiochen <hombiochen@163.com>
Date:   Wed Jun 1 20:04:49 2022 +0800

    udpate a.txt

...
```
如果想查看每次提交引入的修改，可以加`-p`参数，相对于每次提交后都会输出和上个版本的比对信息。
```
> git diff -p
commit c9e0adf2801687d8b5b38dbd09a965db85a5ad8e (HEAD -> master)
Author: hombiochen <hombiochen@163.com>
Date:   Wed Jun 1 20:04:49 2022 +0800

    udpate a.txt

diff --git a/a.txt b/a.txt
index f534deb..ea9b79e 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 Hello World.
+Hello World.
...
```
如果只是想看修改的统计信息，可以加`--stat`参数
```
> git log --stat
commit c9e0adf2801687d8b5b38dbd09a965db85a5ad8e (HEAD -> master)
Author: hombiochen <hombiochen@163.com>
Date:   Wed Jun 1 20:04:49 2022 +0800

    udpate a.txt

 a.txt | 1 +
 1 file changed, 1 insertion(+)
```
另外一个常用的参数是 `--pretty`，可以自定义输出的格式，比如最简单的，每个提交只输出一行。
```
> git log --pretty=oneline
c9e0adf2801687d8b5b38dbd09a965db85a5ad8e (HEAD -> master) udpate a.txt
8eb22e92fad800ff62c1eecd58d8376a42ead64d (devlop) add b.txt
1274a04513710ed775e77be115b0e773eef4d303 update a.txt
a350eda29b33fc40d0eb8d14712bf6b28b2bf736 add a.txt
```
上面这种输出还不够简洁，信息量也不够，我们可以再自定义一下，参数细节参考 [官方文档](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2) 。
```
> git log --pretty="format:%h [%cn](%ar) %s"
c9e0adf [hombiochen](26 minutes ago) udpate a.txt
8eb22e9 [hombiochen](34 minutes ago) add b.txt
1274a04 [hombiochen](73 minutes ago) update a.txt
a350eda [hombiochen](4 hours ago) add a.txt
```
与`--pretty` 一起搭配使用的有 `--graph`，可以显示分支的合并历史。
```
> git log --pretty="format:%h [%cn](%ar) %s" --graph
* c9e0adf [hombiochen](29 minutes ago) udpate a.txt
* 8eb22e9 [hombiochen](37 minutes ago) add b.txt
* 1274a04 [hombiochen](76 minutes ago) update a.txt
* a350eda [hombiochen](4 hours ago) add a.txt
```
