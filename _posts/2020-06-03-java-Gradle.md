---
layout: post
title: Gradle-相关使用指南
categories: [Gradle]
description: Gradle目前是新兴的项目管理工具，当然目前Maven还是主流。
keywords: wenhao  , 文浩  , 似水似流年  , wenhaoclub  , fuwenhao.club , 文浩的博客 , Gradle , java , idea , fwh666 
---

## 序：
> 项目管理工具-Gradle篇

## 教程：
### 安装使用：
#### 手动安装：
- 由于本机是macOS系统。采用brew安装： 建议科学上网后再执行 `brew install gradle`
- 其他系统可以下载zip包安装。
    - 地址：https://gradle.org/gradle-download/
    - 手动解压的需要配置环境变量后才能使用
- 由于包更新频率高，可以考虑使用包管理器来安装
- 命令： gradle -v  检查是否安装成功
- 结果如下：

```
localhost:~ fwh$ gradle -v

Welcome to Gradle 6.5!

Here are the highlights of this release:
 - Experimental file-system watching
 - Improved version ordering
 - New samples

For more details see https://docs.gradle.org/6.5/release-notes.html


------------------------------------------------------------
Gradle 6.5
------------------------------------------------------------

Build time:   2020-06-02 20:46:21 UTC
Revision:     a27f41e4ae5e8a41ab9b19f8dd6d86d7b384dad4

Kotlin:       1.3.72
Groovy:       2.5.11
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          13.0.2 (Oracle Corporation 13.0.2+8)
OS:           Mac OS X 10.14.6 x86_64
```

### 编写测试程序：
- 创建build.gradle文件。
    - build.gradle是Gradle默认的构建脚本文件，执行Gradle命令的时候，会默认加载当前目录下的build.gradle脚本文件，当然你也可以通过 -b 参数指定想要加载执行的文件。
- 在文件内添加代码：

	```
	task helloworld{
	    doLast{
	        println'Hello World!'
	    }
	}
	#后者等同于下面的代码,
	task helloworld2 <<{
	    println "Hello World!"
	}
	```
- 运行：gradle helloworld
- 输出结果：
   
   ```
       > Task :helloworld
    Hello World!
    
    BUILD SUCCESSFUL in 598ms
    1 actionable task: 1 executed
   ```

### Gradle Wrapper
Wrapper，顾名思义，其实就是对Gradle的一层包装，便于在团队开发过程中统一Gradle构建的版本，然后提交到git上，然后别人可以下载下来，这样大家都可以使用统一的Gradle版本进行构建，避免因为Gradle版本不统一带来的不必要的问题。（所以要明白这个东西可以没有，有了只是为了统一管理，更加方便）

生成目录如下：

```
	├── gradle
	│   └── wrapper
	│       ├── gradle-wrapper.jar
	│       └── gradle-wrapper.properties
	├── gradlew
	└── gradlew.bat
```

详细的看下gradle-wrapper.properties内容

```
localhost:wrapper fwh$ cat gradle-wrapper.properties 
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists

具体含义如下：
distributionBase 下载的gradle压缩包解压后存储的主目录
distributionPath 相对于distributionBase的解压后的gradle压缩包的路径
zipStoreBase 同distributionBase，只不过是存放zip压缩包的
zipStorePath 同distributionPath，只不过是存放zip压缩包的
distributionUrl gradle发行版压缩包的下载地址，也就是你现在这个项目将要依赖的gradle的版本。
```


### IDE如何使用：
- 使用IDE新建项目的时候，可以直接选择Gradle选项
- IDEA默认就会使用gradle wrapper来创建项目，所以无需安装gradle也可以正常运行。
- 生成目录结构和Maven的项目结构几乎完全一致。gradle文件夹和gradlew那几个文件就是gradle wrapper的文件，
- 而.gradle后缀名的文件正是gradle的配置文件，对应于Maven的pom.xml。

