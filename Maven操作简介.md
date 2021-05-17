## 什么是Maven？

如今我们构建一个项目需要用到很多第三方的类库，如写一个使用Spring的Web项目就需要引入大量的jar包。一个项目Jar包的数量之多往往让我们瞠目结舌，并且Jar包之间的关系错综复杂，一个Jar包往往又会引用其他Jar包，缺少任何一个Jar包都会导致项目编译失败。

以往开发项目时，程序员往往需要花较多的精力在引用Jar包搭建项目环境上，而这一项工作尤为艰难，少一个Jar包、多一个Jar包往往会报一些让人摸不着头脑的异常。

而Maven就是一款帮助程序员构建项目的工具，我们只需要告诉Maven需要哪些Jar 包，它会帮助我们下载所有的Jar，极大提升开发效率。

安装Maven 和 Maven的Eclipse插件
 安装Maven和Maven的Eclipse插件，用Eclipse的同学可以参考，推荐使用IDEA开发；

Maven规定的目录结构
若要使用Maven，那么项目的目录结构必须符合Maven的规范，其目录结构如下：



## Maven基本命令

1.-v:查询Maven版本

本命令用于检查maven是否安装成功。

Maven安装完成之后，在命令行输入mvn -v，若出现maven信息，则说明安装成功。

2.compile：编译

将java源文件编译成class文件

3.test:测试项目

执行test目录下的测试用例

4.package:打包

将项目打成jar包

5.clean:删除target文件夹

6.install:安装

将当前项目放到Maven的本地仓库中。供其他项目使用

## 什么是Maven仓库？

Maven仓库用来存放Maven管理的所有Jar包。分为：本地仓库 和 中央仓库。

本地仓库：Maven本地的Jar包仓库。
中央仓库： Maven官方提供的远程仓库。
当项目编译时，Maven首先从本地仓库中寻找项目所需的Jar包，若本地仓库没有，再到Maven的中央仓库下载所需Jar包。

## 什么是“坐标”？

在Maven中，坐标是Jar包的唯一标识，Maven通过坐标在仓库中找到项目所需的Jar包。

如下代码中，groupId和artifactId构成了一个Jar包的坐标。

<dependency>
   <groupId>cn.missbe.web.search</groupId>
   <artifactId>resource-search</artifactId>
   <packaging>jar</packaging>
   <version>1.0-SNAPSHOT</version>
</dependency>
groupId:所需Jar包的项目名
artifactId:所需Jar包的模块名
version:所需Jar包的版本号

#### 传递依赖 与 排除依赖

传递依赖：如果我们的项目引用了一个Jar包，而该Jar包又引用了其他Jar包，那么在默认情况下项目编译时，Maven会把直接引用和简洁引用的Jar包都下载到本地。
排除依赖：如果我们只想下载直接引用的Jar包，那么需要在pom.xml中做如下配置：(将需要排除的Jar包的坐标写在中)
<exclusions>
   <exclusion>
      <groupId>cn.missbe.web.search</groupId>
      <artifactId>resource-search</artifactId>
      <packaging>pom</packaging>
      <version>1.0-SNAPSHOT</version>
   </exclusion>
</exclusions>

#### 依赖范围scope

在项目发布过程中，帮助决定哪些构件被包括进来。欲知详情请参考依赖机制。    

- compile ：默认范围，用于编译      
- provided：类似于编译，但支持你期待jdk或者容器提供，类似于classpath      
- runtime: 在执行时需要使用      
- test:    用于test任务时使用      
- system: 需要外在提供相应的元素。通过systemPath来取得      
- systemPath: 仅用于范围为system。提供相应的路径      
- optional:   当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用

#### 依赖冲突

若项目中多个Jar同时引用了相同的Jar时，会产生依赖冲突，但Maven采用了两种避免冲突的策略，因此在Maven中是不存在依赖冲突的。

1.短路优先

本项目——>A.jar——>B.jar——>X.jar
本项目——>C.jar——>X.jar
若本项目引用了A.jar，A.jar又引用了B.jar，B.jar又引用了X.jar，并且C.jar也引用了X.jar。

在此时，Maven只会引用引用路径最短的Jar。

2.声明优先

若引用路径长度相同时，在pom.xml中谁先被声明，就使用谁。

# 聚合

