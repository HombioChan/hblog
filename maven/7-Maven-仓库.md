## Maven 仓库
Maven打包的产物，官方定义为artifact，我们不妨称之为构件，这些构件存放的地方，即为仓库。

根据位置的不同，仓库可以分为：
- 本地仓库
- 远程仓库


## 仓库配置
### 本地仓库配置
本地仓库只有一个配置，即本地仓库地址，在`settings.xml`文件中。
```
<settings>
    <localRepository>...</localRepository>
</settings>
```
### 远程仓库配置
远程仓库可以在`pom.xml`，也可以在`settings.xml`文件中配置，这些配置在查找下载固件的时候会被使用到。
```
  ...
  <repositories>
    <repository>
      <releases>
        <enabled>false</enabled>
        <updatePolicy>always</updatePolicy>
        <checksumPolicy>warn</checksumPolicy>
      </releases>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>never</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
      </snapshots>
      <name>Nexus Snapshots</name>
      <id>snapshots-repo</id>
      <url>https://oss.sonatype.org/content/repositories/snapshots</url>
      <layout>default</layout>
    </repository>
  </repositories>
  <pluginRepositories>
    ...
  </pluginRepositories>
  ...
```
- releases：发布版本的构件组
- snapshots：快照版本的构件组
- enabled：是否启用
- updatePolicy：多久执行一次更新操作，可选值：always、daily（默认值）、interval:X（X-分钟）、never
- checksumPolicy：检文件校验不通时的行为，可选值：ignore、fail、warn
- name/id/url：仓库名称/ID/URL


`pluginRepositories`中的配置与`repositories`中的一致。

> 如果用户不声明仓库配置，默认继承`Super POM`。

### 如何区分快照版本和发布版本
- 快照版本：不稳定版本，`vserion` = `*-SNAPSHOT`
- 发布版本：稳定版本，其他情况的`version`

一个版本的快照构件可以覆盖仓库中的旧构件，而发布版本是禁止的。

### 远程仓库搜索顺序
Maven会按照以下顺序搜索仓库地址查找下载构件，直到返回一个正确的结果。
1. 全局settings.xml
2. 用户settings.xml
3. 项目pom.xml
4. 父pom.xml（递归）
5. Super POM

## 仓库管理器
仓库管理器是一个用于管理仓库的服务器应用，可以简单理解为一个私有远程仓库。

可用的开源仓库管理器
- [Sonatype Nexus OSS](https://www.sonatype.org/nexus/go/)
- ...

### 配置仓库管理器为公共仓库代理服务器
在`settings.xml`中配置`mirrors`元素
```
<settings>
  ...
  <mirrors>
    <mirror>
      <id>${mirror_id}</id>
      <name>${mirror_name}</name>
      <url>${respository_manager_url}</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  ...
</settings>
```
## 安装到本地仓库
### 可控项目
```
mvn install
```
### 第三方构件
有些第三方构件不存在公共仓库，而构建需要使用到，如果你拥有构件（JAR包），可以通过`install:install-file`将构件安装到本地仓库。
```
mvn install:install-file\
    -Dfile=<path-to-file>\
    -DgroupId=<group-id>\
    -DartifactId=<artifact-id>\
    -Dversion=<version>\
    -Dpackaging=<packaging>
```

## 部署到远程仓库
### 可控项目
```
mvn deploy
```
这时，一般在`pom.xml`中加入以下配置，指定上传仓库服务器的地址。
```
    <distributionManagement>
        <repository>
            <id>releases</id>
            <name>ReleasesProxy</name>
            <url>...</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>SnapshotsProxy</name>
            <url>...</url>
        </snapshotRepository>
    </distributionManagement>
```

如果远程仓库服务器需要进行身份认证，那么需要在`settings.xml`中的`servers`元素中配置客户端身份信息，比如账号密码，或者密钥。

### 第三方构件
```
mvn deploy:deploy-file -DgroupId=<group-id> \
  -DartifactId=<artifact-id> \
  -Dversion=<version> \
  -Dpackaging=<type-of-packaging> \
  -Dfile=<path-to-file> \
  -DrepositoryId=<id-to-map-on-server-section-of-settings.xml> \
  -Durl=<url-of-the-repository-to-deploy>
```

