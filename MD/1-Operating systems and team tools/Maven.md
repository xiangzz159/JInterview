# Maven软件项目管理
- [Maven软件项目管理](#maven软件项目管理)
- [Maven声明周期和插件](#maven声明周期和插件)
  - [生命周期](#生命周期)
  - [插件](#插件)
    - [插件目标](#插件目标)
    - [插件绑定](#插件绑定)
    - [插件配置](#插件配置)
    - [插件仓库](#插件仓库)
- [使用Archtype生成项目骨架](#使用archtype生成项目骨架)
- [使用Maven进行测试](#使用maven进行测试)
- [Maven自定义Archtype](#maven自定义archtype)
- [使用Nexus搭建私服](#使用nexus搭建私服)
- [Maven插件编写](#maven插件编写)

# Maven声明周期和插件
项目构建的生命周期一般包括：项目清理、初始化、编译、测试、打包、集成测试、验证、部署、站点生成等阶段，但是maven为了更方便构建，maven使用了3套相互独立的生命周期来包含这些阶段，名称分别为：clean、default、site。clean生命周期的目的是清理项目，default生命周期目的是构建项目，site生命周期是建立项目站点。3套生命周期相互独立。每套生命周期中各个阶段都是由次序关系，后面阶段执行前必须先执行前面的阶段，就像人在死亡前他必须经历出生、少年、青年、壮年、老年这些阶段。

## 生命周期
1. clean生命周期：清理项目
   - pre-clean：执行清理前的工作
   - clean：清理上一次构建生成的文件
   - post-clean：执行清理后的工作
2. default生命周期：default生命周期是最核心的，它包含了构建项目时真正需要执行的所有步骤。
   - validate 
   - initialize
   - generate-source
   - process-source
   - generate-resource
   - process-resources：复制和处理资源文件到target目录，准备打包
   - complie：编译项目的源码
   - process-classes
   - generate-test-sources
   - process-test-sources
   - generate-test-resources
   - process-test-resources
   - test-complie：测试变异源码
   - process-test-classes
   - test：运行测试代码
   - prepare-package
   - package：打包成jar或者war或者其他格式的分发包
   - pre-integration-test
   - integration-test
   - post-integration-test
   - verify
   - install：将打好的包安装到本地仓库，供其他项目使用
   - deploy：将打好的包安装到远程仓库，供其他项目使用；
3. site生命周期：建立项目站点
   - pre-site
   - site：生成项目的站点文档
   - post-site
   - sit-deploy：发布生成的站点文档

## 插件
Maven的核心文件很小，主要的任务都是由插件来完成。定位到：%本地仓库%\org\apache\maven\plugins，可以看到一些下载好的插件

### 插件目标
- Maven的核心仅仅定义了抽象生命周期，具体工作由插件完成，对于插件本身，为了能够实现代码复用，它往往能完成多个任务，也就具有多个功能，这些功能聚集在一个插件中，每个功能就叫做一个插件目标。
- maven-dependency-plugin插件有十多个目标，每个目标对应一个功能，目标有：dependency:analyze、dependency:tree、dependency:list。这是一种通用写法，冒号前面是插件前缀，后面是插件目标
### 插件绑定
1. 内置绑定
   - Maven在核心的生命周期阶段绑定了很多插件的目标，当用户通过命令调用生命周期阶段的时候对应的插件目标就会执行相应的任务。
   - 对于默认的插件绑定在上面介绍3个生命周期时已经有备注了，下面对default生命周期的内置插件绑定关系做一些说明。default生命周期与插件的绑定关系由打包类型决定packaging，因为jar项目需要打包成JAR包，war项目需要打包成war包，还有pom、maven-plugin、ear等类型。  
   [default生命周期插件目标绑定周期](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Built-in_Lifecycle_Bindings)

2. 自定义绑定  
   除了内置绑定外，用户还可以选择将某个插件目标绑定到生命周期的某个阶段。

   实例：创建项目源码包
   - 确定插件  
     基本上所有的插件插件都来源于[Apache](http://maven.apache.org/plugins/index.html)和[Codehaus](http://mojo.codehaus.org/plugins.html)  
     首先通过查看插件发现maven-source-plugin插件的jar-no-fork目标能够将项目的主代码（包括主资源）打包成jar文件
   - 确定生命周期  
     查看生命周期各阶段介绍，发现绑定到default生命周期的verify阶段上，在执行完集成测试后和安装构件之前创建源码jar包。  
     ```pom.xml
        <build>
            <plugins>
                <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <version>2.1.1</version>
                        <executions>
                            <execution>
                            <id>my-attach-sources</id>
                            <phase>verify</phase>
                            <goals>
                                <goal>jar-no-fork</goal>
                            </goals>
                            </execution>
                        </executions>
                    </plugin>
            </plugins>
        </build>    
     ```
    > 解释
    - groupId为org.apache.maven.plugins（maven官方插件），凡是gourpId为org.apache.maven.plugins可以省略（不推荐）
    - executions下每个execution元素可以用来配置执行一个任务，该例中配置一个id为my-attach-sources的任务
    - 通过phase将其绑定到verify生命周期阶段上。不配置phase也会执行目标(不推荐)，因为该插件编写时默认阶段是package，而package在install之前。
    - 通过goals配置指定要执行的插件目标。
    - 执行mvn clean install，多了如下两行,生成了源码包iqasweb-sources.jar。
        ```
        [INFO] --- maven-source-plugin:2.1.1:jar-no-fork (my-attach-sources) @ iqasweb ---
        [INFO] Building jar: E:\Repository\iqasproject\iqasweb\target\iqasweb-sources.jar
        ```
    - 多个目标被绑定到同一目标上时，执行顺序安装声明先后执行。
### 插件配置
- 插件和生命周期绑定后，用户可以配置插件和插件目标的参数，进一步刁征插件目标所执行的任务，以满足项目需求，几乎所有的Maven插件都有一些参数。

- 用户可以通过命令行或者POM来配置插件参数，注意，命令行和POM配置的参数的名称一般都不一样。就需要查看插件的信息，下面有介绍。

- 配置之前需要查看插件有哪些参数可以配置，配置参数的意义。

1. 命令行插件配置
   - 很多插件支持命令行配置，但不是所有插件都支持
   - 在Maven命令中使用-D参数，并伴随一个key=value的形式来配置插件目标参数。  
  
    案例：
    maven-surefire-plugin提供了一个maven.test.skip的命令行参数(在pom中参数为skip)，当其值为true时会跳过执行测试。
    ```shell script
    $mvn install -Dmaven.test.skip=true
    ```
2. POM中插件全局配置
   - 对于参数很少改变的配置应该配置在pom中。
   - 对插件的配置有全局配置和对插件目标的配置。全局配置就是所有基于该插件目标的任务，都会使用这些配置。对插件目标配置就只是对该目标的任务执行该配置。

    案例：为插件的某个目标配置参数，配置在plugin/executions/execution下面的configuration
    ```
     <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <executions>
            <execution>
                <id>my-compile</id>
                <phase>compile</phase>
                <goals>
                   <goal>compile</goal>
                </goals>
                <!-- 配置maven-compiler-plugin插件编译源代码的JDK版本为1.7 -->
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
            </execution>
          </executions>
      </plugin>
    ```

### 插件仓库
- 与依赖构件一样，插件构件同样基于坐标存在于Maven仓库中，需要时从本地仓库寻找，不存在，从远程仓库下载到本地使用。
- 插件仓库使用pluginRepositories和pluginRepository配置，和依赖的中央仓库类似，Maven在超级POM中同样内置了插件远程仓库配置：  
   ```
   <pluginRepositories>
       <pluginRepository>
       <id>central</id>
       <name>Central Repository</name>
       <url>https://repo.maven.apache.org/maven2</url>
       <layout>default</layout>
       <snapshots>
           <enabled>false</enabled>
       </snapshots>
       <releases>
           <updatePolicy>never</updatePolicy>
       </releases>
       </pluginRepository>
   </pluginRepositories>
   ```
- 当然maven配置的中央插件仓库已经能满足我们需求，但是如果使用的插件找不到，也可以配置其它，在POM中或者settings.xml(在settings.xml配置在nexus私服中介绍)中加入其它插件仓库配置。

# 使用Archtype生成项目骨架

# 使用Maven进行测试

# Maven自定义Archtype

# 使用Nexus搭建私服

# Maven插件编写


