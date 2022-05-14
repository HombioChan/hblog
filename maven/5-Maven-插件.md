## Maven 插件使用
### 序言
Maven的设计目标之一是高拓展性，所有与构建绑定的任务都应该使用同一套接口标准实现，这个标准就是Maven的插件机制。

常见的构建任务，比如编译、单元测试、打包，官方已经提供了开箱即用的实现，你可以在 [这里](https://maven.apache.org/plugins/index.html) 查看其使用文档。


### 什么是插件
插件可以理解为一组任务的集合，用户可以通过命令行直接运行指定插件的任务，也可以将插件任务挂载到构建生命周期，随着生命周期运行。

### Maven插件分类
- Build Plugin
- Reporting Plugin

### 插件前缀
Maven对于插件artifactId的命名有以下规范
- 官方：`maven-${prefix}-plugin`
- 第三方：`${prefix}-maven-plugin`

如果插件命名符合上面这两个规范，在使用到插件的地方，可以使用prefix直接引用，Maven会自动关联上。
```
比如
mvn maven-compiler-plugin:compile
可以缩写为
mvn compiler:compile
```

如果用户想自定义插件前缀，可以在pom.xml中声明`goalPrefix`，如下
```
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <artifactId>maven-plugin-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          ...
          <goalPrefix>somePrefix</goalPrefix>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
这样的话，下面三个命令的效果是等效的
```
mvn maven-plugin-plugin:goal
mvn plugin:goal
mvn somePrefix:goal
```

### 如何使用插件
要使用插件，首先得知道插件有哪些可执行任务，每个插件都会包含`help`任务，输出插件的任务简介文档。
```
//以compiler为例
mvn compiler:help

...

This plugin has 3 goals:

compiler:compile
  Compiles application sources

compiler:help
  Display help information on maven-compiler-plugin.
  Call mvn compiler:help -Ddetail=true -Dgoal=<goal-name> to display parameter
  details.

compiler:testCompile
  Compiles application test sources.
```
如果想了解某个任务的使用详情，承上例子如下
```
mvn compiler:help -Ddetail -Dgoal=compile
```

#### 1. 直接运行
完整的命令行格式如下
```
mvn groupId:artifactId:version:goal
```
以compiler插件为例，坐标信息如下
```
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-plugins</artifactId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.9.0</version>
```
执行compile任务，完整的格式如下
```
mvn org.apache.maven.plugins:maven-compiler-plugin:3.9.0:compile
```
如果`pom.xml`在声明compiler插件时指定了groupId和version，那么命令可以缩写为
```
mvn maven-compiler-plugin:compile
```
如果未声明，groupId会从`settings.xml`中`pluginGroups`获取。

再根据插件前缀约定，我们可以直接使用`compiler`引用`maven-compiler-plugin`，所以命令可以进一步简写
```
mvn compiler:compile
```
#### 2. 挂载到构建周期阶段运行
接着上面的例子，修改`pom.xml`，将`compile`任务挂载到编译阶段`compile`
```
...
  <execution>
    <goal>compile</goal>
    <phase>compile</phase>
  </execution>
...
```
那么命令可以简写为
```
mvn compile
```

### 插件配置
#### 通用配置
每个插件任务对应插件项目中的一个MoJO类，这个类中属性就是任务的配置，下面为一个例子
```
public class MyQueryMojo
    extends AbstractMojo
{
    @Parameter(property = "query.url", required = true)
    private String url;
 
    @Parameter(property = "timeout", required = false, defaultValue = "50")
    private int timeout;
 
    @Parameter(property = "options")
    private String[] options;
 
    public void execute()
        throws MojoExecutionException
    {
        ...
    }
}
```
配置中的属性名称与MoJo中的属性名称一一对应。
```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-myquery-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <url>http://www.foobar.com/query</url>
          <timeout>10</timeout>
          <options>
            <option>one</option>
            <option>two</option>
            <option>three</option>
          </options>
        </configuration>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```
使用`@parameter`可以指定默认值，也可以将任务配置与系统属性相绑定，如time属性，非必传，默认值为50，可以通过系统属性`query.url`配置，如下
```
mvn myquery:query -Dquery.url=http://maven.apache.org
```

#### 不同类型的参数映射
1. 简单对象

```
...
    @Parameter(property = "myString")
    private String myString;
...
...
<configuration>
  <myString>a string</myString>
</configuration>
...
```

2. 复杂对象


```

...
    @Parameter(property = "person")
    private Person person;
...
...
<configuration>
  <person>
    <firstName>Jason</firstName>
    <lastName>van Zyl</lastName>
  </person>
</configuration>
...
```

3. List

```
...
    @Parameter(property = "animals")
    private List animals;
...

<project>
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-myanimal-plugin</artifactId>
        <version>1.0</version>
        <configuration>
          <animals>
            <animal>cat</animal>
            <animal>dog</animal>
            <animal>aardvark</animal>
          </animals>
        </configuration>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```

4. Map

```
...
    @Parameter(property = "myMap")
    private Map myMap;
...

...
  <configuration>
    <myMap>
      <key1>value1</key1>
      <key2>value2</key2>
    </myMap>
  </configuration>
...
```

5. Properties

```
...
    @Parameter(property = "myPro")
    private Properties myPro;
...
...
  <configuration>
    <myProperties>
      <property>
        <name>propertyName1</name>
        <value>propertyValue1</value>
      </property>
      <property>
        <name>propertyName2</name>
        <value>propertyValue2</value>
      </property>
    </myProperties>
  </configuration>
...
```

6. 数组

```
@Parameter
private String[] myArray;

<myArray>
  <param>value1</param>
  <param>value2</param>
</myArray>
```

### 一个完整的插件配置
```
/**
 * @goal query
 * @phase package
 */
public class MyBoundQueryMojo
    extends AbstractMojo
{
    @Parameter(property = "query.url", required = true)
    private String url;
 
    @Parameter(property = "timeout", required = false, defaultValue = "50")
    private int timeout;
 
    @Parameter(property = "options")
    private String[] options;
 
    public void execute()
        throws MojoExecutionException
    {
        ...
    }
}
...

<project>
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-myquery-plugin</artifactId>
        <version>1.0</version>
        <executions>
          <execution>
            <id>execution1</id>
            <phase>install</phase>
            <configuration>
              <url>http://www.bar.com/query</url>
              <timeout>15</timeout>
              <options>
                <option>four</option>
                <option>five</option>
                <option>six</option>
              </options>
            </configuration>
            <goals>
              <goal>query</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```

### 使用dependencies
插件也会依赖其他构件，通过`dependencies`可以控制依赖的版本，同时也可以排除不必要的依赖。
```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.2</version>
        ...
        <dependencies>
          <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.1</version>
          </dependency>
          <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant-launcher</artifactId>
            <version>1.7.1</version>
          </dependency>
         </dependencies>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```
### 使用inherited
默认情况下，插件配置会被继承，如果子pom不想继承父pom的插件配置，可以设置 `inherited = false`。
```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.2</version>
        <inherited>false</inherited>
        ...
      </plugin>
    </plugins>
  </build>
  ...
</project>
```
