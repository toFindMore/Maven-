## 《Maven实战》读书笔记

1. **准备**

   * 阿里云 CentOS 7.4云主机
   * 主机已配置java环境

2. **安装Maven**

   ```bash
   cd /usr/local/src/
   wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.1.1/binaries/apache-maven-3.1.1-bin.tar.gz
   tar zxf apache-maven-3.1.1-bin.tar.gz
   mv apache-maven-3.1.1 /usr/local/maven3
   ```

   配置 /etc/profile

   ```bash
   #在适当的位置添加
   export M2_HOME=/usr/local/maven3
   export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
   ```

   生效，运行以下命令，或重启主机 (reboot)

   ```bash
   source /etc/profile
   ```

   版本检查

   ```bash
   mvn -v
   ```

3. **Maven使用入门**

   * **编写POM**

     * 建立hello-world文件夹

     * 进入文件夹，创建 pom.xml

       ```xml
       <?xml version = "1.0" encoding = "UTF-8"?>
       <project xmlns = "http://maven.apache.org/POM/4.0.0"
                xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
       http://maven.apache.org/maven-v4_0_0.xsd">
           <modelVersion >4.0.0</modelVersion>
           <groupId >com.juvenxu.mvnbook</groupId>
           <artifactId >hello-world</artifactId>
           <version >1.0-SNAPSHOT</version>
           <name >Maven Hello World Project</name>
       </project>
       ```

       + project：所有 pom.xml 的根元素 ，即POM的版本问题。对于Maven2和Maven3来说，POM只能是4.0.0

       + groupId：定义了项目属于哪个组，如果在 XXX .com公司创建的myApp，groupId可以这么表示为 com.XXX.myApp
       + artifactId：Maven 项目在组中唯一的ID。
       + version代表版本号，SNAPSHOT代表项目还在开发中，是不稳定的版本。关于版本控制在后面章节会有所介绍。
       + name：不是必须要求的，可以理解为替POM取一个好听的名字。

   * **编写主代码**

     * 在hello-world文件夹中新建文件夹 src/main/java,并创建com/juvenxu/mvnbook/helloworld/HelloWorld.java

       ```bash
       mkdir src
       mkdir src/main
       mkdir src/main/java
       vim src/main/java/HelloWorld.java
       ```

       ```java
       package com.juvenxu.mvnbook.helloworld;
       
       public class HelloWorld
       {
           public String sayHello()
           {
               return "Hello Maven";
           }
       
           public static void main(String[] args)
           {
               System.out.print(new HelloWorld().sayHello());
           }
       }
       ```

       在存放pom.xml的目录下，运行以下命令：

       ```bash
       mvn clean compile
       ```

       出现  BUILD SUCCESSFUL 字样表明构建成功。

       此时在根目录下出现了target文件夹，clean命令其实是将 target 文件夹删除，compile命令对代码进行编译，在 target/classes 生成 com/juvenxu/mvnbook/helloworld/HelloWorld.class 文件。

   * **编写测试代码**

     * 进入 maven 仓库的 <a href="https://mvnrepository.com/artifact/junit/junit/4.12">junit 模块</a>

     * 扩充 pom.xml 文件，得到

       ```xml
       <?xml version = "1.0" encoding = "UTF-8"?>
       <project xmlns = "http://maven.apache.org/POM/4.0.0"
                xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
       http://maven.apache.org/maven-v4_0_0.xsd">
           <modelVersion >4.0.0</modelVersion>
           <groupId >com.juvenxu.mvnbook</groupId>
           <artifactId >hello-world</artifactId>
           <version >1.0-SNAPSHOT</version>
           <name >Maven Hello World Project</name>
           <dependencies>
               <!-- https://mvnrepository.com/artifact/junit/junit -->
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
                   <version>4.12</version>
                   <scope>test</scope>
               </dependency>
           </dependencies>
       </project>
       ```

       scope 表示依赖范围，此处表示只对测试（test）有效。scope有以下几种类型：

       **1.compile：**默认值  他表示被依赖项目需要参与当前项目的编译，还有后续的测试，运行周期也参与其中，是一个比较强的依赖。打包的时候通常需要包含进去

       **2.test：**依赖项目仅仅参与测试相关的工作，包括测试代码的编译和执行，不会被打包，例如：junit

       **3.runtime：**表示被依赖项目无需参与项目的编译，不过后期的测试和运行周期需要其参与。与compile相比，跳过了编译而已。例如JDBC驱动，适用运行和测试阶段

       **4.provided：**打包的时候可以不用包进去，别的设施会提供。事实上该依赖理论上可以参与编译，测试，运行等周期。相当于compile，但是打包阶段做了exclude操作

       **5.system：**从参与度来说，和provided相同，不过被依赖项不会从maven仓库下载，而是从本地文件系统拿。需要添加systemPath的属性来定义路径

     * 编写测试类

       在 src 目录下创建 test/java 文件夹，编写HelloWorld的测试类。如下所示， 

       ```java
       package com.juvenxu.mvnbook.helloworld;
       
       import static org.junit.Assert.assertEquals;
       import org.junit.Test;
       
       public class HelloWorldTest {
           @Test
           public void testSayHello()
           {
               HelloWorld helloWorld = new HelloWorld();
               String result = helloWorld.sayHello();
               assertEquals("Hello Maven",result);
           }
       }
       ```

       在根目录执行 mvn clean test 命令，BUILD SUCCESS ！

       执行命令 mvn clean test，显示测试报告。

   * **打包和运行**
     * 运行命令 mvn clean package , 会在 target 文件下生成对应的 jar 包