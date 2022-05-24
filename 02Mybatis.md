### 第一章 初识Mybatis框架

#### 1.1 框架概念

- 前辈们已经写好的成熟的解决方案
- 程序中框架【代码半成品】
- 生活中“框架”
  - 手抓饼
  - 买毛坯房
- SSM【Spring+SpringMVC+Mybatis】
  - Mybatis：JDBC【dao层】半成品
  - SpringMVC：Servlet【表示层、表述层、控制层、表现层】半成品
  - Spring：大管家

#### 1.2 Mybatis框架简介

- Mybatis前身Ibatis

- **Mybatis是一个半自动化的持久化层ORM框架**

  - ORM：Object  Relational  Mapping【对象关系映射】
    - 理解：将对象中属性与表中字段进行关联映射
    - 优势：操作对象中属性，就可以影响表中字段数据

  ![image-20220507101305212](02Mybatis.assets\image-20220507101305212.png)

- Mybatis与Hibernate对比
  - Hibernate**全自动化**持久层ORM框架
    - 不需要手写sql【不方便优化SQL】
  - Mybatis**半自动化**持久层ORM框架
    - 需要手写sql【方便优化SQL】
- POJO（Plain Ordinary Java Object，普通的Java对象）
  
  - POJO作用等同于JavaBean

#### 1.3 Mybatis官方网址

- 下载网址：https://github.com/mybatis/mybatis-3
- **文档网址：https://mybatis.org/mybatis-3/**

### 第二章 搭建Mybatis框架

#### 2.1 搭建框架思路

- 导入框架所需jar包【坐标】
- 编写配置文件【不同框架配置文件不同】
- 使用框架核心类库【不同框架核心类库不同】

#### 2.2 搭建Mybatis框架步骤

1. 准备

   - 建库建表建约束
   - 准备maven工程

2. 导入mybatis所需jar包

   ```xml
   <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.26</version>
   </dependency>
   <!--导入MyBatis的jar包-->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.6</version>
   </dependency>
   <!--junit-->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
       <scope>test</scope>
   </dependency>
   ```

3. 编写mybatis-config.xml核心配置文件

   - 名称：没有具体要求，建议使用mybatis-config.xml

   - 位置：src/main/resources

   - 作用：设置mybatis基本行为

   - 示例代码

     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE configuration
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-config.dtd">
     
     <configuration>
         <!--设置数据库连接环境-->
         <environments default="development">
             <environment id="development">
                 <!-- 设置事务管理器 -->
                 <transactionManager type="JDBC"/>
                 <!--设置数据源-->
                 <dataSource type="POOLED">
                     <property name="driver" value="com.mysql.jdbc.Driver"/>
                     <property name="url" value="jdbc:mysql://localhost:3306/db220227"/>
                     <property name="username" value="root"/>
                     <property name="password" value="root"/>
                 </dataSource>
             </environment>
         </environments>
         <!--    设置映射文件路径-->
         <mappers>
             <mapper resource="mapper/EmployeeMapper.xml"/>
         </mappers>
     </configuration>
     ```

4. 编写POJO及对应接口【CRUD】

   ```java
   package com.atguigu.mapper;
   import com.atguigu.pojo.Employee;
   /**
    * @author Chunsheng Zhang 尚硅谷
    * @create 2022/5/7 10:53
    */
   public interface EmployeeMapper {
       /**
        * 通过员工id获取员工信息
        */
       public Employee selectEmpById(int empId);
   }
   ```

5. 编写映射文件【xxxMapper.xml】

   - 名称：要求与接口名称一致

   - 位置：src/main/resouces/mapper/

   - **主要作用：为接口中的方法定义SQL语句**【三点一致】

     - 映射文件名称与接口名称一致
     - 映射文件中mapper标签的namespace属性，与接口的全类名一致
     - 映射文件中sql语句的id属性值，与接口中方法名一致

   - 示例代码

     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE mapper
             PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     <mapper namespace="com.atguigu.mapper.EmployeeMapper">
         <!--定义查询sql语句
             resultType:设置结果集类型全类名
         -->
       <select id="selectEmpById" resultType="com.atguigu.pojo.Employee">
             SELECT
                 id,
                 last_name lastName,
                 email,
                 salary
             FROM
                 tbl_employee
             WHERE
                 id = #{empId}
       </select>
     </mapper>
     ```

6. 使用核心类库进行测试

   - 核心类库：SqlSession

   - 先获取SqlSessionFactory，再获取SqlSession，最后通过SqlSession获取接口的代理对象，进行测试

   - 示例代码

     ```java
     @Test
     public void testHw() throws Exception{
     
         //定义mybatis核心配置文件路径
         String resource = "mybatis-config.xml";
         //使用IO流，读取配置文件
         InputStream inputStream = Resources.getResourceAsStream(resource);
         //通过SqlSessionFactoryBuilder构建SqlSessionFactory对象
         SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
     
         //通过sqlSessionFactory获取SqlSession对象
         SqlSession sqlSession = sqlSessionFactory.openSession();
         //最后通过SqlSession获取接口的代理对象，进行测试
         //旧版本玩法：sqlSession.select();
         EmployeeMapper employeeMapper = sqlSession.getMapper(EmployeeMapper.class);
         //测试
         Employee employee = employeeMapper.selectEmpById(1);
         System.out.println("employee = " + employee);
     
     }
     ```

#### 2.3 搭建Mybatis框架常见问题

![image-20220507113607046](02Mybatis.assets\image-20220507113607046.png)

![image-20220507113618641](02Mybatis.assets\image-20220507113618641.png)



### 第三章 日志框架【log4j】

#### 3.1 log4j作用

- 为相应框架添加日志功能【可以看到框架内部执行细节】

#### 3.2 log4j使用步骤

- 导入log4j的jar包

  ```xml
  <!--log4j-->
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

- 编写配置文件

  - 名称：log4j.xml

  - 位置：src/main/resources

  - 示例代码

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
    <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    
        <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
            <param name="Encoding" value="UTF-8" />
            <layout class="org.apache.log4j.PatternLayout">
                <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n" />
            </layout>
        </appender>
        <logger name="java.sql">
            <level value="debug" />
        </logger>
        <logger name="org.apache.ibatis">
            <level value="info" />
        </logger>
        <root>
            <level value="debug" />
            <appender-ref ref="STDOUT" />
        </root>
    </log4j:configuration>
    ```

### 第四章 Mybatis核心配置文件详解

