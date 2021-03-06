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
  - [使用Archtype生成项目](#使用archtype生成项目)
    - [常用的archetype](#常用的archetype)
  - [根据项目生成archetype的命令](#根据项目生成archetype的命令)
  - [使用生成的archetype项目创建新项目](#使用生成的archetype项目创建新项目)
    - [方法1:利用自动生成的archetype-catalog.xml来创建项目](#方法1利用自动生成的archetype-catalogxml来创建项目)
    - [方法2:利用项目生成命令](#方法2利用项目生成命令)
- [使用Maven进行测试](#使用maven进行测试)
  - [跳过测试](#跳过测试)
  - [动态指定要测试的案例](#动态指定要测试的案例)
  - [包含与排除测试用例](#包含与排除测试用例)
  - [生成测试报告](#生成测试报告)
    - [基本测试报告](#基本测试报告)
    - [测试覆盖率报告](#测试覆盖率报告)
  - [运行TestNG测试](#运行testng测试)
  - [重用测试代码](#重用测试代码)
- [使用Nexus搭建私服](#使用nexus搭建私服)
  - [简介](#简介)
  - [为什么使用Nexus](#为什么使用nexus)
  - [Nexus仓库类型介绍](#nexus仓库类型介绍)
  - [管理本地仓库](#管理本地仓库)
  - [安装流程](#安装流程)
- [Maven插件编写](#maven插件编写)
  - [Maven插件命名规范](#maven插件命名规范)
  - [什么是 Mojo？](#什么是-mojo)
  - [创建 Mojo 工程](#创建-mojo-工程)
  - [简单 Mojo 的创建](#简单-mojo-的创建)
  - [运行自定义 Plugin](#运行自定义-plugin)
  - [执行](#执行)
  - [命令简化](#命令简化)
  - [例子](#例子)

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
     ```xml
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
    [root@localhost ~]# mvn install -Dmaven.test.skip=true
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
## 使用Archtype生成项目
archetype也就是原型，准确说是一个项目模板，我们可以根据该模板来生成项目。

我们通过ide创建的maven项目都有相同的目录结构：
- 在项目的根目录中放置pom.xml
- 在src/main/java目录中放置项目的主代码
- 在src/main/resource目录中放置项目的配置文件
- 在src/test/java目录中放置项目的测试代码
- 在src/test/resource目录中放置项目的测试配置文件

我们称以上这些基本的目录结构和pom.xml文件内容为项目的骨架。项目的骨架是固定的，这样的好处就是为了避免重复造轮子。

项目骨架的生成：
```
[root@localhost ~]# mvn archetype:generate
# 批处理方式
[root@localhost ~]# mvn archetype:generate -B \
-DarchetypeGroupId=org.apache.maven.archetypes \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DarchetypeVersion=1.0 \
-DgroupId=com.study.mavenstudy\
-DartifactId=archetype-test \
-Dversion=1.0-SNAPSHOT \
-Dpackage=com.study.mavenstudy
```

### 常用的archetype
- maven-archetype-quickstart：默认值 常用于一般javase项目结构
- maven-archetype-webapp：用于web项目架构
- appfuse archetype：appfuse是一个集成了很多开源工具的项目，在于帮助程序员快速高效的创建项目，它提供了大量archetype，方便用户使用各种类型的项目

## 根据项目生成archetype的命令
1. 在项目根目录执行如下命令
    ```
    [root@localhost ~]# mvn archetype:create-from-project
    ```
    它会生成archetype项目的相关文件。

    ![img](./img/20180513124040902.png)

2. 切换到项目的target/generated-sources/archetype/下，运行命令mvn install，我们要对archetype项目打包。

    ![img](./img/20180513124535123.png)

## 使用生成的archetype项目创建新项目
### 方法1:利用自动生成的archetype-catalog.xml来创建项目
当我们install后，会在~/m2.目录生成如下定义archetype文件：

![img](./img/20180513164431406.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<archetype-catalog xsi:schemaLocation="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0 http://maven.apache.org/xsd/archetype-catalog-1.0.0.xsd"
    xmlns="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <archetypes>
    <archetype>
      <groupId>com.chenjun</groupId>
      <artifactId>springBootAop-archetype</artifactId>
      <version>1.0.1</version>
      <description>Spring Boot AOP Sample</description>
    </archetype>
  </archetypes>
</archetype-catalog>
```
我们运行:mvn archetype:generate -DarchetypeCatalog=local

表示利用本地archetype-catalog.xml定义的archetype来创建项目。

### 方法2:利用项目生成命令
需要将install后的achetype项目copy到插件查找目录org\apache\maven\archetypes
使用Archtype生成项目，指定对应的archetype插件即可
> PS：记得把自己installl的archetype的jar，手工copy到本地仓库的org\apache\maven\archetypes目录下，因为是从该目录下查找本地插件。


# 使用Maven进行测试
Maven本身并不是一个单元测试框架，它只是在构建执行到特定生命周期阶段的时候，通过插件来执行JUnit或者TestNG的测试用例。这个插 件就是maven-surefire-plugin，也可以称为测试运行器(Test Runner)，它能兼容JUnit 3、JUnit 4以及TestNG。

在默认情况下，maven-surefire-plugin的test目标会自动执行测试源码路径（默认为src/test/java/）下所有符合一组命名模式的测试类。这组模式为：

- **/Test*.java：任何子目录下所有命名以Test开关的Java类。
- **/*Test.java：任何子目录下所有命名以Test结尾的Java类。
- **/*TestCase.java：任何子目录下所有命名以TestCase结尾的Java类。

## 跳过测试
要想跳过测试，在命令行加入参数skipTests就可以了。如：
```shell script
[root@localhost ~]# mvn package -DskipTests
[root@localhost ~]# mvn install -DskipTests 
```

也可以在pom配置中提供该属性。
```xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <skipTests>true</skipTests>  
    </configuration>  
</plugin> 
```
有时候可能不仅仅需要跳过测试运行，还要跳过测试代码的编译：

```shell script
[root@localhost ~]# mvn package -Dmaven.test.skip=true 
```

也可以在pom中配置maven.test.skip（不推荐）：

```xml
<plugin>  
    <groupId>org.apache.maven.plugin</groupId>  
    <artifactId>maven-compiler-plugin</artifactId>  
    <version>2.1</version>  
    <configuration>  
        <skip>true</skip>  
    </configuration>  
</plugin>  
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <skip>true</skip>  
    </configuration>  
</plugin>  
```

## 动态指定要测试的案例
maven-surefire-plugin提供了一个test参数让Maven用户能够在命令行指定要运行的测试用例。如：
```shell script
[root@localhost ~]# mvn test -Dtest=RandomGeneratorTest  
```

也可以使用通配符：
```shell script
[root@localhost ~]# mvn test -Dtest=Random*Test
```

或者也可以使用“，”号指定多个测试类：
```
[root@localhost ~]# mvn test -Dtest=Random*Test,AccountCaptchaServiceTest  
```
如果没有指定测试类，那么会报错并导致构建失败。
```
[root@localhost ~]# mvn test -Dtest 
```
这时候可以添加-DfailIfNoTests=false参数告诉maven-surefire-plugin即使没有任何测试也不要报错。
```
[root@localhost ~]# mvn test -Dtest -DfailIfNoTests=false  
```
由此可见，命令行参数-Dtest -DfailIfNoTests=false是另外一种路过测试的方法

## 包含与排除测试用例
如果由于历史原因，测试类不符合默认的三种命名模式，可以通过pom.xml设置maven-surefire-plugin插件添加命名模式或排除一些命名模式。
```xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <includes>  
            <include>**/*Tests.java</include>  
        </includes>  
        <excludes>  
            <exclude>**/*ServiceTest.java</exclude>  
            <exclude>**/TempDaoTest.java</exclude>  
        </excludes>  
    </configuration>  
</plugin>  
```

## 生成测试报告
### 基本测试报告
默认情况下，maven-surefire-plugin会在项目的target/surefire-reports目录下生成两种格式的错误报告。
简单文本格式——内容十分简单，可以看出哪个测试项出错。
与JUnit兼容的XML格式——XML格式已经成为了Java单元测试报告的事实标准，这个文件可以用其他的工具如IDE来查看。
### 测试覆盖率报告
测试覆盖率是衡量项目代码质量的一个重要的参考指标。Cobertura是一个优秀的开源测试覆盖率统计工具（详见 http://cobertura.sourceforge.net/)，Maven通过cobertura-maven-plugin与之集成，用户可 以使用简单的命令为Maven项目生成测试覆盖率报告。运行下面命令生成报告：
```
[root@localhost ~]# mvn cobertura:cobertura
```
## 运行TestNG测试
TestNG是Java社区中除了JUnit之外另一个流行的单元测试框架。TestNG在JUnit的基础上增加了很多特性，其站点是http://testng.org/ .添加TestNG依赖：
```xml
<dependency>  
    <groupId>org.testng</groupId>  
    <artifactId>testng</artifactId>  
    <version>5.9</version>  
    <scope>test</scope>  
</dependency> 
```
 下面是JUnit和TestNG的常用类库对应关系

|JUnit类|TestNG类|作用|
|:--|:--|:--|
|org.junit.Test|org.testng.annotations.Test|标注方法为测试方法|
|org.junit.Assert|org.testng.Assert|检查测试结果|
|org.junit.Before|org.testng.annotations.BeforeMethod|标注方法在每个测试方法之前运行|
|org.junit.After|org.testng.annotations.AfterMethod|标注方法在每个测试方法之后运行|
|org.junit.BeforeClass|org.testng.annotations.BeforeClass|标注方法在所有测试方法之前运行|
|org.junit.AfterClass|org.testng.annotations.AfterClass|标注方法在所有测试方法之后运行|

TestNG允许用户使用一个名为testng.xml的文件来配置想要运行的测试集合。如在类路径上添加testng.xml文件，配置只运行RandomGeneratorTest
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<suite name="Suite1" verbose="1">  
    <test name="Regression1">  
        <classes>  
            <class name="com.juvenxu.mvnbook.account.captcha.RandomGeneratorTest" />  
        </classes>  
    </test>  
</suite>  
```
同时再配置maven-surefire-plugin使用该testng.xml，如：
```xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <suiteXmlFiles>  
            <suiteXmlFile>testng.xml</suiteXmlFile>  
        </suiteXmlFiles>  
    </configuration>  
</plugin>  
```
TestNG较JUnit的一大优势在于它支持测试组的概念。如可以在方法级别声明测试组：
```java
@Test(groups={"util","medium"})  
```
然后可以在pom中配置运行一个或多个测试组：
```xml
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-surefire-plugin</artifactId>  
    <version>2.5</version>  
    <configuration>  
        <groups>util,medium</groups>  
    </configuration>  
</plugin> 
```
## 重用测试代码
当命令行运行mvn package的时候，Maven只会打包主代码及资源文件，并不会对测试代码打包。如果测试代码中有需要重用的代码，这时候就需要对测试代码打包了。
这时候需要配置maven-jar-plugin将测试类打包，如：
```xml
<plugin>  
	<groupId>org.apache.maven.plugins</groupId>  
	<artifactId>maven-jar-plugin</artifactId>  
	<version>2.2</version>  
	<executions>  
		<execution>  
			<goals>  
				<goal>test-jar</goal>  
			</goals>  
		</execution>  
	</executions>  
</plugin>  
```
maven-jar-plugin有两个目标，分别为jar和test-jar。这两个目标都默认绑定到default生命周期的package阶段运行，只是test-jar并没有在超级POM中配置，因此需要我们另外在pom中配置。

现在如要引用test-jar生成的测试代码包，可以如下配置：
```xml
<dependency>  
	<groupId>com.juvenxu.mvnbook.account</groupId>  
	<artifactId>account-captcha</artifactId>  
	<version>1.0.0-SNAPSHOT</version>  
	<type>test-jar</type>  
	<scope>test</scope>  
</dependency>  
```

# 使用Nexus搭建私服
[Nexus Repository Manager](https://www.sonatype.com/) 仓库管理有2个版本，专业版和oss版，oss版是免费的，专业版是收费的，我们使用oss版。
## 简介
nexus是一个强大的maven仓库管理器,它极大的简化了本地内部仓库的维护和外部仓库的访问.

nexus是一套开箱即用的系统不需要数据库,它使用文件系统加Lucene来组织数据

nexus使用ExtJS来开发界面,利用Restlet来提供完整的REST APIs,通过IDEA和Eclipse集成使用

nexus支持webDAV与LDAP安全身份认证.

nexus提供了强大的仓库管理功能,构件搜索功能,它基于REST,友好的UI是一个extjs的REST客户端,占用较少的内存,基于简单文件系统而非数据库.

## 为什么使用Nexus
- 节省外网带宽。
- 加速Maven构建。
- 部署第三方构件。
- 提高稳定性，增强控制。
- 降低中央仓库的负荷。
- 控制和审计
- 建立本地内部公用仓库

私服仓库的工作流程

![img](./img/398358-20190716100518508-94247349.png)

## Nexus仓库类型介绍
- hosted：本地仓库，通常我们会部署自己的构件到这一类型的仓库。比如公司的第二方库。
- proxy：代理仓库，它们被用来代理远程的公共仓库，如maven中央仓库。
- group：仓库组，用来合并多个hosted/proxy仓库，当你的项目希望在多个repository使用资源时就不需要多次引用了，只需要引用一个group即可。
- virtual：虚拟仓库，用于适配Maven 1

![img](./img/170144310676162.png)

## 管理本地仓库
我们前面讲到类型为hosted的为本地仓库，Nexus预定义了3个本地仓库，分别是Releases, Snapshots, 3rd Party. 分别讲一下这三个预置的仓库都是做什么用的:

- Releases: 这里存放我们自己项目中发布的构建, 通常是Release版本的, 比如我们自己做了一个FTP Server的项目, 生成的构件为ftpserver.war, 我们就可以把这个构建发布到Nexus的Releases本地仓库. 关于符合发布后面会有介绍.
- Snapshots:这个仓库非常的有用, 它的目的是让我们可以发布那些非release版本, 非稳定版本, 比如我们在trunk下开发一个项目,在正式release之前你可能需要临时发布一个版本给你的同伴使用, 因为你的同伴正在依赖你的模块开发, 那么这个时候我们就可以发布Snapshot版本到这个仓库, 你的同伴就可以通过简单的命令来获取和使用这个临时版本.
- 3rd Party:顾名思义, 第三方库, 你可能会问不是有中央仓库来管理第三方库嘛,没错, 这里的是指可以让你添加自己的第三方库, 比如有些构件在中央仓库是不存在的. 比如你在中央仓库找不到Oracle 的JDBC驱动, 这个时候我们就需要自己添加到3rdparty仓库。
- Apache Snapshots: 用了代理ApacheMaven仓库快照版本的构件仓库
- Central: 用来代理maven中央仓库中发布版本构件的仓库
- Central M1 shadow: 用于提供中央仓库中M1格式的发布版本的构件镜像仓库
- Codehaus Snapshots: 用来代理CodehausMaven 仓库的快照版本构件的仓库

## 安装流程
1. 下载Nexus
2. 启动并登入Nexus
3. 配置Nexus
   - 开启远程索引
   - 建立宿主仓库
   - 创建Nexus仓库组
   - 创建Nexus代理仓库
   - 修改本地Maven的settings.xml
   - 修改maven项目的pom.xml配置 
> 搜索引擎查询详细步骤

# Maven插件编写
## Maven插件命名规范
一般来说，我们会将自己的插件命名为 -maven-plugin，而不推荐使用 maven--plugin，因为后者是 Maven 团队维护官方插件的保留命名方式，使用这个命名方式会侵犯 Apache Maven 商标。

## 什么是 Mojo？
Mojo 就是 Maven plain Old Java Object。每一个 Mojo 就是 Maven 中的一个执行目标（executable goal），而插件则是对单个或多个相关的 Mojo 做统一分发。一个 Mojo 包含一个简单的 Java 类。插件中多个类似 Mojo 的通用之处可以使用抽象父类来封装。

## 创建 Mojo 工程
这里，我们使用 Idea 作为开发工具进行讲解，创建工程选择 Maven，然后在模板中找到 maven-archetype-mojo，点击下一步，输入对应的参数，如：com.xiang/xiang-maven-plugin/1.0-SNAPSHOT，最后点击完成即可创建一个简单的 Mojo 工程。

创建完成后，工程内会生成对应的 pom.xml 文件。其内容比较简单，与普通 Maven 工程的 pom.xml 基本一致，只是自动添加了对 maven-plugin-api 的依赖，这个依赖里面会包含一些 Mojo 的接口与抽象类，在后续编写具体的 Mojo 时再进行详细讲解。
```xml
<dependency>
  <groupId>org.apache.maven</groupId>
  <artifactId>maven-plugin-api</artifactId>
  <version>3.0</version>
</dependency>
```
与普通 pom.xml 文件一个重要的不同之处是它的打包方式：
```xml
<packaging>maven-plugin</packaging>
```
## 简单 Mojo 的创建
工程创建完毕后，我们开始 Mojo 创建之旅，上面提到过 Mojo 是一个简单的 Java 类，那我们创建第一个 Mojo 类用于打印一行输出。
```java
package com.xiang;

import org.apache.maven.plugin.AbstractMojo;
import org.apache.maven.plugin.MojoExecutionException;
import org.apache.maven.plugin.MojoFailureException;
import org.apache.maven.plugins.annotations.Mojo;
import org.apache.maven.plugins.annotations.Parameter;

/**
 * @Author：Yerik Xiang
 * @Date：2020/9/10 10:51
 * @Desc：mvn install;mvn com.xiang:xiang-maven-plugin:1.0-SNAPSHOT:hello -Dhello.name="Yerik"
 */
@Mojo(name = "hello")
public class HelloMojo extends AbstractMojo {

    @Parameter(property = "hello.name", defaultValue = "World")
    private String name;

    public void execute() throws MojoExecutionException, MojoFailureException {
        System.out.println("hello world");
        getLog().info("Hello " + name);
    }

}
```
观察一下这个类，我们发现它继承了 AbstractMojo 这个抽象类，并实现了 execute() 方法，该方法就是用来定义这个 Mojo 具体操作内容，我们只需要根据自己的需要来编写自己的实现即可。

Mojo 操作的实现我们了解了，那怎么让 Maven 知道这是一个 Mojo 而不是一个普通的 Java 类呢？这里，就需要说一下 Mojo 的查找机制了，在处理源码的时候，plugin-tools 会把使用了 @Mojo 注解或 Javadoc 里包含 @goal 注释的类来当作一个 Mojo 类。在上面的例子中，我们使用了 Javadoc 的方法来声明一个 Mojo。同样我们也可以使用 @Mojo 注解来进行声明：

使用 @Mojo 注解，我们需要引入一个新包：
```xml
<dependency>
  <groupId>org.apache.maven.plugin-tools</groupId>
  <artifactId>maven-plugin-annotations</artifactId>
  <version>3.1</version>
</dependency>
```

## 运行自定义 Plugin
与使用其它插件类似，我们需要在 pom.xml 文件中引入插件：
```xml
<build>
    <plugins>
        <plugin>
        <groupId>com.xiang</groupId>
        <artifactId>xiang-maven-plugin</artifactId>
        <version>1.0-SNAPSHOT</version>
        </plugin>
    </plugins>
</build>
```
我们还可通过配置指定Maven插件在生命周期的哪个阶段执行，还可以通过一些命令赋值。

比如我这次的需求，就需要在编译期执行，将需要扫描的类名传入
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.xiang</groupId>
            <artifactId>xiang-maven-plugin</artifactId>
            <version>1.0-SNAPSHOT</version>
            <executions>
                <execution>
                    <!-- 配置compile执行 -->
                    <phase>compile</phase>
                    <goals>
                        <!-- 配置执行目标 -->
                        <goal>xiang</goal>
                    </goals>
                    <configuration>
                        <!-- className是要传入的值 -->
                        <message>Yerik</message>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## 执行
```shell script
[root@localhost ~]# mvn install;mvn com.xiang:xiang-maven-plugin:1.0-SNAPSHOT:hello -Dhello.name="Yerik"
```

可看到终端输出：
```shell script
[INFO] Scanning for projects...
[INFO] 
[INFO] --------------------< com.xiang:xiang-maven-plugin >--------------------
[INFO] Building xiang-maven-plugin Maven Mojo 1.0-SNAPSHOT
[INFO] ----------------------------[ maven-plugin ]----------------------------
[INFO] 
[INFO] --- xiang-maven-plugin:1.0-SNAPSHOT:hello (default-cli) @ xiang-maven-plugin ---
hello world
[INFO] Hello Yerik
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.308 s
[INFO] Finished at: 2020-09-10T14:07:46+08:00
[INFO] ------------------------------------------------------------------------
```

## 命令简化
在刚才运行插件的时候，我们使用全量的插件指引，但这个实在是太长太繁琐了，那我们是否可以缩短我们的执行命令呢？答案肯定是可以的，如果你想要执行的是你本地库中最新版本的插件，那么你可以删除掉版本号；如果你的命名满足前面提及的两种命令方式，你可以直接使用插件名及 goal 名来运行对应的插件，如：
```shell script
[root@localhost ~]# mvn xiang:hello -Dhello.name="Yerik"
```

## [例子](https://github.com/xiangzz159/JInterview-demo/tree/master/xiang-maven-plugin)