#### **依赖管理：**
- 这点也是gradle相较maven的优势之一了。相较于maven一大串的XML配置，gradle的依赖项仅需一行。
  
    ```
    dependencies {
        testImplementation 'junit:junit:4.13'
        implementation 'com.google.code.gson:gson:2.8.6'
    }
    ```

#### 依赖控制粒度：
- gradle依赖的粒度控制相较于Maven也更加精细，maven只有compile、provided、test、runtime四种scope，而gradle有以下几种scope：
- 1.implementation，默认的scope。implementation的作用域会让依赖在编译和运行时均包含在内，但是不会暴露在类库使用者的编译时。举例，如果我们的类库包含了gson，那么其他人使用我们的类库时，编译时不会出现gson的依赖。
- 2.api，和implementation类似，都是编译和运行时都可见的依赖。但是api允许我们将自己类库的依赖暴露给我们类库的使用者。
- 3.compileOnly和runtimeOnly，这两种顾名思义，一种只在编译时可见，一种只在运行时可见。而runtimeOnly和Maven的provided比较接近。
- 4.testImplementation，这种依赖在测试编译时和运行时可见，类似于Maven的test作用域。
- 5.testCompileOnly和testRuntimeOnly，这两种类似于compileOnly和runtimeOnly，但是作用于测试编译时和运行时。
通过简短精悍的依赖配置和多种多样的作用与选择，Gradle可以为我们提供比Maven更加优秀的依赖管理功能。

#### gradle的任务和插件：
- gradle的配置文件是一个groovy脚本文件，在其中我们可以以编程方式自定义一些构建任务。因为使用了编程方式，所以这带给了我们极大的灵活性和便捷性。打个比方，现在有个需求，要在打包出jar的时候顺便看看jar文件的大小。在gradle中仅需在构建脚本中编写几行代码即可。而在Maven中则需要编写Maven插件，复杂程度完全不在一个水平。
- 当然，Maven发展到现在，已经存在了大量的插件，提供了各式各样的功能可以使用。但是在灵活性方面还是无法和Gradle相比。而且Gradle也有插件功能，现在发展也十分迅猛，存在了大量非常好用的插件，例如gretty插件。gretty原来是社区插件，后来被官方吸收为官方插件，可以在Tomcat和jetty服务器上运行web项目，比Maven的相关插件功能都强大。
- 虽然gradle可以非常灵活的编写自定义脚本任务，但是其实一般情况下我们不需要编写构建脚本，利用现有的插件和任务即可完成相关功能。在IDEA里，也可以轻松的查看当前gradle项目中有多少任务，基本任务如build、test等Maven和Gradle都是相通的

#### 配置镜像：
- Maven官方仓库的下载速度非常慢，所以一般我们要配置国内的镜像源。gradle在这方面和Maven完全兼容，因此只需稍微配置一下镜像源，即可使用Maven的镜像。如果你用gradle构建过项目，应该就可以在用户目录的.gradle文件夹下看到gradle的相关配置和缓存。
- 之前wrapper下载的gradle也存放在该文件夹下，位置是wrapper/dists。
- 而依赖的本地缓存在caches\modules-2\files-2.1文件夹下。目录结构和Maven的本地缓存类似，都是包名+版本号的方式，但是gradle的目录结构最后一层和Maven不同，这导致它们无法共用本地缓存。
- 言归正传，在gradle中配置下载镜像需要在.gradle文件夹中直接新建一个init.gradle初始化脚本，脚本文件内容如下。这样一来，gradle下载镜像的时候就会使用这里配置的镜像源下载，速度会快很多。再加上gradle wrapper在中国设置了CDN，现在使用gradle的速度应该会很快。
    ```
    allprojects {
       repositories {
           maven {
               url "https://maven.aliyun.com/repository/public"
           }
           maven {
               url "https://maven.aliyun.com/repository/jcenter"
           }
           maven {
               url "https://maven.aliyun.com/repository/spring"
           }
           maven {
               url "https://maven.aliyun.com/repository/spring-plugin"
           }
           maven {
               url "https://maven.aliyun.com/repository/gradle-plugin"
           }
           maven {
               url "https://maven.aliyun.com/repository/google"
           }
           maven {
               url "https://maven.aliyun.com/repository/grails-core"
           }
           maven {
               url "https://maven.aliyun.com/repository/apache-snapshots"
           }
       }
    }
    ```
