## Maven settings.xml
`settings.xml`是Maven的配置文件，一般存放于
- 全局空间：`${maven.home}/conf/settings.xml`
- 用户空间：`${user.home}/.m2/settings.xml`

如果两个文件都存在，那么会对配置进行合并，相同的配置下用户空间的优先。

安装Maven后，用户空间是不存在`settings.xml`的，一般都是复制`${maven.home}/conf/settings.xml`文件到用户空间下，然后进行配置，在用户空间所加的配置只对当前用户生效。

### 元素集
```
    <settings>
      <localRepository/>
      <interactiveMode/>
      <offline/>
      <pluginGroups/>
      <servers/>
      <mirrors/>
      <proxies/>
      <profiles/>
      <activeProfiles/>
    </settings>
```

### localRepository
本地仓库地址，默认值：`${user.home}/.m2/repository`

### interactiveMode
是否与用户交互获取输入，默认`true`

### offline
是否在离线模式情况下进行构建，默认`false`。

当构建的机器因为网络问题或者安全问题无法访问远程仓库时，且构建所用到的插件都支持离线模式，设置`offline=true`是一个不错的选择。

### pluginGroups
插件组ID列表，默认包含
- `org.apache.maven.plugins`
- `org.codehaus.mojo`

作用：CLI运行Maven命令使用插件时，如果没有指定插件组ID，会从这个列表中搜索。


比如运行
```
mvn org.eclipse.jetty:jetty:run
```
如果在`pluginGroups`中指定了`org.eclipse.jetty`，如下
```
<settings>
  ...
  <pluginGroups>
    <pluginGroup>org.eclipse.jetty</pluginGroup>
  </pluginGroups>
  ...
</settings>
```
那么可以简化命令为
```
mvn jetty:run
```

日常开发使用到的插件比如`compiler:compile`的组ID非`org.apache.maven.plugins`即`org.codehaus.mojo`，所以在CLI中运行命令无需指定组ID。

### servers
用于配置仓库/镜像服务器的连接信息
```
<settings>
  ...
  <servers>
    <server>
      <id>server001</id>
      <username>my_login</username>
      <password>my_password</password>
      <privateKey>${user.home}/.ssh/id_dsa</privateKey>
      <passphrase>some_passphrase</passphrase>
      <filePermissions>664</filePermissions>
      <directoryPermissions>775</directoryPermissions>
      <configuration></configuration>
    </server>
  </servers>
  ...
</settings>
```
- id：服务器ID，对应仓库/镜像的ID
- username/password：用户名/密码
- privateKey/passphrase：除了使用用户名/密码登录，也可以使用私钥登录（即SSH）。如果使用密钥登录，注意不要配置password，否则私钥会被忽略，导致登录失败。
- filePermissions/directoryPermissions：文件权限/目录权限

### mirrors
镜像服务器，包含指定仓库的全部内容，就近选择镜像服务器，可以加快依赖组件的上传与下载速度。
```
<settings>
  ...
  <mirrors>
    <mirror>
      <id>mirror_id</id>
      <name>China Mirror</name>
      <url>http://downloads.china.com/pub/maven2</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  ...
</settings>
```
- id：镜像服务器ID
- name：镜像服务器名称
- url：镜像服务器URL，国内的镜像有阿里、网易...
- mirrorOf：被镜像仓库的ID，central-中央仓库

### proxies
当只能通过指定代理服务器访问外网时可以使用。
```
<settings>
  ...
  <proxies>
    <proxy>
      <id>myproxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.somewhere.com</host>
      <port>8080</port>
      <username>proxyuser</username>
      <password>somepassword</password>
      <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
    </proxy>
  </proxies>
  ...
</settings>
```
### profiles
在settings.xml中的profiles可以使用以下元素，相比在pom.xml中的profiles，是一个裁剪集。
- repositories
- pluginRepositories
- properties

### activeProfiles
激活`settings.xml`中声明的profile
```
<settings>
  ...
  <activeProfiles>
    <activeProfile>env-test</activeProfile>
  </activeProfiles>
</settings>
```
