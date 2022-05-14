## Maven 简介
### 序言
编程过程中常常涉及到很多与编程无关的构建工作，如依赖下载、编译源码、单元测试等操作，如果通过手动的方式调用这些构建过程，不仅耗时且容易出错，所以开发效率不高。于是，解决这个痛点的项目构建工具诞生了。通过使用项目构建工具，运行简单的命令即可完成项目构建过程，从而让开发者聚焦编程，大大提高了开发效率。

Maven就是项目构建工具实现之一，它是采用Java编程语言开发的，主要用来管理和构建Java项目的一个工具。

下面简单地过一下Maven的使用，让你有个大概的了解。

### 下载安装Maven
这里只介绍通过压缩包安装步骤
1. 到 [官网](https://maven.apache.org/) 下载压缩包
2. 本地解压到指定目录
3. 配置运行目录到环境变量

安装成功之后，在命令行运行以下命令，能够输出版本信息就说明成功了。
```mvn
mvn --version   
```

### 创建一个项目
首先我们的创建一个Maven可以识别的项目，即当前项目下必须要有一个`pom.xml`文件。简单起见，直接通过Maven提供的命令创建。
> 这里目的只是为了创建一个项目，可以先忽略命令行参数的作用
```mvn
mvn -B archetype:generate\
    -DgroupId=com.mycompany.app\
    -DartifactId=my-app\
    -DarchetypeArtifactId=maven-archetype-quickstart\
    -DarchetypeVersion=1.4
```
生成的项目结构如下
```
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── mycompany
    │   │           └── app
    │   │               └── App.java
    │   └── resources
    │       └── hombio.tx
    └── test
        └── java
            └── com
                └── mycompany
                    └── app
                        └── AppTest.java

```
### 编译项目
```mvn
mvn compile
```

### 运行单元测试
```
mvn test
```
### 打包项目
```
mvn package
```
### 打包部署到本地仓库
```
mvn install
```
### 打包部署到远程仓库
```
mvn deploy
```
### 清理项目构建输出
```
mvn clean
```

## 小结
如上所见，Maven已经提供了一系列开箱即用的命令，用于执行对应的构建任务，确实是简化了开发者的构建工作。

接下来，我会深入介绍一下Maven提供的一些常用特性，让你知其然，也知其所以然，从而更好地使用这个工具，而不是停留在简单的命令调用层次。