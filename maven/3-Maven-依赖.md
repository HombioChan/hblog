## Maven 依赖

### 序言
依赖机制是Maven的核心特性，开发者只需在`pom.xml`中声明好依赖的坐标信息，在执行构建时，Maven就会自动下载这些依赖，包括依赖的依赖（即传递依赖）。

但是，依赖自动下载存在三个风险
1. 循环依赖
2. 依赖版本冲突
3. 依赖过多  

本节介绍一下依赖的使用以及Maven解决依赖自动下载带来的三个风险的几个机制。

### 使用依赖
使用依赖比较简单，只需在项目的`pom.xml`文件中的`dependencies`下声明依赖坐标即可。比如，项目使用`junit`依赖，如下配置
```
<project>
...
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
...
```
### 依赖协调
顾名思义，项目依赖链中，存在两个版本不同的依赖，发生了版本冲突，这时需要协调使用哪个版本的依赖。
#### 深度不同，短路径优先
D 1.0 会被使用
```
  A
  ├── B
  │   └── C
  │       └── D 2.0
  └── E
      └── D 1.0
```
#### 深度相同，先定义优先
D 2.0 会被使用
```
  A
  ├── B
  │   └── D 2.0
  └── E
      └── D 1.0
```

### 依赖范围
首先对classpath（简称cp）做一下分类
- 编译cp：源代码开发所处的环境
- 测试cp：开发单元测试所处的环境
- 运行cp：程序编译打包之后运行的环境

依赖范围的作用有两个
1. 用于控制依赖的传递性
2. 控制一个依赖有效的cp环境

Maven支持6个依赖范围：compile、provided、runtime、test、system、import。（其中system已经废弃）


#### compile
如果依赖没有指定scope，默认是compile。
- 有效的cp环境：所有

#### provided
- 有效的cp环境：编译cp、测试cp
- 比如：Servlet API、lombok

#### runtime
- 有效的cp环境：测试cp、运行cp

#### test
- 有效的cp环境：测试cp
- 比如：junit

#### import
只在`<dependencyManagement>`中类型为`<packaging>pom</packaging>`的依赖声明中使用，用来做依赖库的版本管理控制，并不引入传递依赖。 以常用的Spring Cloud为例。
```
...
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
       </dependencies>
    </dependencyManagement>
...
```
#### 对传递依赖引入的影响
除了import，其他的范围对于传递依赖自动引入的行为都有不同的影响。

| 范围 | compile | provided | runtime | test |
| --- | --- | --- | --- | --- |
| compile | compile | - | runtime | - |
| provided | provided | - | provided | - |
| runtime | runtime | - | runtime | - |
| test | test | - | test | - |


> 说明：比如当前项目引入了scope=compile的依赖A，依赖A又引入scope=runtime的依赖B，那么依赖B会被自动引入，scope=runtime。其他关系类比。

### 依赖管理
集中统一管理依赖版本，多用于继承场景下。父pom依赖版本管理，子pom中的依赖就不再需要在坐标中声明版本信息了。
```
<project>
  ...
  <dependencyManagement>
    <dependencies>
      ...
      <dependency>
        <groupId>group-a</groupId>
        <artifactId>artifact-a</artifactId>
        <version>1.0</version> 
      </dependency>
      ...
    </dependencies>
  </dependencyManagement>
  ...
</project>
```

### 依赖排除
显示排除传递依赖，如下例子
```
A -> B -\-> C
```
```
<project>
  ...
    <dependencies>
      <dependency>
        <groupId>group-b</groupId>
        <artifactId>B</artifactId>
        <version>1.0</version>
 
        <exclusions>
          <exclusion>
            <groupId>group-c</groupId>
            <artifactId>C</artifactId>
          </exclusion>
        </exclusions>
        
      </dependency>
    </dependencies>
  ...
</project>
```

### 依赖可选
声明当前依赖可选，自动引入传递依赖时会被忽略。如下，项目A只会引入B依赖。
```
A -> B -> C(optional)
```

```
<project>
  ...
    <dependencies>
      <dependency>
        <groupId>group-c</groupId>
        <artifactId>C</artifactId>
        <version>1.0</version>
        <optional>true</optional>
      </dependency>
    </dependencies>
  ...
</project>
```

### 小结
综上，依赖协调和依赖版本管理可以用来解决依赖冲突的问题，前者是Maven内置的默认规则，后者是开发者手动定义的规则；依赖范围、依赖排除和依赖可选，都可以减少项目依赖的数目，避免依赖过多。依赖循环问题比较复杂，当Maven检测到循环依赖时，是会将错误传递给开发者的，这时需要开发者手动排除，通过依赖排除机制将循环断开，从而解决问题。