#### 4.1 配置文件概述

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。
- 配置文件中包含一个根标签【configuration】，九个子标签【需要掌握其中6个子标签】

#### 4.2 Mybatis核心配置文件中标签的作用

- 根标签：configuration，主要作用就是包含子标签

- 子标签

  ![image-20220507141937174](02Mybatis.assets\image-20220507141937174.png)
  - properties子标签

    - 语法：\<properties resource="基于类路径引入外部属性文件">

    - 作用：定义property属性，也可以引入外部属性文件

    - 示例代码

      ```xml
      <!--    定义属性文件-->
      <!--    <properties>-->
      <!--        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>-->
      <!--    </properties>-->
      
      <!--    引入外部属性文件-->
      <properties resource="db.properties"></properties>
      ```

  - settings子标签

    - 语法：<settings>

    - 作用：这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的**运行时行为**。

    - 常用属性

      - **mapUnderscoreToCamelCase**：是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。【**注意字母必须全部相同才可以自动映射**】
        - false:默认值
        - true：开启驼峰式命名自动映射
      - cacheEnabled
      - lazyLoadingEnabled
      - useGeneratedKeys

    - 示例代码

      ```xml
      <settings>
          <setting name="mapUnderscoreToCamelCase" value="true"/>
      </settings>
      ```

  - typeAliases子标签

    - 语法：<typeAliases>

    - 作用：定义类型别名【类型别名可为 Java 类型（POJO）设置一个缩写名字。】

    - 示例代码

      ```xml
      <!-- 方式一：为POJO定义类型别名-->
         <typeAliases>
              <typeAlias type="com.atguigu.pojo.Employee" alias="employee"></typeAlias>
          </typeAliases>
      <!--方式二-->
          <typeAliases>
              <!--为当前包下所有的POJO定义别名【默认：将类名字母作为别名，不区分大小写，建议使用小写字母】-->
              <package name="com.atguigu.pojo"/>
          </typeAliases>
      ```
      
    - Mybatis内建的类型别名

      ![image-20220509141552583](02Mybatis.assets\image-20220509141552583.png)

      ![image-20220509141611087](02Mybatis.assets\image-20220509141611087.png)

  - environments子标签

    - 作用：设置数据库环境【多个】，最终会使用Spring管理数据源&事务管理器

    - 示例代码

      ```xml
      <!--设置数据库连接环境-->
          <environments default="development">
      
              <environment id="development">
                  <!-- 设置事务管理器 -->
                  <transactionManager type="JDBC"/>
                  <!--设置数据源-->
                  <dataSource type="POOLED">
      <!--                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>-->
      <!--                <property name="url" value="jdbc:mysql://localhost:3306/db220227?serverTimezone=UTC"/>-->
                      <property name="driver" value="${db.driverClassName}"/>
                      <property name="url" value="${db.url}"/>
                      <property name="username" value="${db.username}"/>
                      <property name="password" value="${db.password}"/>
                  </dataSource>
              </environment>
          </environments>
      ```

  - mappers子标签

    - 作用：设置映射文件路径【加载映射文件】

    - 示例代码

      ```xml
      <!--    设置映射文件路径-->
          <mappers>
              <mapper resource="mapper/EmployeeMapper.xml"/>
              <!--
                     * 设置加载映射文件的包名【加载当前包下的所有映射文件】
                     * 注意：要求映射文件的包名与接口的包名必须一致
              -->
      <!--        <package name="com.atguigu.mapper"/>-->
          </mappers>
      ```

