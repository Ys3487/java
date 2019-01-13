## 一 Java语言简介
Java语言是由詹姆斯·高斯林于1990年在Sun公司工作期间开发的跨平台开发语言。  
Java语言平台版本：
```
J2SE：
    Java 2 Platform Standard Edition标准版，Java开发的基础
J2ME：
    开发电子消费品和嵌入式设备的解决方案，基本无人使用
J2EE：
    为企业应用提供的一套解决方案，包含：Servlet，JSP等，主要为Web应用
```
## 二 Java语言特点
- 开源
- 跨平台
- 完全面向对象
Java的跨平台：通过Java语言编写的程序可以在不同的系统上运行，即Java在上市时的广告语：write once, run anywhere!(一处编译，到处运行!)  
Java跨平台原理：在需要运行Java程序的操作系统上，先安装一个Java公司提供的对应平台的JVM虚拟机，由JVM来实现跨越操作系统的不同，程序员只用关注Java代码的书写即可。  
![](/images/J2SE-简介-01.png)
## 三 Java环境搭建
#### 3.1 JRE与JDK
JRE：Java Runtime Environment，Java运行环境，该环境包含java虚拟机和java核心类库，如果只是要运行一个已经开发完毕的java程序，只需安装jre即可。  
JDK：Java Development Kit，Java开发工具包，内含JRE以及开发所需的常用Java工具类库（编译工具javac与打包工具jar）。  
简单而言：使用JDK开发Java程序，然后交给JRE运行。
#### 3.2 JDK下载与安装
下载地址：https://www.oracle.com/  
注意：推荐安装在没有空格和中文的目录中！
Java11与低版本安装目录的不同：低版本Java在安装JDK时，除了会安装jdk，以及在jdk目录下安装jre外，还会在jdk同级目录下额外再安装一个jre。Java11中，没有了额外的jre文件。  
安装完毕后，配置环境变量:
```
新建JAVA_HOME，值为：C:\Dev\Java\bin
配置Path环境，添加：%JAVA_HOME%
安装完毕后，输入以下命令：
    java --version      # 查看java版本
    java                # 查看java运行命令
    javac               # 查看javac编译命令
```
注意：
- win10环境下其实已经不需要额外书写JAVA_HOME了，直接在Path中添加Java的bin目录即可（因为win10以下的系统Path是一长串字符串，不容易辨识，win改进了）
- mac直接安装下载包即可。
#### 3.3 Linux部署java环境
## 四 HelloWorld
新建一个Test.java文件，使用VSCode打开(会提示安装java插件，安装即可)，编辑内容为:
```java
class Test {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
``` 
如果VSCode安装了Coder Runner插件，此时可以直接右键运行，查看结果。当然也可以直接编译运行：
```
javac Test.java         # 此时会将Test.java编译，生成一个编译好的字节码文件 Test.class
java Test               # 注意没有文件后缀，此时jre虚拟机运行Test.class文件
```
使用IDEA作为开发工具配置注意：
- IDEA可以配置项目的jdk版本，也可以配置全局的JDK版本，位于Flie-Project Structure内
- IDEA需要配置class文件的生成目录，位于Flie-Project Structure-Project compiler output
- 使用IDEA书写HELLO WORLD步骤：新建一个空文件夹，并用IDEA打开，新建一个module，并在modue的src目录下创建Test.java，右键运行即可
如图所示：
![](/images/J2SE-简介-02.png)
## 五 Java中的注释
```
单行注释：  // 注释文字
多行注释：  /* 注释文字 */
文档注释：  /** 注释文字 */
``
