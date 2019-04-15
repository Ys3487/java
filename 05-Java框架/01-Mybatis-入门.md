## 一 Mybatis简介

#### 1.1 JDBC的缺陷

- 1、数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。
- 2、Sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变 java代码。
- 3、使用 preparedStatement 向占有位符号传参数存在硬编码，因为 sql 语句的 where 条件不一定，可能多也可能少，修改 sql 还要修改代码，系统不易维护。
- 4、对结果集解析存在硬编码（查询列名），sql 变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成 pojo 对象解析比较方便。


#### 1.2 Mybatis说明

mybatis 是一个优秀的基于 java 的持久层框架，它内部封装了 jdbc，使开发者只需要关注 sql 语句本身，而不需要花费精力去处理加载驱动、创建连接、创建 statement 等繁杂的过程。  

mybatis 通过 xml 或注解的方式将要执行的各种 statement 配置起来，并通过 java 对象和 statement 中sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为 java 对象并返回。  

采用 ORM 思想解决了实体和数据库映射的问题，对 jdbc 进行了封装，屏蔽了 jdbc api 底层访问细节，使我们不用与 jdbc api 打交道，就可以完成对数据库的持久化操作。  

ORM：Object Relational Mapping 对象关系映射，通过操作Java实体类来执行sql。

## 二 Mybatis配置

#### 1.0 整体项目结构
![](/images/mybatis1.png)

#### 1.1 创建工程导入坐标

在IDEA中新建Maven空项目:
```
GroupId:com.mine
ArtifactId: testmybatis
```

在pom文件中添加如下配置：
```xml
    <dependencies>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
        </dependency>

    </dependencies>
```
此时IDEA右下角提示import依赖，确认即可，安装完毕后，IDEA左侧External中会出现mybatis包。提示：上述配置中只需要mybatis和mysql包即可。

#### 1.2 创建实体类和DAO接口

数据库的表为：user，分别有字段uid，name，birthday。  

编写数据模型实体类：com.mine.model.User
```java
package com.mine.model;

import java.io.Serializable;
import java.util.Date;

public class User implements Serializable {

    private Integer uid;
    private String name;
    private Date birthday;

    @Override
    public String toString() {
        return "User{" +
                "uid=" + uid +
                ", name='" + name + '\'' +
                ", birthday=" + birthday +
                '}';
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

}

```

编写DAO接口：com.mine.dao.IUserDao(这里的I只是表示这是接口的书写习惯)
```java
package com.mine.dao;

import com.mine.model.User;

import java.util.List;

public interface IUserDao {
    /**
     * 查询所有用户
     */
    List<User> findAll();
}

```

#### 1.3 创建映射配置文件 IUserDao.xml

注意：Mybatis映射配置文件位置必须和dao接口包位置结构相同，遵从了上述的开发配置，我们只需要书写接口类，实体类将由Mybatis自动创建。

该配置文件位于resources目录下，在该目录创建xml文件需要逐个创建目录：com--->mine--->dao
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mine.dao.IUserDao">

    <!-- 配置查询所有操作 -->
    <select id="findAll" resultType="com.mine.model.User">
      select * from user
    </select>

</mapper>
```

#### 1.4 创建Mybatis主配置文件 SqlMapConfig.xml

主配置文件位于resources目录下，还要拷贝log4j.properties文件于该目录下，本文档在resources下提供了log4j.properties，可直接下载。
```xml
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置 mybatis 的环境 -->
    <environments default="mysql">
        <!-- 配置 mysql 的环境 -->
        <environment id="mysql"> <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"></transactionManager> <!-- 配置连接数据库的信息:用的是数据源(连接池) -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 告知 mybatis 映射配置的位置 -->
    <mappers>
        <mapper resource="com/mine/dao/IUserDao.xml"/>
    </mappers>
</configuration>
```

#### 1.5 编写测试类

在test文件夹下新建测试类：com.mine.dao.Test1
```java
package com.mine.dao;

import com.mine.model.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

public class Test1 {

    public static void main(String[] args) throws Exception{

        //1.读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建 SqlSessionFactory 的构建者对象
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        //3.使用构建者创建工厂对象SqlSessionFactory
        SqlSessionFactory factory = builder.build(in);
        //4.使用 SqlSessionFactory 生产 SqlSession 对象
        SqlSession session = factory.openSession();
        //5.使用 SqlSession 创建 dao 接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        //6.使用代理对象执行查询所有方法
        List<User> users = userDao.findAll();

        for(User user : users) {
            System.out.println(user);
        }

        //7.释放资源
        session.close();
        in.close();

    }
}

```

执行测试类即可查看结果。

#### 1.6 基于注解方式的配置

在持久层接口添加注解：
```java
public interface IUserDao {
    /**
    * 查询所有用户
    * @return */
    @Select("select * from user")
    List<User> findAll(); 
}
```

修改主配置文件SqlMapConfig.xml：
```xml
<mappers>
    <mapper class="com.mine.dao.IUserDao"/>
</mappers>
```
注意事项： 在使用基于注解的 Mybatis 配置时，请移除 xml 的映射配置(IUserDao.xml)。