- 当然，如果你有代理的话，其实我推荐你直接为gradle设置全局代理。因为gradle脚本实在是太灵活了，有些脚本中可能依赖了github或者其他地方的远程脚本。这时候上面设置的下载镜像源就不管用了。
- 所以有条件还是干脆直接使用全局代理比较好。设置方式很简单，在.gradle文件夹中新建gradle.properties文件，内容如下。中间几行即是设置代理的配置项。当然其他几行我也建议你设置一下，把gradle运行时的文件编码设置为UTF8，增加跨平台兼容性。

     ```
     org.gradle.jvmargs=-Xmx4g -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
    systemProp.http.proxyHost=127.0.0.1
    systemProp.http.proxyPort=10800
    systemProp.https.proxyHost=127.0.0.1
    systemProp.https.proxyPort=10800
    systemProp.file.encoding=UTF-8
    org.gradle.warning.mode=all
     ```
#### IDE中build.gradle配置

```
// Gradle 构建本身需要的配置（例如插件仓库）
buildscript {
    // 配置变量
    ext {
        flywayVersion = '3.2.1'
        hibernateVersion = '5.3.7.Final'
        springBootVersion = '2.1.4.RELEASE'
    }
    // 插件仓库
    repositories {
        gradlePluginPortal()
    }
    // 应用插件需要的依赖包
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}" as Object)
    }
}

// idea 插件，用来生成 idea 工程目录
apply plugin: 'idea'
// java 插件,提供 build、jar 等task
apply plugin: 'java'
// spring boot 插件提供 bootrun 等task，非必须
apply plugin: 'org.springframework.boot'
// dependency-management 插件，为spring 版本火车项目提供一致的版本号
apply plugin: 'io.spring.dependency-management'

// 指定构建输出目录
//buildDir = './out'

// 指定包信息
group = 'cn.printf'
version = '1.0.0'

// java 版本
sourceCompatibility = 1.8
targetCompatibility = 1.8

// 编译字符集
tasks.withType(JavaCompile) { options.encoding = 'utf-8' }

// 包依赖仓库
repositories {
    mavenLocal()
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    maven { url 'http://maven.aliyun.com/nexus/content/repositories/jcenter' }
    mavenCentral()
}

// 依赖
dependencies {
    // 监控
    compile 'org.springframework.boot:spring-boot-starter-actuator'

    compileOnly "org.projectlombok:lombok"
    testCompileOnly "org.projectlombok:lombok"

    // web
    compile 'org.springframework.boot:spring-boot-starter-web'

    // Session
    compile 'org.springframework.boot:spring-boot-starter-data-redis'
    compile 'org.springframework.session:spring-session-data-redis'

    // database
    compile 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'mysql:mysql-connector-java'

    // 数据库迁移
    compile 'org.flywaydb:flyway-core'

    // 对象转换
    compile group: 'org.modelmapper', name: 'modelmapper', version: '1.1.1'

    // 加密库
    compile 'commons-codec:commons-codec:1.13'

    // 测试
    testCompile("org.springframework.boot:spring-boot-starter-test")

    // 开发热启动工具
    runtime('org.springframework.boot:spring-boot-devtools')
}
```


## 相关问题：
- Maven的几个痛点：
    - 1. Maven的配置文件是XML格式的，假如你的项目依赖的包比较多，那么XML文件就会变得非常非常长；
    - 2. XML文件不太灵活，假如你需要在构建过程中添加一些自定义逻辑，搞起来非常麻烦；
    - 3. Maven非常的稳定，但是相对的就是对新版java支持不足，哪怕就是为了编译java11，也需要更新内置的Maven插件。   

