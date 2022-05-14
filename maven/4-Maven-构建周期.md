## 什么是构建生命周期
构建是一个复杂的过程，可以拆分为多个阶段，每个阶段执行特定的任务，比如`创建->验证->编译->测试->打包`，如果每个项目都周而复始地、顺序地经历这些阶段，这个循环周期就称之为项目的构建生命周期。

## Maven的构建生命周期
Maven定义了三个生命周期，其中`default`为使用最多的生命周期。
- clean
- default
- site

每个构建周期都被定义为一组阶段，每个阶段执行特定的任务，比如`default`构建周期包含了以下阶段（只是部分），其中`compile`负责编译项目源代码。
- validate
- compile
- test
- package
- verify
- install
- deploy

注意，执行某个阶段时，先前的阶段也将顺序执行。

比如，运行命令`maven package`，实际运行的阶段为：
```
validate -> compile -> test -> package
```

## 使用构建生命周期
```
mvn [phase]

phase=compile|test|package|...
```
### 构建阶段如何执行任务

阶段本身只是个概念，没有任何执行逻辑，实际的任务执行是由插件完成。

以构建阶段`compile`为例，默认挂载了插件`compiler`的`compile`任务，所以每次运行命令`mvn compile`时，实际执行的是插件`compiler`的`compile`任务。

换句话说
```
mvn compile
```
等效于
```
mvn compiler:compile
```
从这点来看，阶段的意义就是缩短了命令行的长度。

### 构建阶段与插件任务的关系
1）一个阶段可以挂载多个插件任务，任务按挂载先后会被顺序执行  
2）同一个插件任务可以被挂载到多个构建阶段，且都会被执行一次  
3）一个阶段也可以不挂载插件任务，这种构建阶段不会被执行

### 如何挂载插件任务到构建阶段
默认情况下，Maven为每个阶段都挂载了一个默认的插件任务，比如
```
compile <-- compiler:compile
```

开发者也可以通过配置在构建阶段绑定插件任务，比如下面这个例子，将插件任务`display-maven-plugin:time`挂载到了构建阶段`compile`
```
...
 <plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>compile</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...

```
之后运行`mvn compile`，执行完默认的`compiler:compile`任务后，就会执行`display-maven-plugin:time`任务。

## 小结
构建生命周期是Maven的核心概念，Maven定义了三个什么周期：clean、default、site，其中`default`最为常用；每个生命周期包含了一组阶段（phase），阶段可以挂载多个插件任务，日常开发使用到构建阶段命令，实际上任务执行是由插件实现的；Maven为每个阶段挂载一个默认的插件任务，开发者通过配置可以新增插件任务挂载到构建阶段。
