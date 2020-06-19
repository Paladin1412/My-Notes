# Maven


---

## 第三方文件

### Libraries 库

在 Java 项目开发中，我们需要大量导入其他开发者已经完成的 Java 文件供自己使用。

为便于开发，我们把所有导入的第三方文件放入项目的 Libraries 库中， Java 程序通过 `import` 语句可以直接调用。

*指定 JDK 版本和文件储存位置后，JDK 文件也保存在 Libraries 库中。*

### JAR 文件

(Java Archive File) Java 的一种文档格式。实际是 Java 文件压缩后的 ZIP 文件，又名文件包。

Libraries 库里的第三方文件都是以 jar 文件形式保存，Java 程序导入时会自动解析其中的 Java 代码并使用。

---

## Maven

### Maven 功能

项目管理工具，为项目自动加载需要的 Jar 包。

用户只需要在配置文件中注明所需要的第三方文件和路径，Maven 会自动将 JAR 文件导入到项目的 Libraries 库中。

### Maven 配置

在 IDEA 的 Settings/Maven 中，可以对 Maven 进行配置：

1. **Maven 安装位置**：默认为 IDEA 自带，可配置为本地安装。
2. **XML 配置文件位置**
3. **Libraries 库位置**

![mavenidea](mavenidea.PNG)

### XML 文件

Maven 采用 XML 配置文件来注明项目所需要的第三方文件和路径。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    
    <!--
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>
    -->

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.9.RELEASE</version>
        <relativePath/> 
    </parent>
    

    <!-- 相关配置 -->
    <properties>
        <java.version>1.8</java.version>
    </properties>

    <!-- 导入 JAR 包 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web-services</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.1</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.17</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/libs-snapshot</url>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/libs-snapshot</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