- Gradle和maven的区别？以及如何选择？
    - Maven 和 Gradle 都是优秀的项目自动构建工具。
    - Maven 是行业标准，而 Gradle 是后起之秀。后者抛弃了前者基于 XML 的配置原则，取而代之的是 Groovy 这种风格的简洁配置
    - 例如：
    - Maven形式：
    
    ```
    <properties>
        <kaptcha.version>2.3</kaptcha.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>com.google.code.kaptcha</groupId>
            <artifactId>kaptcha</artifactId>
            <version>${kaptcha.version}</version>
            <classifier>jdk15</classifier>
        </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    </dependencies>
    ```
    Gradle方式：
    
    ```
    dependencies {
    compile('org.springframework:spring-core:2.5.6')
    compile('org.springframework:spring-beans:2.5.6')
    compile('org.springframework:spring-context:2.5.6')
    compile('com.google.code.kaptcha:kaptcha:2.3:jdk15')
    testCompile('junit:junit:4.7')
    }
    ```
- 为什么使用gradle？
    1. 速度，gradle使用构建缓存、守护进程等方式提高编译速度。结果就是gradle的编译速度要远超maven，平均编译速度比Maven快好几倍，而且项目越大，这个差距就越明显。
    2. 灵活性，gradle要比Maven灵活太多，虽然有时候灵活并不是一件好事情。但是大部分情况下，灵活一点可以极大的方便我们。Maven死板的XML文件方式做起事情来非常麻烦。很多Maven项目都通过执行外部脚本的方式来完成一些需要灵活性的工作。而在gradle中配置文件就是构建脚本，构建脚本就是编程语言（groovy编程语言），完全可以自给自足，无需外部脚本。
    3. 简洁性，完成同样的功能，gradle脚本的长度要远远短于maven配置文件的长度。虽然很多人都说XML维护起来不麻烦，但是我觉得，维护一个光是依赖就有几百行的XML文件，不见得就比gradle脚本简单。
    4. 也许是因为我上面说的原因，也许有其他原因，不得不承认的一件事情就是gradle作为一个新兴的工具已经有了广泛的应用。spring等项目已经从Maven切换到了gradle。开发安卓程序也只支持gradle了。因此不管是否现在需要将项目从maven切换到gradle，但是至少学习gradle是一件必要的事情。

- **为什么无法加载依赖的注解？**
	- 需要删除.build和.gradle文件夹后，重新加载就可以了。
    - 记得配置全局依赖文件init.gradle
    - 相关网址：https://blog.csdn.net/qq_38154018/article/details/106261397

### 琐碎知识点：
- gradle构建命令：
    - 清理命令 gradle clean
    - 构建打包命令 gradle clean build
    - 编译时跳过测试，使用-x,-x参数用来排除不需要执行的任务
        - gradle clean build -x test
- 创建缓存依赖：
    - 执行命令gradle clean build --refresh-dependencies或删除.gradle/caches目录，构建的时候它会下载所有依赖并加入到缓存中。
- maven项目转换为gradle项目
    - gradle init --type pom
    - 上面的命令会根据pom文件自动生成gradle项目所需的文件和配置，然后以gradle项目重新导入即可。
- settings.gradle配置:
    - 是模块Module配置文件，大多数setting.gradle的作用是为了配置子工程，根目录下的settings.gradle脚本文件是针对module的全局配置，它的作用域所包含的所有module是通过settings.gradle来配置。
settings.gradle用于创建多Project的Gradle项目。Project在IDEA里对应Module模块。
例如配置module名rootProject.name = 'DyoonPLM'

## 总结：

- [使用阿里云国内镜像](https://blog.csdn.net/lj402159806/article/details/78422953) 
- [极客学院相关链接](https://wiki.jikexueyuan.com/project/gradle/java-quickstart.html)
- [掘金网文章](https://juejin.im/post/5e924273f265da47f079379c)