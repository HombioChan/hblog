## 打工人的settings
主要配置下载依赖的仓库即可，保证依赖的可下载和快速下载。
### 没有内部仓库管理器
如果没有内部使用的仓库管理器，配置一个中央仓库镜像即可，这里选择阿里云的Maven镜像，其他都是用默认配置。
```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
  
  <mirrors>
    <mirror>
      <id>aliyun</id>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
 
</settings>

 
```
### 有内部管理器
如果公司内部有一个Maven的仓库管理器，一般为Nexus，可以使用以下配置。

> 说明:   
> 1）`#{...}` 需要根据实际上下文替换  
> 2）由于是内部仓库，可能开发环境会使用到SNAPSHOT包，所以仓库要启动SNAPSHOT下载，默认是禁止的  
> 3）如果Nexus开启了连接身份认证，那么需要配置`server`

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
    <servers>
        <server>
            <id>internal-nexus</id>
            <username>#{username}</username>
            <password>#{password}</password>
        </server>
    </servers>

    <profiles>
        <profile>
            <id>internal-profile</id>
            <properties>
                <internal-nexus-url>#{internalNexusURL}</internal-nexus-url>
            </properties>
            <repositories>
                <repository>
                    <id>internal-nexus</id>
                    <name>Internal Nexus Repository</name>
                    <url>${internal-nexus-url}</url>
                    <layout>default</layout>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>

            <pluginRepositories>
                <pluginRepository>
                    <id>internal-nexus</id>
                    <name>Internal Nexus Repository</name>
                    <url>${internal-nexus-url}</url>
                    <layout>default</layout>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
    </profiles>

    <activeProfiles>
        <activeProfile>internal-profile</activeProfile>
    </activeProfiles>

</settings>
```