Maven聚合（或者称为多模块），是为了能够使用一条命令就构建多个模块，例如已经有两个模块，分别为account-email,account-persist，我们需要创建一个额外的模块（假设名字为account-aggregator，然后通过该模块，来构建整个项目的所有模块，accout-aggregator本身作为一个Maven项目，它必须有自己的POM,不过作为一个聚合项目，其POM又有特殊的地方，看下面的配置：

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
        http://maven.apache.org/maven-v4_0_0.xsd>
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.juvenxu.mvnbook.account</groupId>
        <artifact>account-aggregator</artifact>
        <version>1.0.0-SNAPSHOT</version>
        <packaging>pom</packaging>
        <name>Account Aggregator</name>
        <modules>
            <module>account-email</module>
            <module>account-persist</module>
        </modules>
</project>
上面有一个特殊的地方就是packaging,其值为pom,如果没有声明的话，默认为jar。对于聚合模块来说，其打包方式必须为pom，否则无法构建。

modules里的每一个module都可以用来指定一个被聚合模块，这里每个module的值都是一个当前pom的相对位置，本例中account-email、account-persist位于account-aggregator目录下，当三个项目同级的时候，上面的两个module应该分别为../account-email和../account-persist。

Maven聚合模块的两种结构：

1）父子目录结构关系：聚合模块是被聚合模块父文件夹

配置方式：<module>account-email</module>

                     <module>account-persist</module>

2）平行目录结构：聚合模块是被聚合模块是平级的文件夹（我通常使用该方式）

配置方式：<module>../account-email</module>

                     <module>../account-persist</module>

