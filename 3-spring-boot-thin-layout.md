# spring boot maven 打包瘦身

1. 项目pom.xml 依赖添加

```xml

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <dependencies>
                <dependency>
                    <groupId>org.springframework.boot.experimental</groupId>
                    <artifactId>spring-boot-thin-layout</artifactId>
                    <version>1.0.28.RELEASE</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

2. 执行 `mvn clean package`
3. 启动 `java -Dthin.root=/your/public/lib/path -jar thin-app.jar` 并指定启动jar包时需要下载依赖的文件存放目录(第一次启动，下载依赖会需要很长时间)
4. 可以选择预热缓存，并非真正启动jar。`java -Dthin.root=/your/public/lib/path -Dthin.dryrun=true -jar thin-app.jar`

> 默认所有 jar 文件都缓存在本地 Maven 存储库中, 可以指定 Dthin.root 参数自定义jars依赖缓存目录. 这会将所有依赖项下载到 ${thin.root}/repository，并在 ${thin.root}/settings.xml 中查找 Maven 设置。



------
# 参考
- [spring-boot-thin-launcher](https://github.com/spring-projects-experimental/spring-boot-thin-launcher)