- **总结**Mybatis核心配置文件【mybatis-config.xml】

  - 子标签有顺序要求的，参考顺序详情见官网

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <configuration>
  
      <!--    定义属性文件-->
      <!--    <properties>-->
      <!--        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>-->
      <!--    </properties>-->
  
      <!--    引入外部属性文件-->
      <properties resource="db.properties"></properties>
  
      <settings>
          <setting name="mapUnderscoreToCamelCase" value="true"/>
      </settings>
  
  <!--    为POJO定义类型别名-->
  <!--    <typeAliases>-->
  <!--        <typeAlias type="com.atguigu.pojo.Employee" alias="employee"></typeAlias>-->
  <!--    </typeAliases>-->
  
      <typeAliases>
          <!--为当前包下所有的POJO定义别名【默认：将类名字母作为别名，不区分大小写，建议使用小写字母】-->
          <package name="com.atguigu.pojo"/>
      </typeAliases>
  
  
      <!--设置数据库连接环境-->
      <environments default="development">
  
          <environment id="development">
              <!-- 设置事务管理器 -->
              <transactionManager type="JDBC"/>
              <!--设置数据源-->
              <dataSource type="POOLED">
  <!--                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>-->
  <!--                <property name="url" value="jdbc:mysql://localhost:3306/db220227?serverTimezone=UTC"/>-->
                  <property name="driver" value="${db.driverClassName}"/>
                  <property name="url" value="${db.url}"/>
                  <property name="username" value="${db.username}"/>
                  <property name="password" value="${db.password}"/>
              </dataSource>
          </environment>
      </environments>
      <!--    设置映射文件路径-->
      <mappers>
          <mapper resource="mapper/EmployeeMapper.xml"/>
          <!--
                 * 设置加载映射文件的包名【加载当前包下的所有映射文件】
                 * 注意：要求映射文件的包名与接口的包名必须一致
          -->
  <!--        <package name="com.atguigu.mapper"/>-->
      </mappers>
  </configuration>
  ```



### 第五章 Mybatis映射文件详解

#### 5.1 映射文件概述

- MyBatis 的真正强大在于它的语句映射，这是它的魔力所在。由于它的异常强大，映射器的 XML 文件就显得相对简单。
- 如果拿它跟具有相同功能的 JDBC 代码进行对比，你会立即发现省掉了**将近 95% 的代码**。
- MyBatis 致力于减少使用成本，让程序员能更专注于 SQL 代码。

#### 5.2 映射文件的详解

- 根标签【一个】

  - 语法：

    ```xml
    <mapper namespace="com.atguigu.mapper.EmployeeMapper"></mapper>
    ```

  - 作用：定义当前映射文件的namespace【设置目标接口】

- 子标签【共有9个，需要掌握8个】

  - insert：定义添加SQL语句

  - delete：定义删除SQL语句

  - update：定义修改SQL语句

  - select：定义查询SQL语句

    - 注意：查询语句必须定义resultType或resultMap，否则报如下错

      Caused by: org.apache.ibatis.executor.ExecutorException: A query was run and no Result Maps were found for the Mapped Statement 'com.atguigu.mapper.EmployeeMapper.selectAllEmp'.  **It's likely that neither a Result Type nor a Result Map was specified.**

  - sql：定义SQL片段

  - resultMap标签：自定义映射，resultType解决不了的问题，交个resultMap

  - cache：定义当前名称空间的缓存配置

  - cache-ref：定义当前名称空间的缓存引用

#### 5.3 子标签中常用的属性

- id属性：为SQL语句定义唯一标识

- resultType属性：定义结果集预期返回的类型【全类名（类的全限定名）或别名】
  - 注意
    - 如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。
    - resultType 和 resultMap 之间只能同时使用一个。



### day03 

### 第六章 获取主键自增&数据库受影响行数

#### 6.1 获取主键自增数值

- 语法：在insert标签中添加两个属性即可
  - useGeneratedKeys=true
  - keyProperty="对象id"

- 示例代码

  ```xml
  <insert id="insertEmployee" useGeneratedKeys="true" keyProperty="id">
      INSERT INTO
          tbl_employee (last_name,email,salary)
      VALUES
          (#{lastName},#{email},#{salary})
  </insert>
  ```

#### 6.2 获取数据库受影响行数

- 语法：将接口的返回值设置int或boolean即可

- 示例代码

  ```java
    /**
       * 删除员工信息
       * @param empId
       */
      public int deleteEmpById(int empId);
      public boolean deleteEmpById(int empId);
  ```

### 第七章 Mybatis中参数传递问题【重点】

> 参数传递概念：Mybatis中参数传递指的是，将接口中方法的参数，传递到映射文件的SQL语句中。

#### 7.1 单个普通类型参数

- 普通参数：包括基本类型及其包装类型+String等
- 语法规定：单个普通参数可以任意入参【任意参数名&任意参数类型】

#### 7.2 多个普通参数

- Mybatis底层会将多个参数封装Map中，Map的key是param1、param2、...或【arg0、arg1、...】

- 注意：获取多个普通参数值，通过Map的key获取。否则报如下错误

  Caused by: org.apache.ibatis.binding.BindingException: Parameter 'id' not found. Available parameters are [arg1, arg0, param1, param2]

#### 7.3 POJO参数【常用】

- Mybatis中支持POJO入参的，使用POJO的属性作为参数key
- 使用POJO参数时，应该使用POJO的属性获取参数值
- 适用场景：支持POJO就用POJO入参

#### 7.4 命名参数【常用】

- 语法：@Param注解，Mybatis中使用该注解为参数命名【定义名称】

  - @Param注解位置：参数前面
  - @Param注解属性：value属性【定义参数名称】
    - **注意：所有注解如只使用一个value属性时，value关键字可省略的**

- **概述：命名参数底层封装Map【ParamMap继承HashMap】**

- 命名参数，支持两种参数

  - 自己定义名称【id、lastName、...】
  - param1、param2、...

- 适用场景：不支持POJO&少量参数【参数<=2】

- **源码解析命名参数**

  ![image-20220509111550636](02Mybatis.assets\image-20220509111550636.png)

  ![image-20220509112806797](02Mybatis.assets\image-20220509112806797.png)

#### 7.5 Map参数【常用】

- Mybatis中支持Map参数，map的key就是参数key
- Map参数中：使用Map的key获取参数值
- 适用场景：不支持POJO&大量参数【参数>=3】

#### 7.6 其他参数【List|Array|Collection等】

- 会被MyBatis封装成一个map传入, Collection对应的key是collection,Array对应的key是array. 如果确定是List集合，key还可以是list。

### 第八章 Mybatis中#{}与${}区别【面试题】

#### 8.1 回顾Statement&PreparedStatement对象

- Statement：执行SQL语句命令对象，入参使用字符串拼接的方式，没有解决SQL注入安全隐患，相对不安全。
- PreparedStatement：执行SQL语句命令对象，入参使用预编译SQL方式【占位符】，解决SQL注入安全隐患，相对安全。

#### 8.2 #{}与$()区别

- #{}底层使用PreparedStatement对象，执行SQL语句命令对象，入参**使用预编译SQL方式**【占位符】，解决SQL注入安全隐患，相对安全。
- ${}底层使用Statement对象，执行SQL语句命令对象，入参**使用字符串拼接的方式**，没有解决SQL注入安全隐患，相对不安全。

#### 8.3 #{}和${}使用场景

- 回顾单条完整SQL语句，其中【？】就是占位符

  ```sql
  SELECT 
  	col,col2,... 
  FROM 
  	tableName 
  WHERE opr1=? 
  AND opr2=? 
  GROUP BY ? 
  ORDER BY ? 
  LIMIT ?,?
  ```

- #{}使用场景：原生SQL的【？】位置，在Mybatis中就可以使用【#{}】

  - #{}  == ?

- ${}使用场景：原生SQL的【？】解决不了问题，如需要参数时，使用${}

  - 最常用场景：**动态查询表名时使用${}**

    ![image-20220509114837835](02Mybatis.assets\image-20220509114837835.png)

  - 示例代码

    ```java
    /**
     * 测试【$使用场景】
     * 动态查询表名
     */
    public List<Employee> selectAllEmp(@Param("tableName") String tableName);
    ```

    ```xml
    <!--    测试动态表【$使用场景】-->
    <select id="selectAllEmp" resultType="employee">
        select
        	id,
        	last_name,
        	email,
        	salary
        from
        	${tableName}
    </select>
    ```

### 第九章 Mybatis中返回值四种结果

#### 9.1 查询单行数据返回单个对象

```java
public Employee selectEmpByEmpIdAndLastName(int id,String lastName);
```

```xml
<!--    通过员工id及员工lastName获取员工信息-->
    <select id="selectEmpByEmpIdAndLastName" resultType="employee">
        select
            id,
            last_name,
            email,
            salary
        from
            tbl_employee
        where
            id = #{arg0}
        and
            last_name = #{arg1}
    </select>
```

- resultType设置对象全类名或别名即可

#### 9.2 查询多行数据返回对象的集合

```java
/**
 * 测试【$使用场景】
 * 动态查询表名
 */
public List<Employee> selectAllEmp(@Param("tableName") String tableName);
```

```xml
<!--    测试动态表【$使用场景】-->
    <select id="selectAllEmp" resultType="employee">
        select
            id,
            last_name,
            email,
            salary
        from
            ${tableName}
    </select>
```

- resultType设置集合包含的类型

#### 9.3 查询单行数据返回Map

- 为什么返回Map？

  - 查询结果不支持对象，返回Map

    ```java
    /**
     * 查询单行数据，返回Map
     */
    public Map<String,Object> selectOneObjectReturnMap(int empId);
    ```

    ```xml
    <!--    查询单行数据，返回Map-->
    <select id="selectOneObjectReturnMap" resultType="map">
        select
            id,
            last_name,
            email,
            salary
        from
       	 	tbl_employee
        where
       	 	id = #{empId}
    </select>
    ```

  - 查询结果不期望返回对象，期望Map

    ```java
    /**
     * 查询结果不期望返回对象，期望Map
     * 查询单行数据，返回Map
     */
    public Map<String,Employee> selectOneObjectReturnMap2(int empId);
    ```

    ```xml
    <!--    查询单行数据，返回Map-->
        <select id="selectOneObjectReturnMap2" resultType="map">
            select
                id,
                last_name,
                email,
                salary
            from
                tbl_employee
            where
                id = #{empId}
        </select>
    ```

- **总结：查询单行数据返回Map，字段作为Map的key，查询结果作为Map的value**

#### 9.4 查询多行数据返回Map

- 查询多行数据支持List<Employee>，**但期望返回Map<Integer,Employee>**

```java
/**
 * 查询多行数据返回Map
 *      key:id
 *      value:employee
 * @return
 */
@MapKey("id")
public Map<Integer,Employee> selectAllEmpReturnMap();
```

```xml
<!-- 查询多行数据返回Map-->
<select id="selectAllEmpReturnMap" resultType="map">
    select
        id,
        last_name,
        email,
        salary
    from
        tbl_employee
</select>
```

- 注意：查询多行数据返回Map<Integer,Employee>，必须使用@MapKey注解标识key的属性名

### 第十章 Mybatis中自动映射与自定义映射【重难点】

> 回顾多表连接查询几种情况
>
> -- employee 多    dept 一 
> -- 在【多】的表中，添加【一】的id
>
> 1. 等值连接查询
>
>    SELECT 
>    	e.`id`,e.`last_name`,e.`email`,e.`salary`,
>    	d.`dept_id`,d.`dept_name`
>    FROM 
>    	tbl_employee e,tbl_dept d
>    WHERE 
>    	e.`dept_id`=d.`dept_id`
>
> 2. 内连接查询
>
>    SELECT 
>    	e.`id`,e.`last_name`,e.`email`,e.`salary`,
>    	d.`dept_id`,d.`dept_name`
>    FROM 
>    	tbl_employee e 
>    INNER JOIN 
>    	tbl_dept d
>    ON 
>    	e.`dept_id`=d.`dept_id`
>
> 3. 外连接查询
>
>    - 左外连接
>
>      SELECT 
>      	e.`id`,e.`last_name`,e.`email`,e.`salary`,
>      	d.`dept_id`,d.`dept_name`
>      FROM 
>      	tbl_employee e  LEFT JOIN tbl_dept d
>      ON 
>      	e.`dept_id`=d.`dept_id`
>
>    - 右外连接
>
>      SELECT 
>      	e.`id`,e.`last_name`,e.`email`,e.`salary`,
>      	d.`dept_id`,d.`dept_name`
>      FROM 
>      	tbl_employee e  RIGHT JOIN tbl_dept d
>      ON 
>      	e.`dept_id`=d.`dept_id`

#### 10.1 自动映射【resultType】与自定义映射【resultMap】概述

- 自动映射【resultType】：指的是将**表中的字段**与**类中的属性**自动建立映射关系

  - 注意：

    - 要求字段名与属性名一致，才可以自动映射。

    - 如属性名与字段名不一致，可以定义字段别名解决问题。

    - 如需将字段a_bc与aBc属性自动映射，开启驼峰式命名映射即可

      ```xml
      <settings>
          <setting name="mapUnderscoreToCamelCase" value="true"/>
      </settings>
      ```

  - 自动映射解决不了问题

    - 不期望定义别名&不支持驼峰式命名映射，自动映射解决不了
    - **多表连接查询时，结果集中返回多张表的数据，此时resultType解决不了**

- 自定义映射【resultMap】
  
  - 指定是程序员自己**定义表中的字段**与**类中的属性**关联关系
- 注意：resultType与resultMap只能同时使用一个
  
  - **使用心得：优先使用resultType解决，resultType解决不了，使用resultMap**

#### 10.2 自定义映射【级联映射（一对一）】

> 建立对象与对象之间关联关系

```java
/**
 * 自定义映射【级联映射】
 *      通过员工id获取员工信息，及员工所属的部门信息
 */
public Employee selectEmpAndDeptByEmpId(int empId);
```

```xml
<!--    自定义映射-级联映射-->
<resultMap id="empAndDeptRm" type="employee">
    <id property="id" column="id"></id>
    <result property="lastName" column="last_name"></result>
    <result property="email" column="email"></result>
    <result property="salary" column="salary"></result>
    <!--        级联映射-->
    <result property="dept.deptId" column="dept_id"></result>
    <result property="dept.deptName" column="dept_name"></result>
</resultMap>
<select id="selectEmpAndDeptByEmpId" resultMap="empAndDeptRm">
    SELECT
        e.`id`,
        e.`last_name`,
        e.`email`,
        e.`salary`,
        d.`dept_id`,
        d.`dept_name`
    FROM
        tbl_employee e,
        tbl_dept d
    WHERE
    	e.`dept_id` = d.`dept_id`
    AND
    	e.id = #{empId}
</select>
```

#### 10.3 自定义映射【association（一对一）】

```java
/**
 * 自定义映射【association】
 *      通过员工id获取员工信息，及员工所属的部门信息
 */
public Employee selectEmpAndDeptByEmpIdAssociation(int empId);
```

```xml
<!--    association映射-->
    <resultMap id="empAndDeptAssociationRm" type="employee">
        <!--        定义主键映射关系-->
        <id property="id" column="id"></id>
        <!--        定义非主键映射关系-->
        <result property="lastName" column="last_name"></result>
        <result property="email" column="email"></result>
        <result property="salary" column="salary"></result>
        <!--        association映射-->
        <association property="dept"
                     javaType="com.atguigu.pojo.Dept">
            <id property="deptId" column="dept_id"></id>
            <result property="deptName" column="dept_name"></result>
        </association>
    </resultMap>
    <select id="selectEmpAndDeptByEmpIdAssociation" resultMap="empAndDeptAssociationRm">
         SELECT
            e.`id`,
            e.`last_name`,
            e.`email`,
            e.`salary`,
            d.`dept_id`,
            d.`dept_name`
        FROM
            tbl_employee e,
            tbl_dept d
        WHERE
            e.`dept_id` = d.`dept_id`
        AND
            e.id = #{empId}
    </select>
```

### day04

- association分步查询

  - 概述：将多表【一步】连接查询，拆分成单表的多步查询

  - 优势：提高查询效率，降低服务器压力

  - 语法&示例代码

    ```java
    /**
     *  测试分步查询association
     *  通过员工id获取员工信息，及员工所属的部门信息
     *      1. 通过员工id，获取员工信息【包含部门id】
     *      2. 通过员工id获取部门id，从而通过部门id，获取部门信息
     */
    public Employee selectEmpAndDeptByEmpIdAssociationStep(int empId);
    ```

    ```xml
    <!--    day04-->
        <resultMap id="empAndDeptAssociationStepRm" type="employee">
            <id property="id" column="id"></id>
            <result property="lastName" column="last_name"></result>
            <result property="email" column="email"></result>
            <result property="salary" column="salary"></result>
            <!--        association分步查询-->
            <association property="dept"
                   select="com.atguigu.mapper.DeptMapper.selectDeptByDeptId"
                        column="dept_id">
            </association>
        </resultMap>
        <select id="selectEmpAndDeptByEmpIdAssociationStep" resultMap="empAndDeptAssociationStepRm">
            select
                id,
                last_name,
                email,
                salary,
                dept_id
            from
                tbl_employee
            where
                id = #{empId}
        </select>
    ```

    ```java
    /**
     * 通过部门id获取部门信息
     */
    public Dept selectDeptByDeptId(int deptId);
    ```

    ```xml
    <select id="selectDeptByDeptId" resultType="dept">
        select
            dept_id,
            dept_name
        from
            tbl_dept
        where
            dept_id=#{deptId}
    </select>
    ```

- association延迟加载

#### 10.4 自定义映射【collection（一对多）】

- 基本语法

  ```java
  /**
   * 通过部门id获取部门信息，及部门对应的员工信息
   */
  public Dept selectDeptAndEmpByDeptId(int deptId);
  ```

  ```xml
  <!--    通过部门id获取部门信息，及部门对应的员工信息-->
      <resultMap id="deptAndEmpRm" type="dept">
          <id property="deptId" column="dept_id"></id>
          <result property="deptName" column="dept_name"></result>
          <!-- collection解决一对多问题-->
          <collection property="empList"
                      ofType="com.atguigu.pojo.Employee">
              <id property="id" column="id"></id>
              <result property="lastName" column="last_name"></result>
              <result property="email" column="email"></result>
              <result property="salary" column="salary"></result>
          </collection>
      </resultMap>
      <select id="selectDeptAndEmpByDeptId" resultMap="deptAndEmpRm">
          SELECT
              e.`id`,
              e.`last_name`,
              e.`email`,
              e.`salary`,
              d.`dept_id`,
              d.`dept_name`
          FROM
              tbl_employee e,
              tbl_dept d
          WHERE
              e.`dept_id` = d.`dept_id`
          AND
              d.dept_id = #{deptId}
      </select>
  ```

- collection分步查询

  ```java
  /**
   * collection分步查询
   *      通过部门id获取部门信息，及部门对应的员工信息
              1. 通过部门id获取部门信息【tbl_dept】
              2. 通过部门id获取员工信息【tbl_employee】
   */
  public Dept selectDeptAndEmpByDeptIdStep(int deptId);
  ```

  ```xml
  <!--    collection分步查询-->
      <resultMap id="deptAndEmpStepRm" type="dept">
          <id property="deptId" column="dept_id"></id>
          <result property="deptName" column="dept_name"></result>
          <collection property="empList"
                      select="com.atguigu.mapper.EmployeeMapper.selectEmpByDeptId"
                      column="dept_id"
                      fetchType="lazy">
  
          </collection>
      </resultMap>
      <select id="selectDeptAndEmpByDeptIdStep" resultMap="deptAndEmpStepRm">
          select
              dept_id,
              dept_name
          from
              tbl_dept
          where
              dept_id = #{deptId}
      </select>
  ```

  ```java
  /**
   * 通过部门id获取员工信息
   */
  public List<Employee> selectEmpByDeptId(int deptId);
  ```

  ```xml
  <!--    通过部门id获取员工信息-->
  <select id="selectEmpByDeptId" resultType="employee">
      select
          id,
          last_name,
          email,
          salary,
          dept_id
      from
          tbl_employee
      where
          dept_id = #{deptId}
  </select>
  ```

#### 10.5 resultMap标签详解

- 属性
  - id属性：定义resultMap唯一标识
  - type属性：定义resultMap类型【类似与resultType作用】

- 子标签

  - id子标签：自定义**主键字段与属性**关联关系
    - property属性：设置属性名称【对象中】
    - column属性：设置字段名称【表中】
  - result子标签：自定义**非主键字段与属性**关联关系
    - property属性：设置属性名称【对象中】
    - column属性：设置字段名称【表中】
  - association子标签：自定义一对一关联关系
    - 属性
      - property属性：设置【关联】属性名称
      - javaType属性：设置属性Java类型【全类名或别名】
      - select属性：定义分步查询SQL的ID
      - column属性：定义分步查询SQID的参数
      - fetchType属性：定义局部延迟加载
    - 子标签
      - id子标签：自定义**主键字段与属性**关联关系
      - result子标签：自定义**非主键字段与属性**关联关系
  - collection子标签：自定义一对多关联关系
    - 属性
      - property属性：设置【关联】属性名称
      - ofType属性：设置集合属性的泛型类型【全类名或别名】
      - select属性：定义分步查询SQL的ID
      - column属性：定义分步查询SQID的参数
      - fetchType属性：定义局部延迟加载
    - 子标签
      - id子标签：自定义**主键字段与属性**关联关系
      - result子标签：自定义**非主键字段与属性**关联关系


### 第十一章 Mybatis中延迟加载【懒加载】

#### 11.1 延迟加载使用场景

- 前提：在分步查询的基础上实现延迟加载
- 概述：使用当前数据时加载，不使用当前数据时，暂不加载

#### 11.2 延迟加载语法

- 全局设置【settings中】

  ```xml
  <!--  开启延迟加载【默认值：false】-->
  <setting name="lazyLoadingEnabled" value="true"/>
  <!--  开启时，任一方法的调用都会加载该对象的所有延迟加载属性。
                  否则，每个延迟加载属性会按需加载
                  默认值:false【在 3.4.1 及之前的版本中默认为 true】
              -->
  <setting name="aggressiveLazyLoading" value="false"/>
  ```

- 局部设置【分步查询association或collection添加fetchType属性】

  - fetchType属性
    - lazy：开启局部懒加载
    - eager：关闭局部懒加载

  ```xml
  <association property="dept"
              select="com.atguigu.mapper.DeptMapper.selectDeptByDeptId"
              column="dept_id"
              fetchType="eager">
  </association>
  <collection property="empList"
                  select="com.atguigu.mapper.EmployeeMapper.selectEmpByDeptId"
                      column="dept_id"
                      fetchType="lazy">
          </collection>
  ```

#### 11.3 延迟加载扩展

- 如分步查询时，接口中需要多个参数时怎么办？

  - 将多个参数封装Map结构即可
  - 语法：{k1=v1,k2=v2,...}

- 示例代码

  ```xml
  <resultMap id="deptAndEmpStepRm" type="dept">
      <id property="deptId" column="dept_id"></id>
      <result property="deptName" column="dept_name"></result>
      <collection property="empList"
                  select="com.atguigu.mapper.EmployeeMapper.selectEmpByDeptId"
                  column="{dId=dept_id}"
                  fetchType="lazy">
  
      </collection>
  </resultMap>
  <select id="selectDeptAndEmpByDeptIdStep" resultMap="deptAndEmpStepRm">
      select
          dept_id,
          dept_name
      from
          tbl_dept
      where
          dept_id = #{deptId}
  </select>
  ```

  ```xml
  <select id="selectEmpByDeptId" resultType="employee">
      select
          id,
          last_name,
          email,
          salary,
          dept_id
      from
          tbl_employee
      where
          dept_id = #{dId}
  </select>
  ```

### 第十二章 Mybatis中动态SQL【重要】

#### 12.1 动态SQL概述

- 动态SQL：指定是SQL语句可动态变化
  - 类似页面
    - 静态页面：html【thymeleaf动态渲染数据】
    - 动态页面：jsp
- Mybatis中特点
  - Mybatis的接口中【不支持】方法重载
    - 因为：接口中方法名与SQL的id一致，而SQL的id不能重复
  - Mybatis映射文件中注释
    - xml注释【推荐使用】：<!-- 注释内容 -->
    - SQL注释【不推荐使用】：--  注释内容
  - Mybatis动态SQL中支持：OGNL表达式语言
    - OGNL（ Object Graph Navigation Language ）对象图导航语言
    - 这是一种强大的表达式语言，通过它可以非常方便的来操作对象属性。

#### 12.2 动态SQL常用标签

- if标签：应用于SQL中的基本if判断

- where标签：解决where关键字及条件中**多出的前面第一个and或or关键字**问题

  ```xml
  <!--    测试动态SQL-->
  <select id="selectEmpByDynamicOpr" resultType="employee">
      select
          id,
          last_name,
          email,
          salary,
          dept_id
      from
       tbl_employee
      <where>
          <if test="id != null">
               id = #{id}
          </if>
          <if test="lastName != null">
              and last_name = #{lastName}
          </if>
          <if test="email != null">
              and email = #{email}
          </if>
          <if test="salary != null">
              and salary = #{salary}
          </if>
      </where>
  </select>
  ```

- trim标签：可以在条件判断完的SQL语句**前后添加或者去掉指定的字符**

  - prefix: 添加前缀
  - prefixOverrides: 去掉前缀
  - suffix: 添加后缀
  - suffixOverrides: 去掉后缀

  ```xml
  <!--    测试trim-->
      <select id="selectEmpByDynamicTrim" resultType="employee">
          select
              id,
              last_name,
              email,
              salary,
              dept_id
          from
              tbl_employee
          <trim prefix="where" suffixOverrides="and|or">
              <if test="id != null">
                  id = #{id} and
              </if>
              <if test="lastName != null">
                  last_name = #{lastName} and
              </if>
              <if test="email != null">
                  email = #{email} and
              </if>
              <if test="salary!=null">
                  salary = #{salary}
              </if>
          </trim>
      </select>
  ```

- choose 主要是用于分支判断，类似于java中的switch case,只会满足所有分支中的一个

  ```xml
  <!--    测试choose-->
      <select id="selectEmpByDynamicChoose" resultType="employee">
          select
              id,
              last_name,
              email,
              salary,
              dept_id
          from
              tbl_employee
          <where>
              <choose>
                  <when test="id!=null">
                      id = #{id}
                  </when>
                  <when test="lastName!=null">
                      last_name = #{lastName}
                  </when>
                  <when test="email!=null">
                      email = #{email}
                  </when>
                  <when test="salary!=null">
                      salary = #{salary}
                  </when>
                  <otherwise>
                      1=1
                  </otherwise>
              </choose>
          </where>
      </select>
  ```

- set标签：主要是用于解决修改操作中SQL语句中可能**多出一个逗号**的问题

  ```xml
  <!--    修改员工信息-->
      <update id="updateEmp">
          update
              tbl_employee
          <set>
              <if test="lastName!=null">
                  last_name = #{lastName},
              </if>
              <if test="email">
                  email = #{email},
              </if>
              <if test="salary!=null">
                  salary = #{salary}
              </if>
          </set>
          where
              id = #{id}
      </update>
  ```

- foreach标签：

  ```xml
  <!--    测试foreach标签-->
      <select id="selectEmpByIds" resultType="employee">
          select
              id,
              last_name,
              email,
              salary
          from
              tbl_employee
          where
              id in
                  <foreach open="(" close=")" collection="ids" item="id" separator=",">
                      #{id}
                  </foreach>
      </select>
  ```

- sql标签：定义SQL语句片段

  - 语法

    - sql标签定义SQL片段
    - 使用include标签引入SQL片段

  - 示例代码

    ```xml
    <!--    定义SQL片段【员工表，主信息】-->
        <sql id="sql_column_employee">
            id,
            last_name,
            email,
            salary
        </sql>
        
    <!--    定义SQL片段【查询员工表】-->
        <sql id="sql_from_employee">
            select
                id,
                last_name,
                email,
                salary,
                dept_id
            from
                tbl_employee
        </sql>
        <!--    测试动态SQL-->
        <select id="selectEmpByDynamicOpr" resultType="employee">
            <include refid="sql_from_employee"></include>
            <where>
                <if test="id != null">
                     id = #{id}
                </if>
                <if test="lastName != null">
                    and last_name = #{lastName}
                </if>
                <if test="email != null">
                    and email = #{email}
                </if>
                <if test="salary != null">
                    and salary = #{salary}
                </if>
            </where>
        </select>
    <!--    测试foreach标签-->
        <select id="selectEmpByIds" resultType="employee">
            select
                <include refid="sql_column_employee"></include>
            from
                tbl_employee
            where
                id in
                    <foreach open="(" close=")" collection="ids" item="id" separator=",">
                        #{id}
                    </foreach>
        </select>
    ```



### 第十三章 Mybatis中缓存机制

#### 13.1 缓存概述

- 生活中缓存
  - 缓存音频、视频
  - 优势：节约数据流量、提高效率
- Mybatis程序中缓存【提高程序性能，降低服务器压力】
  - 一级缓存：本地缓存【SqlSession级别缓存】
  - 二级缓存：second level cache【全局作用域缓存（SqlSessionFactory级别缓存）】
  - 第三方缓存：EhChach缓存框架

#### 13.2 Mybatis中一级缓存

- 概述：一级缓存也叫本地缓存，作用域：SqlSession【sqlSession级别缓存】

- 特点：

  - 一级缓存默认开启，不能被关闭
  - 一级缓存可以清空

- 缓存原理

  - 第一次获取数据时，先从数据中获取数据，并将数据缓存至一级缓存中
    - 一级缓存机制：底层就是一个Map，Map的key：【 hashCode+查询的SqlId+编写的sql查询语句+参数】
  - 以后再次获取相同数据时，先从一级缓存中获取，**如在一级缓存中未获取到所需数据，再从数据库中获取。**

- 一级缓存五种失效情况

  1) 不同的SqlSession对应不同的一级缓存

  2) 同一个SqlSession但是查询条件不同

  3) 同一个SqlSession两次查询期间执行了任何一次增删改操作

  - 执行增删改操作，会默认情况缓存

  4) 同一个SqlSession两次查询期间手动清空了缓存

  - sqlSession.clearCache();

  5) 同一个SqlSession两次查询期间提交了事务

  - sqlSession.commit();

### day05

#### 13.3 Mybatis中二级缓存

- 二级缓存概述

  - second level cache【全局作用域缓存（sqlSessionFactory）】
  - 作用域：sqlSessionFactory

- 二级缓存特点

  - 二级缓存默认关闭的，需要手动开启才可以使用
  - 二级缓存需要提交sqlSession或关闭sqlSession时，才会缓存数据

- 二级缓存使用步骤

  ① 全局配置文件中开启二级缓存<setting name="cacheEnabled" value="true"/>

  ② 需要使用二级缓存的映射文件处使用cache配置缓存<cache />

  ③ 注意：POJO需要实现Serializable接口

  ④ 关闭sqlSession或提交sqlSession时，将数据缓存到二级缓存

- 二级缓存，缓存机制

  - 第一次获取数据时，从数据库获取数据，将数据缓存至一级缓存，当提交sqlSession或关闭sqlSession时，将数据缓存至二级缓存。
  - 以后再次获取相同数据时，先从一级缓存中获取数据，如一级缓存中未获取到数据，再从二级缓存中获取，如二级缓存也未获取到数据，需要去数据库中获取数据。

- 二级缓存相关属性

  - eviction：设置缓存回收策略
    - LRU：最近最少使用的，先移除
    - FIFO：先进先出，按照对象进入顺序，去移除对象
  - flushInternal：设置刷新间隔，单位毫秒
  - size：设置缓存数量
  - readOnly：设置缓存是否为只读缓存【设置只读，就不能进行增删改操作】

- 二级缓存失效情况

  - 两次查询期间，执行任意一次增删改操作
  - 执行sqlSession.clearCache()只清空一级缓存，**不会清空二级缓存**

#### 13.4 Mybatis中第三方缓存【EhCache】

- EhCache概述

  - EhCache是缓存插件
  - EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider

- EhCache使用步骤

  1. 导入jar包

  2. 编写配置文件

     - 名称：ehcache.xml
     - 位置：src/main/resources

  3. 在映射文件中，加载第三方缓存插件

     ```xml
     <cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>
     ```

- EhCache注意事项
  - EhCache需要开启二级缓存，才能使用【EhCache需要在二级缓存基础上使用】
  - 设置二级缓存失效，第三方EhCache缓存也会失效

### 第十四章 Mybatis逆向工程

#### 14.1 逆向工程概述

- 正向工程：通过应用程序【java代码】，影响数据库表中的数据【java->表】
- 逆向工程：通过表结构，影响【生成】应用程序

#### 14.2 逆向工程-MBG

- MBG全称：Mybatis Generator就是代码生成器
- 可以快速通过表结构，生成对应代码【POJO&PojoMapper&PojoMapper.xml】
- 但是表连接、存储过程等这些复杂sql的定义需要我们手工编写
- MBG支持两种风格CURD
  - 普通CRUD
  - QBC风格CRUD
- 文档地址：http://www.mybatis.org/generator/

#### 14.3 MBG基本使用

- 导入jar包

  ```xml
  <!-- mybatis-generator-core -->
  <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-core</artifactId>
      <version>1.3.6</version>
  </dependency>
  <!-- mysql-connector-java -->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.37</version>
      <!--            <version>8.0.26</version>-->
  </dependency>
  <!--导入MyBatis的jar包-->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
  </dependency>
  <!--junit-->
  <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
  </dependency>
  
  <!--log4j-->
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

- 编写配置文件

  - 名称：mbg.xml

  - 位置：src/main/resources

  - 示例代码

    ```xml
    <!DOCTYPE generatorConfiguration PUBLIC
            "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
    <generatorConfiguration>
        <!--
           id属性：设置一个唯一标识
           targetRuntime属性值说明：
             MyBatis3Simple：基本的增删改查
             MyBatis3：带条件查询的增删改查
        -->
        <context id="simple" targetRuntime="MyBatis3Simple">
            <!--设置连接数据库的相关信息-->
            <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                            connectionURL="jdbc:mysql://localhost:3306/mybatis"
                            userId="root"
                            password="root"/>
    
            <!--设置JavaBean的生成策略-->
            <javaModelGenerator targetPackage="com.atguigu.mybatis.mbg.beans" targetProject="src/main/java"/>
    
            <!--设置SQL映射文件的生成策略-->
            <sqlMapGenerator targetPackage="com.atguigu.mybatis.mbg.mapper" targetProject="src/main/resources"/>
    
            <!--设置Mapper接口的生成策略-->
            <javaClientGenerator type="XMLMAPPER" targetPackage="com.atguigu.mybatis.mbg.mapper" targetProject="src/main/java"/>
    
            <!--逆向分析的表-->
            <table tableName="employees" domainObjectName="Employee"/>
            <table tableName="departments" domainObjectName="Department"/>
        </context>
    </generatorConfiguration>
    ```

- 执行代码生成器，生成相关代码

  ```java
  @Test
  public void testMBG() throws IOException, XMLParserException, InvalidConfigurationException, SQLException, InterruptedException {
      List<String> warnings = new ArrayList<String>();
      boolean overwrite = true;
      File configFile = new File("src/main/resources/mbg.xml");
      ConfigurationParser cp = new ConfigurationParser(warnings);
      Configuration config = cp.parseConfiguration(configFile);
      DefaultShellCallback callback = new DefaultShellCallback(overwrite);
      MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
      myBatisGenerator.generate(null);
  }
  ```

  

### 第十五章 Mybatis分页插件【PageHelper】

#### 15.1 分页功能基本概述

- 为什么需要分页

  - 提高用户体验度
  - 降低服务器压力

- 设计分页Page类

  ![image-20220511115006632](02Mybatis.assets\image-20220511115006632.png)

  - 属性：
    - pageNum：当前页码【用户行为】
    - pages：总页数【计算：总页数=总数据数量/每页显示数量】
    - total：总数据数量【查询数据库获取】
    - pageSize：每页显示数量
    - List<T>：当前页显示数据集合【查询数据库获取】
  - 使用**业务层【service】**实现分页功能
  - 使用**业务bean**实现分页功能

#### 15.2 PageHelper概述

- PageHelper：分页插件

- 官方文档：

  https://github.com/pagehelper/Mybatis-PageHelper/blob/master/README_zh.md

#### 15.3 PageHelper应用

- 使用步骤

  1. 导入jar包

     ```xml
     <!-- pagehelper -->
     <dependency>
         <groupId>com.github.pagehelper</groupId>
         <artifactId>pagehelper</artifactId>
         <version>5.0.0</version>
     </dependency>
     ```

  2. 在mybatis-config.xml配置文件中添加插件

     ```xml
     <plugins>
         <!--配置PageHelper分页插件-->
         <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
     </plugins>
     ```

  3. 使用

     - 在查询之前，开启分页功能

       - PageHelper.startPage(int pageNum,int pageSize)
         - pageNum：当前页码
         - pageSize：每页显示数据数量

     - 在查询之后，将结果封装PageInfo中

       - PageInfo<T> pageInfo = new PageInfo<T>(list,index)

         - index：设置页码逻辑

         ```java
         /*
             假设:页码=8【index=5】
                 [1] 2 3 4 5
                 1 [2] 3 4 5
                 1 2 [3] 4 5
         
                 2 3 [4] 5 6
                 3 4 [5] 6 7
                 4 5 [6] 7 8
         
                 4 5 6 [7] 8
                 4 5 6 7 [8]
         * */
         ```

#### 15.4 Page与PageInfo

- Page业务bean

  ```java
   //开启分页
  Page<Employee> page = PageHelper.startPage(2, 3);
  //执行查询语句
  List<Employee> employees = mapper.selectByExample(ee);
  // - pageNum：当前页码【用户行为】
  int pageNum = page.getPageNum();
  System.out.println("pageNum = " + pageNum);
  // - pages：总页数【计算：总页数=总数据数量/每页显示数量】
  int pages = page.getPages();
  System.out.println("pages = " + pages);
  // - total：总数据数量【查询数据库获取】
  long total = page.getTotal();
  System.out.println("total = " + total);
  //- pageSize：每页显示数量
  int pageSize = page.getPageSize();
  System.out.println("pageSize = " + pageSize);
  // - List<T>：当前页显示数据集合【查询数据库获取】
  List<Employee> result = page.getResult();
  
  for (Employee employee : page.getResult()) {
      System.out.println("employee = " + employee);
  }
  ```

- PageInfo业务bean

  ```java
  //开启分页
  Page<Employee> page = PageHelper.startPage(1, 5);
  //执行查询语句
  List<Employee> employees = mapper.selectByExample(ee);
  //测试PageInfo
  PageInfo<Employee> pageInfo = new PageInfo<>(employees,5);
  //当前页码【用户行为】
  int pageNum = pageInfo.getPageNum();
  System.out.println("pageNum = " + pageNum);
  //总页数【计算：总页数=总数据数量/每页显示数量】
  int pages = pageInfo.getPages();
  System.out.println("pages = " + pages);
  //总数据数量【查询数据库获取】
  long total = pageInfo.getTotal();
  System.out.println("total = " + total);
  //每页显示数量
  int pageSize = pageInfo.getPageSize();
  System.out.println("pageSize = " + pageSize);
  //当前页显示数据集合【查询数据库获取】
  for (Employee employee : pageInfo.getList()) {
      System.out.println("employee = " + employee);
  }
  
  System.out.println("总记录数是："+pageInfo.getTotal());
  System.out.println("是否有上一页："+pageInfo.isHasPreviousPage());
  System.out.println("上一页是："+pageInfo.getPrePage());
  System.out.println("是否有下一页："+pageInfo.isHasNextPage());
  System.out.println("下一页是："+pageInfo.getNextPage());
  System.out.println("是否是第一页："+pageInfo.isIsFirstPage());
  System.out.println("是否是最后一页："+pageInfo.isIsLastPage());
  
  System.out.println("页码信息：");
  int[] navigatepageNums = pageInfo.getNavigatepageNums();
  for (int navigatepageNum : navigatepageNums) {
      System.out.print(navigatepageNum+"  ");
  }
  ```





























##                                                                回顾&预习



























#                                 练习时间：15分钟

练习内容：15.4 PageInfo应用



















# 休息时间：10分