![img](https://img-blog.csdnimg.cn/20191124135237426.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nemV5dWFhYQ==,size_16,color_FFFFFF,t_70)

# 继承

在构建多个模块的时候，往往会多有模块有相同的groupId、version，或者有相同的依赖，例如：spring-core、spring-beans、spring-context和junit等，或者有相同的组件配置，例如：maven-compiler-plugin和maven-resources-plugin配置，在Maven中也有类似Java的继承机制，那就是POM的继承。

继承POM的用法

面向对象设计中，程序员可以通过一种类的父子结构，在父类中声明一些字段和方法供子类继承，这样可以做到“一处声明、多处使用”，类似的我们需要创建POM的父子结构，然后在父POM中声明一些配置，供子POM继承。

下面声明一个父POM，如下：

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:shemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.juvenxu.mvnbook.account</groupId>
    <artifactId>account-parent</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Account Parent</name>
</project>
这个父POM中，groupId和version和其它模块一样，它的packaging为pom，这一点和聚合模块一样，==作为父模块的POM，其打包类型也必须为pom==,由于父模块只是为了帮助消除配置的重复，因此它本身不包含除POM之外的项目文件，也就不需要src/main/java之类的文件夹了。

有了父模块，就需要其它模块来继承它。首先将account-email的POM修改如下：

account-email继承account-parent的POM

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:shemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
    <parent>
        <groupId>com.juvenxu.mvnbook.account<groupId>
        <artifactId>account-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../account-parent/pom.xml</relativePath>
    </parent
    

    <artifactId>account-email</artifactId>
    <name>Account Email</name>
    
    <dependencies>
        ....
    </dependencies>
    <build>
        <plugins>
            ....
        </plugins>
    </build>
</project>
上面POM中使用parent元素声明父模块，==paren下的子元素groupId、artifactId和version指定了父模块的坐标，这三个元素是必须的==。元素relativePath表示了父模块POM的相对位置。当项目构建时，Maven会首先根据relativePath检查父POM，如果找不到，再从本地仓库查找。relativePath的默认值是../pom.xml,Maven默认父POM在上一层目录下。

上面POM没有为account-email声明groupId，version,不过并不代表account-email没有groupId和version，实际上，这个子模块隐式的从父模块继承了这两个元素，这也就消除了不必要的配置。上例中，父子模块使用了相同的groupId和version，如果遇到子模块需要使用和父模块不一样的groupId或者version的情况，可以在子模块中显式声明。对于artifactId元素来说，子模块更应该显式声明，因为如果完全继承groupId、artifactId、version,会造成坐标冲突；另一方面，即使使用不同的groupId或version,同样的artifactId容易造成混淆。

account-persist继承account-parent的POM

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:shemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
    <parent>
        <groupId>com.juvenxu.mvnbook.account<groupId>
        <artifactId>account-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../account-parent/pom.xml</relativePath>
    </parent
    

    <artifactId>account-persist</artifactId>
    <name>Account Persist</name>
    
    <dependencies>
        ....
    </dependencies>
    <build>
        <testResources>
            <testResource>
                <directory>src/test/resources</directory>
                <filtering>true</filtering>
            </testResource>
        </testResources>
        <plugins>
            ....
        </plugins>
    </build>
</project>
最后，同样需要把account-parent加入到聚合模块accountp-aggregator中，代码如下：

将account-parent加入到聚合模块

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
        http://maven.apache.org/maven-v4_0_0.xsd>
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.juvenxu.mvnbook.account</groupId>
        <artifact>account-aggregator</artifact>
        <version>1.0.0-SNAPSHOT</version>
        <packaging>pom</packaging>
        <name>Account Aggregator</name>
        <modules>
            <module>account-email</module>
            <module>account-persist</module>
            <module>account-parent</module>
        </modules>
</project>
可继承的POM元素

groupId:项目组ID,项目坐标的核心元素
version:项目版本,项目坐标的核心元素
description:项目的描述信息
organnization:项目的组织信息
inceptionYear:项目的创始年份
url:项目的URL地址
developers:项目的开发者信息
contributors:项目的贡献者信息
distributionManagement:项目的部署配置
issueManagement:项目的缺陷跟踪系统信息
ciManagement:项目的集成信息
scm:项目的版本控制系统信息
mailingLists:项目的邮件列表信息
properties:自定义的Maven属性
dependencies:项目的依赖配置
dependencyManagement:项目的依赖管理配置
repositories:项目的仓库配置
build:包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等
reporting:包括项目的报告输出目录配置，报告插件配置等。
依赖管理

当多有模块中有相同的依赖时，我们可以将这些依赖提取出来，统一在父POM中声明，这样就可以简化子模块的配置了，但是这样还是存在问题，当想项目中加入一些，不需要这么多依赖的模块，如果让这个模块也依赖那些不需要的依赖，显然不合理。

Maven提供的dependentcyManagement元素既能让子模块继承到父模块的依赖配置，又能保证子模块依赖使用的灵活度。在dependentcyManagement元素下的依赖声明不会引入实际的依赖，不过他能够约束denpendencies下的依赖使用。对上面的accoun-parent进行改进，代码如下：

<project
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:shemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.juvenxu.mvnbook.account</groupId>
    <artifactId>account-parent</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Account Parent</name
    <properties>
        <springframework.version>2.5.6</springframework.version>
        <junit.version>4.7</junit.version>
    </properties>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${springframework.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
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
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${springframework.version}</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
上面使用了dependencyManagement声明的依赖既不会给account-parent引入依赖，也不会给它的子模块引入依赖，不过这段配置是会被继承的。

修改account-email的POM如下：

继承dependencyManagement的account-email POM

<properties>
    <javax.mail.version>1.4.1</javax.mail.version>
    <greenmail.version>1.3.1b</greenmail.version>
</properties>
<dependencyManagement>
    <dependencies>
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
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.mail</groupId>
            <artifactId>mail</artifactId>
            <version>${javax.mail.version}</version>
        </dependency>
        <dependency>
            <groupId>javax.icegreen</groupId>
            <artifactId>greenmail</artifactId>
            <version>${greenmail.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
子模块只需要配置简单的groupId和artifactId就能获得对应的依赖信息，从而引入正确的依赖。

这种依赖管理机制似乎不能减少太多的POM配置，但是其好处很大，原因在于在POM中使用dependencyManagement声明依赖能够统一规范项目中依赖的版本，当依赖的版本在父POM中声明之后，子模块在使用依赖的时候就无效声明版本，也就不会发生多个子模块使用依赖版本不一致的情况。这可以降低依赖冲突的几率。

如果在子模块不声明依赖的使用，即使该依赖已经在父POM的dependencyManagement中声明了，也不会产生任何实际的效果。

如果子模块不声明依赖的使用，即使该依赖已经在父POM的dependencyManagement中声明了，也不会产生任何实际的效果，如account-persist的POM。

<properties>
    <dom4j.version>1.6.1</dom4j.version>
</properties>
<dependencies>
    <dependency>
        <groupId>dom4j</groupId>
        <artifactId>dom4j</artifactId>
        <version>dom4j.version</version>
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
这里没有声明spring-context-support,那么该依赖就不会被引入。这正是dependencyManagement的灵活性所在

另外，依赖范围有个import范围，import依赖范围只有在dependencyManagement下才有效果，使用该范围的依赖通常指向一个POM，作用是将目标中的dependencyManagement配置导入到当前POM的dependencyManagement中配置，除了复制配置或者继承这两种方式之外，还可以使用import范围依赖将这一配置导入。
使用import范围依赖导入依赖管理配置

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.juvenxu.mvnbook.account<groupId>
            <artifactId>account-parent</artifactId>
            <version>1.0-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
注意，上面代码中依赖的type为pom,import依赖范围由于其特殊性，一般都指向打包类型为pom的模块。如果有多个项目，他们使用的依赖版本都是一致的，则可以定义一个dependencyManagement专门管理依赖的POM，然后在各个项目中导入这些依赖管理配置。

插件管理

Maven提供了dependencyManagement元素帮忙管理依赖，类似地，Maven也提供了pluginManagement元素帮忙管理插件。该元素中配置的依赖不会造成实际的插件调用行为，当POM中配置了真正的plugin元素，并且其groupId和artifactId与pluginManagement中配置的插件匹配时，pluginManagement的配置才会影响实际的插件行为。

前面有如何通过组件去将jar-no-fork目标绑定到verity生命周期阶段，以生成项目源码包。如果一个项目中有很多子模块，并且需要这些模块的源码包，那么很显然，为所有模块重复类似的插件配置不是最好的办法，这时最好的办法是在父POM中使用pluginManagement配置插件，代码如下：

<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.1.1</version>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
当子模块需要生成源码包的时候，只需要如下简单的配置，代码如下：

子模块声明使用了maven-source-plugin插件，同时又继承了父模块的pluginManagement配置，两者基于groupId和artifactId匹配合并。

有了pluginManagement元素，accout-email和accout-persist的POM也能得以简化了他们都配置了maven-compiler-plugin和maven-resources-plugin。可以将这两个插件的配置移到account-parent的pluginManagement元素中，代码如下：

<build>
    <pluginMangement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.5</source>
                    <target>1.5</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifact>maven-resources-plugin</artifact>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </pluginMangement>
</build>
accout-email和account-persist可以完全地移除关于maven-compiler-plugin和maven-resources-plugin的配置，但他们仍然能享受这这个插件的服务，前一插件开启了jdk1.5编译的支持，后一插件也会使用UTF-8编码处理资源文件。这背后涉及了很多Maven机制，首先，内置的插件绑定关系将两个插件绑定到了account-email和account-persist生命周期上，其次，超级POM为这两个插件申明了版本;最后，account-parent中的pluginManagement对这两个插件的行为进行了配置。

当项目中的多个模块有同样的插件配置时，应当将配置移到父POM的pluginMangement元素中。即使各个模块对于同一插件的具体配置不尽相同，也应当使用父POM的pluginManagement元素同意声明插件的版本。甚至可以要求将所有用到的插件的版本在父POM的pluginManagement中声明，子模块使用插件时不配置版本信息，这么做可以统一项目的插件版本，避免潜在的插件不一致或者不稳定的问题，也更易于维护。

3、聚合与继承的关系
多模块中的聚合与继承其实是两个概念，其目的是完全不同的，前者主要是为了方便快速构建项目，后者主要是为了消除重复配置。

对于聚合模块来说，它知道有哪些被聚合的模块，但那些被聚合的子模块不知道这个聚合模块的存在。

对于继承关系的父POM来说，它不知道哪些子模块继承于它，但那些子模块都必须知道自己的父POM是什么。



###                                                                                聚合关系与继承关系的比较

在现有的实际项目中，往往会发现一个POM既是聚合POM，又是父POM，这么做主要是为了方便。一般来说，融合使用聚合与继承也没什么问题，例如可以将account-aggretor和account-parent其POM如下：

![img](https://img-blog.csdnimg.cn/20181104221844673.png)

<project xmlns="http://maven-apache.org/POM/4.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.juvenxu.mvnbook.account</groupId>
    <artifactId>account-parent</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Account Parent</name>
    <modules>
        <module>account-persist</module>
    </modules>
    <properties>
        <springframework.version>2.5.6</springframework.version>
        <junit.version>4.7</junit.version>
    </properties>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${springframework.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
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
                <groupId>junit<groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version
                <scope>test</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <configuration>
                        <source>1.5</source>
                        <target>1.5</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-resource-plugin<artifactId>
                    <configuration>
                        <endcoding>UTF-8</encoding>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build
</project>
可以看到POM的打包方式为pom,它包含了一个modules元素，表示用来聚合account-persist和account-email两个模块，它还包含了properties、dependencyManagement和pluginManagement元素供子模块继承。