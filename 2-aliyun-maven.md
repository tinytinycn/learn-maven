# 使用阿里云maven

- 使用自定义setting.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <mirrors>
      <mirror>
        <id>aliyun-maven-central</id>
        <!-- 从central,jcenter仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>central</mirrorOf>
        <name>aliyun central jcenter聚合仓库</name>
        <url>https://maven.aliyun.com/repository/public</url>
      </mirror>
      <mirror>
        <id>aliyun-maven-google</id>
        <!-- 从google仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>google</mirrorOf>
        <name>aliyun google仓库</name>
        <url>https://maven.aliyun.com/repository/google</url>
      </mirror>
      <mirror>
        <id>aliyun-maven-spring</id>
        <!-- 从spring仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>spring</mirrorOf>
        <name>aliyun spring仓库</name>
        <url>https://maven.aliyun.com/repository/spring</url>
      </mirror>
      <mirror>
        <id>aliyun-maven-spring-plugin</id>
        <!-- 从spring-plugin仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>spring-plugin</mirrorOf>
        <name>aliyun spring-plugin仓库</name>
        <url>https://maven.aliyun.com/repository/spring-plugin</url>
      </mirror>
      <mirror>
        <id>aliyun-maven-apache-snapshots</id>
        <!-- 从apache-snapshots仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>apache snapshots</mirrorOf>
        <name>aliyun apache snapshots仓库</name>
        <url>https://maven.aliyun.com/repository/apache-snapshots</url>
      </mirror>
      <mirror>
        <id>aliyun-maven-apache-snapshots</id>
        <!-- 从gradle-plugin仓库下载依赖包 统一由阿里云url下载 -->
        <mirrorOf>gradle-plugin</mirrorOf>
        <name>aliyun gradle-plugin仓库</name>
        <url>https://maven.aliyun.com/repository/gradle-plugin</url>
      </mirror>
    </mirrors>
  </settings>
  ```

------

# 参考
- [阿里云Maven镜像](https://developer.aliyun.com/mirror/maven)
- [阿里云云效Maven](https://developer.aliyun.com/mvn/guide)
  
  