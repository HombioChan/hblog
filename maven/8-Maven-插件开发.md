## Maven 插件开发
Maven的拓展性是由插件实现的，中央仓库已经积累了许多官方和第三方的插件，用户可以直接依赖使用。当已有插件无法满足当前需求时，用户也可以按照官方提供的插件开发规范，实现自己的插件。

插件开发大致可以分为下面3个步骤
1. 声明`pom.xml`
2. 开发`Mojo`
3. 测试

## 案例
### 0. 环境信息
```
Apache Maven 3.6.3
Java version: 1.8.0_291, vendor: Oracle Corporation
```
### 1. 声明`pom.xml`
- packaging = maven-plugin
- 引入依赖：maven-plugin-api、maven-plugin-annotations
- 使用插件：maven-compiler-plugin、maven-plugin-plugin  (均使用当前最新版本[--传送门--](https://maven.apache.org/plugins/index.html))

完整配置如下
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.hombio</groupId>
    <artifactId>hombio-maven-plugin</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>maven-plugin</packaging>
    <name>Sample Parameter-less Maven Plugin</name>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-plugin-api</artifactId>
            <version>3.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.plugin-tools</groupId>
            <artifactId>maven-plugin-annotations</artifactId>
            <version>3.4</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-plugin-plugin</artifactId>
                <version>3.6.4</version>
            </plugin>
        </plugins>
    </build>

</project>
```

### 2. 开发Mojo
```
//插件任务名称：tell
@Mojo(name = "tell")
public class GreetingMojo extends AbstractMojo {

    //定义参数
    @Parameter(property = "hombio.name", required = false, defaultValue = "hombio")
    private String name;

    public void execute() throws MojoExecutionException, MojoFailureException {
        //任务逻辑：简单打印名称
        getLog().info("Hello: "+ name);
    }

}
```

### 3. 测试
安装到本地仓库
```
mvn clean install
```
直接在命令行运行插件任务
```
mvn top.hombio:hombio-maven-plugin:1.0-SNAPSHOT:tell -Dhombio.name=World
```
输出如下
```
[INFO] Scanning for projects...
[INFO]
[INFO] -------------------< top.hombio:hombio-maven-plugin >-------------------
[INFO] Building Sample Parameter-less Maven Plugin 1.0-SNAPSHOT
[INFO] ----------------------------[ maven-plugin ]----------------------------
[INFO]
[INFO] --- hombio-maven-plugin:1.0-SNAPSHOT:tell (default-cli) @ hombio-maven-plugin ---
[INFO] Hello: Hello=World
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.202 s
[INFO] Finished at: 2022-01-24T17:20:49+08:00
[INFO] ------------------------------------------------------------------------
```

## 小结
以上只是一个简单的案例，没有写单元测试用例，对于执行复杂逻辑的插件，最好参考官方插件编写单元测试用例，保证功能的正确性，然后再部署到远程仓库，供其他用户使用。