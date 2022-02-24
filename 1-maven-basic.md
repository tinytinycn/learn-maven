# maven 依赖

## 1. 依赖配置

依赖包的基本声明包括一下元素:

```xml
<project>
    <dependencies>
        <dependency>
            <groupId>...</groupId>
            <artifactId>...</artifactId>
            <version>...</version>
            <type>...</type>
            <scope>...</scope>
            <optional>...</optional>
            <exclusions>
                <exclusion>
                ...
                </exclusion>
                ...
            </exclusions>
        </dependency>
        ...
    </dependencies>
    ...
</project>
```

- **groupId、artifactId和version**：依赖的基本坐标
- **type**：依赖的类型，对应于项目坐标定义的`packaging`，一般情况下，该元素不用声明，默认为`jar`；
- **scope**：依赖的范围；
- **optional**：标记依赖是否可选；
- **exclusions**：用来排除传递性依赖。

## 2 依赖范围Scope

Maven依赖的依赖范围有以下几类，分别是：`compile`、`test`、`provided`、`runtime`、`system`和`import`。

之所以Maven会有这么多依赖范围，**主要原因**是：1. Maven在**编译**项目主代码的时候需要使用一套classpath；2. Maven在**编译和执行测试**的时候会使用另外一套classpath；Maven实际**运行**项目的时候，又会使用一套classpath。依赖范围就是用来控制依赖与这三种classpath的关系。

- **compile**：编译依赖范围（默认值），对编译、测试、运行三种classpath都有效；
- **test**：测试依赖范围，只对测试classpath有效；
- **provided**：已提供依赖范围，对编译和测试classpath有效；
- **runtime**：运行时依赖范围，对测试和运行classpath有效；
- **system**：系统依赖范围，以provided依赖范围一致，但使用`system`范围时必须通过`systemPath`元素显示地制定依赖文件的路径；
- **import**：导入依赖范围，该依赖不会对三种classpath产生实际影响。

| scope    | 对编译有效 | 对测试有效 | 对运行有效 |
| -------- | ---------- | ---------- | ---------- |
| compile  | Y          | Y          | Y          |
| test     | -          | Y          | -          |
| provided | Y          | Y          | -          |
| runtime  | -          | Y          | Y          |
| system   | Y          | Y          | -          |

使用`system`依赖范围的例子:

```xml
<dependency>
    <groupId>commons-collections</groupId>
    <artifactId>commons-collection</artifactId>
    <version>3.2.1</version>
    <type>system</type>
    <systemPath>${java.home}/lib/rt.jar</systemPath>
</dependency>
```

## 3 传递性依赖

**传递性依赖**：比如一个项目`account-email`有一个compile范围的`spring-core`依赖，而`spring-core`有一个compile范围的`commons-logging`的依赖，那么`commons-logging`就会成为`account-email`的compile范围依赖，`commons-logging`是`account-email`的一个传递性依赖。

看一下**传递性依赖**和**依赖范围**的关系，假设A依赖于B，B依赖于C，我们说A对于B是**第一直接依赖范围**，B对于C是**第二依赖范围**，A对于C是**传递性依赖**。**第一直接依赖的范围和第二直接依赖的范围决定了传递性依赖的范围**.

如下表所示，最左边一列表示第一直接依赖，最上面一行表示第二直接依赖范围，中间的结果就表示传递性依赖范围。

| 依赖范围 | compile  | test | provided | runtime  |
| -------- | -------- | ---- | -------- | -------- |
| compile  | compile  | ——   | ——       | runtime  |
| test     | test     | ——   | ——       | test     |
| provided | provided | ——   | provided | provided |
| runtime  | runtime  | ——   | ——       | runtime  |

得到以下结论：

1. 当第二直接依赖的范围是`compile`的时候，传递性依赖的范围与第一直接依赖的范围一致；
2. 当第二直接依赖的范围是`test`的时候，依赖不会得以传递；
3. 当第二直接依赖的范围是`provided`的时候，只传递第一直接依赖范围也为`provided`的依赖，且传递性依赖的范围同样为`provided`；
4. 当第二直接依赖的范围是`runtime`的时候，传递性依赖的范围与第一依赖的范围一致，但`compile`例外，此时传递性依赖的范围为`runtime`。

## 4 依赖调解

假如项目A有这样的依赖的关系：A->B->C->X(1.0)，A->D->X(2.0)，X是A的传递性依赖，但是有两条路径，那么应该选择哪个版本呢？

Maven依赖调解（Dependency Mediation）有两个原则：

- 路径最近者优先；
- 第一声明者优先（路径相等的前提下，在POM中依赖声明的顺序决定了谁会被解析使用）；

## 5 可选依赖

假设有这样一个依赖关系，项目A依赖与项目B，项目B依赖于项目X和Y，B对于X和Y的依赖都是可选依赖：A->B、B->X（可选）、B->Y（可选），那么X、Y就是A的传递性依赖。然而，由于这里X、Y是可选依赖，**依赖将不会得以传递**。

```xml
<dependency>  
  <groupId>commons-logging</groupId>   
  <artifactId>commons-logging</artifactId>   
  <version>1.1.1</version>   
  <optional>true<optional>  
</dependency>
```

一般情况下，不应该使用可选依赖，使用可选依赖的原因是某一个项目实现了多个特性，在面向对象设计中，有个单一职责性原则，意指一个类应该只有一项职责，而不应该糅合太多的功能。**当项目需要某个依赖的可选依赖，可以显式地声明这一依赖**。

## 6 排除依赖

```xml
 <dependency>
    <groupId>com.juvenxu.mvnbook</groupId>
    <artifactId>project-b</artifactId>
    <version>1.0.0</version>
    <exclusions>
        <exclusion>
            <!-- 排除对project-c的依赖 -->
            <groupId>com.juvenxu.mvnbook</groupId>
            <artifactId>project-c</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

上述代码中，项目A依赖于项目B，但是由于一些原因，不想引入传递性依赖C，而是自己显示声明对于项目C1.0.0版本的依赖。代码中用exclusions元素可以包含**多个**exclusion子元素。需要注意的是声明exslusion的时候只需要groupId和artifactId，而**不需要**version元素(Maven解析后的依赖中，不可能出现groupId和artifactId相同，但是version不同的两个依赖)。

## 7 归类依赖

当我们使用`Spring Framework`的依赖时，会有许多依赖，如：`spring-core:2.5.6.org`、`org.springframework:spring-beans:2.5.6`等，它们是来自同一项目下的不同模块。它们依赖的版本都是相同时，当需要升级`Spring Framework`时，这些依赖的版本都会一起升级。这时候我们可以用Maven属性的方式来定义一个名为`springframework.version`的属性，让所有的`spring framework`子模块都引用它。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.juvenxu.mvnbook</groupId>
    <artifactId>project-a</artifactId>
    <version>1.0.0</version>

    <properties>
        <!--类似于设置一个变量-->
        <springframework.version>3.1.2.RELEASE</springframework.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${springframework.version}</version>
        </dependency>
    </dependencies>
</project>
```

当我们需要升级的时候,就只需要改属性值`springframework.version`就可以了

## 8 优化依赖

- 使用`mvn dependency:list`命令可以查看项目已解析的依赖;
- 使用`mvn dependency:tree`命令可以查看项目构成的依赖树;
- 使用`mvn dependency:analyze`命令可以分析当前项目的依赖.


