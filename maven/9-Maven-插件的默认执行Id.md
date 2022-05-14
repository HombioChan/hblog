## 插件配置类型
配置插件任务有两种选择
### 1. 插件级别
```
...
  <build>
    <plugins>
      <plugin>
        <configuration>
          ...
        </configuration>
      <plugin>
    </plugins>
  </build>
...
```
插件级别的配置属于全局配置，被插件的所有任务所共享。
### 2. 运行级别
```
...
  <build>
    <plugins>
      <plugin>
        ...
        <executions>
          <execution>
            <id>...</id>
            <goal>...</goal>
            <configuration>
              ...
            </configuration>
          </execution>
        </executions>
      <plugin>
    </plugins>
  </build>
...
```
运行级别的配置属于局部配置，只在指定的任务（goal）下有效。

## 局限性
以上两个配置可以满足大部分的插件任务配置需求，但是存在以下三个局限性
1. 如果插件任务挂载到默认生命周期的某个阶段，并声明了配置，从CLI直接运行改插件任务，无法再指定配置，只能使用已有的配置。
2. 默认生命周期的每个阶段，都挂载了默认的插件任务，无法修改运行级别配置。
3. 默认生命周期的每个阶段，都挂载了默认的插件任务，无法再添加一个使用不同运行级别配置的默认插件任务。

## 解决方案
Maven提供的解决方案很简单，没用任何技术难度
- 每个从CLI直接运行的插件任务，分配一个一个默认执行Id：`default-cli`
- 默认生命周期的每个阶段挂载的默认插件任务，分配一个默认执行Id：`default-<phase>`，比如`default-compile`

## 实例分析
### 为CLI插件任务指定插件配置
当运行`mvn package`时，使用的是前者的配置，当运行`mvn assembly:single`时，使用的是后者的配置。其中配置`tarLongFileMode`是共享的。
```
<plugin>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <tarLongFileMode>gnu</tarLongFileMode>    
  </configuration>
  <executions>
    <execution>
      <id>build-distros</id>
      <phase>package</phase>
      <goals>
        <goal>single</goal>
      </goals>
      <configuration>
        <descriptors>
          <descriptor>src/main/assembly/bin.xml</descriptor>
          <descriptor>src/main/assembly/src.xml</descriptor>
        </descriptors>
      </configuration>
    </execution>
    <execution>
      <id>default-cli</id>
      <configuration>
        <descriptorRefs>
          <descriptorRef>jar-with-dependencies</descriptorRef>
          <descriptorRef>project</descriptorRef>
        </descriptorRefs>
      </configuration>
    </execution>
  </executions>
</plugin>
```
### 单独配置`compiler`
当运行`mvn compile`时，使用以下的配置，不再是默认的配置。
```
<plugin>
  <artifactId>maven-compiler-plugin</artifactId>
  <executions>
    <execution>
      <id>default-compile</id>
      <configuration>
        <source>1.3</source>
        <target>1.3</target>
      </configuration>
    </execution>
  </executions>
</plugin>
```

### 配置`compile`执行两次
当运行`mvn compile`时，第一次运行`compile`任务，使用上面的配置；第二次执行`compile`则使用下面的配置。
```
<plugin>
  <artifactId>maven-compiler-plugin</artifactId>
  <configuration>
    <source>1.5</source>
    <target>1.5</target>
  </configuration>
  <executions>
    <execution>
      <id>default-compile</id>
      <configuration>
        <excludes>
          <exclude>**/cli/*</exclude>
        </excludes>
      </configuration>
    </execution>
    <execution>
      <id>build-java14-cli</id>
      <phase>compile</phase>
      <goals>
        <goal>compile</goal>
      </goals>
      <configuration>
        <source>1.3</source>
        <target>1.3</target>
        <includes>
          <include>**/cli/*</include>
        </includes>
      </configuration>
    </execution>
  </executions>
</plugin>
```