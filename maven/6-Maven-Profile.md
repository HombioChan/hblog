## Maven Profile
### 什么是Profile
Profile就是一份配置，一般由条件触发生效。

### 解决什么问题
解决无法避免的平台依赖性问题，比如
- 插件需要配置本地文件系统路径
- 不同平台下，依赖集不同
- ...

### 配置所在
- setting.xml
- pom.xml

### Profile中可用的配置元素
profile所在的配置文件不同，可用的配置元素也不同。
#### setting.xml
在setting.xml文件中，profile只能配置下面3个元素
- repositories
- pluginRepositories
- properties

#### pom.xml
pom.xml的元素大部分都可以在profile中配置。
-   `<repositories>`
-   `<pluginRepositories>`
-   `<dependencies>`
-   `<plugins>`
-   `<properties>`
-   `<modules>`
-   `<reports>`
-   `<reporting>`
-   `<dependencyManagement>`
-   `<distributionManagement>`
-   `<build>`
    -   `<defaultGoal>`
    -   `<resources>`
    -   `<testResources>`
    -   `<directory>`
    -   `<finalName>`
    -   `<filters>`
    -   `<pluginManagement>`
    -   `<plugins>`
    
### 触发生效
#### 1.通过命令行
如下，在命令行中指定-P参数，可以指定激活的profile列表，以`,`分隔，其中`?`表示profile是可选的，当配置解析失败时可以跳过，避免执行失败。
```
mvn groupId:artifactId:goal -P profile-1,profile-2,?profile-3
```

#### 2.通过settings.xml
这里激活的是在`settings.xml`中声明的profile
```
<settings>
  ...
  <activeProfiles>
    <activeProfile>profile-1</activeProfile>
  </activeProfiles>
  ...
</settings>
```

#### 3.默认激活
```
<profiles>
  <profile>
    <id>profile-1</id>
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
    ...
  </profile>
</profiles>
```

#### 4.通过条件激活
1) JDK版本  

```
<profiles>
  <profile>
    <activation>
      <jdk>1.4</jdk>
    </activation>
    ...
  </profile>
</profiles>
```
2) OS信息  

```
<profiles>
  <profile>
    <activation>
      <os>
        <name>Windows XP</name>
        <family>Windows</family>
        <arch>x86</arch>
        <version>5.1.2600</version>
      </os>
    </activation>
    ...
  </profile>
</profiles>
```

3) 系统属性  

```
// a. 存在系统属性debug即生效
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>

// b. 不存在系统属性debug即生效
<profiles>
  <profile>
    <activation>
      <property>
        <name>!debug</name>
      </property>
    </activation>
    ...
  </profile>
</profiles>

// c. 存在系统属性debug=true即生效
<profiles>
  <profile>
    <activation>
      <property>
        <name>debug</name>
        <value>true</value>
      </property>
    </activation>
    ...
  </profile>
</profiles>
```
4) 文件是否存在  

- `exists`
- `missing`

```
<profiles>
  <profile>
    <activation>
      <file>
        <missing>target/generated-sources/axistools/wsdl2java/org/apache/maven</missing>
      </file>
    </activation>
    ...
  </profile>
</profiles>
```

### 检查profile是否生效
```
mvn help:active-profiles
```
### 撤销生效的profile
在命令行中profileId叫前缀`!`或者`-`可以撤销已经触发生效的profile。
```
mvn groupId:artifactId:goal -P !profile-1,!profile-2,!?profile-3
```
### Profile读取顺序
同一个配置文件，相同的配置，后定义的优先级高。
```
<project>
  ...
  <repositories>
    <repository>
      <id>global-repo</id>
      ...
    </repository>
  </repositories>
  ...
  <profiles>
    <profile>
      <id>profile-1</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>profile-1-repo</id>
          ...
        </repository>
      </repositories>
    </profile>
    <profile>
      <id>profile-2</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>profile-2-repo</id>
          ...
        </repository>
      </repositories>
    </profile>
    ...
  </profiles>
  ...
</project>
```
以上配置，最终仓库搜索的优先级为：`profile-2-repo > profile-1-repo > global-repo`