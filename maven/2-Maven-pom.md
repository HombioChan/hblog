## Maven POM
### 序言
一个项目如果要被Maven管理，那么在根目录下必须要有一个`pom.xml`文件。

POM，全称Project Object Model，存在形式是一个xml格式的文件，里面包含了Maven执行构建所需的所有信息。

### POM中的元素 
POM中的元素比较多，遇到不熟悉的元素，你可以到 [这里](https://maven.apache.org/pom.html) 查看文档，获取需要的信息。

### 最小份POM
最小份POM文件只需要包括
- 模型版本
- 项目的坐标信息
```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
</project>
```

## POM之间的关系
### 继承
默认情况下，所有的项目pom文件都会继承一个基础pom文件，这个pom文件称之为  [`Super POM`](https://maven.apache.org/ref/3.6.3/maven-model-builder/super-pom.html) 。

下面给出两个显示指定继承的例子。
#### 1）例子1
```
.parent
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```
my-module如果想继承parent的pom，如下
```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```
#### 2）例子2
```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
```
my-module如果想继承parent的pom，这是需要使用到`relativePath`
```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
 
  <artifactId>my-module</artifactId>
</project>
```

### 聚合
聚合可以实现多模块构建，要想使用聚合需要执行以下2个步骤
1. 修改父POM的pacakaging=pom
2. 在父pom种指定modules

比如，项目模块如下
```
.parent
 |-- my-module
 |   `-- pom.xml
 `-- pom.xml
```
parent如果想聚合mly-module，那么配置如下
```
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-module</module>
  </modules>
</project>
```
同理继承，指定模块时可以使用相对路径
```
.
 |-- my-module
 |   `-- pom.xml
 `-- parent
     `-- pom.xml
     
<project>
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>../my-module</module>
  </modules>
</project>
```

### 继承与聚合，如何抉择
如果多个Maven项目存在相同的配置，那么可以通过继承重构这些项目，复用相同的配置。

如果希望多个项目一起构建，那么就可以使用聚合。

继承与聚合不是非此即彼的关系，可以同时使用，具体项目，具体分析，灵活组合。