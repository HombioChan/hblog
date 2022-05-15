## SNAPSHOT依赖更新
默认情况，如果配置了支持从仓库下载SNAPSHOT包，默认的更新策略是每天一次。

实际多人开发场景中，仓库中的SNAPSHOT一天内可能会被更新多次，为了保证本地使用的SNAPSHOT是最新的，我们需要有一种机制手动更新SNAPSHOT包。

### 命令行
Maven提供了`-U`参数，支持立即触发更新SNAPSHOT包
```
 -U,--update-snapshots        Forces a check for missing
                              releases and updated snapshots on
                              remote repositories
```

比如执行compile顺带更新SNAPTHOT包
```
mvn compile -U
```

### IDEA
打开IDEA/settings/Maven，勾选`aloway update snapshots`，后续点击Maven插件界面的`Reload All Maven Projects`按钮时，即会执行更新。
