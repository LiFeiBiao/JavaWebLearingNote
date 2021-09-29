# Spring

#### Spring简介

1. **是什么**
   
   1. Spring是分层的Java SE/EE应用**full-stack轻量级开源框架**,以**loc ( Inverse Of Control :反转控制)**和**AOP ( Aspect Oriented Programming :面向切面编程)**为内核。
   2. 提供了**展现层SpringMVC**和**持久层Spring JDBCTemplate**以及**业务层事务管理**等众多的企业级应用技术,还能整合开源世界众多著名的第三方框架和类库,逐渐成为使用最多的Java EE企业应用开源框架。 
   
2. 发展：略过

3. **优势**：

   1. **方便解耦，简化开发**

      通过Spring提供的loC容器,可以将对象间的依赖关系交由Spring进行控制,避免硬编码所造成的过度耦合。用户也不必再为单例模式类、属性文件解析等这些很底层的需求编写代码,可以更专注于上层的应用。

   2. **AOP编程的支持**

      ,通过Spring的AOP功能,方便进行面向切面编程,许多不容易用传统OOP实现的功能可以通过AOP轻松实现。 

   3. **声明式事务的支持**

      可以将我们从单调烦闷的事务管理代码中解脱出来,通过声明式方式灵活的进行事务管理,提高开发效率和质量。

   4. **方便程序的测试**

      可以用非容器依赖的编程方式进行几乎所有的测试工作,测试不再是昂贵的操作,而是随手可做的事情

   5. **方便集成各种优秀框架**

      Spring对各种优秀框架(Struts, Hibemnate, Hessian, Quart等)的支持。

   6. **降低JavaEEAPI的使用难度**

      Spring对JavaEE API (如JDBC、JavaMail、远程调用等)进行了薄薄的封装层,使这些API的使用难度大为降低。

   7. **Java源码是经典学习范例**

      Spring的源代码设计精妙、结构清晰、匠心独用,处处体现着大师对Java设计模式灵活运用以及对Java技术的高深造诣。它的源代码无意是Java技术的最佳实践的范例。

4. **体系结构**

   1. ![image-20210908085608520](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210908085608520.png)

#### 快速入门

1. **Spring程序开发步骤**

   ![image-20210908090013944](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210908090013944.png)

   ①导入Spring开发的基本包**坐标**

   ②编写**Dao接口和实现类**

   ③创建Spring核心**配置文件**

   ④在Spring配置文件中配置UserDaolmpl

   ⑤使用Spring的API获得Bean实例 

#### 	拓展

- ​	SpringBoot
  - 一个快速开发的脚手架
  - 基于SpringBoot可以快速的开发单个微服务
  - 约定大于配置
- Spring'Cloud
  - 是基于SpringBoot实现的
- 大多数公司都是基于SpringBoot进行快速开发，学习SpringBoot的前提是完全掌握Spring和SpringMVC，承上启下的作用



#### 配置文件

1. Bean标签基本配置

2. Bean标签范围配置

   1. scope：指对象的作用范围。取值如下：

      ![image-20210908094602672](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210908094602672.png)

   2. 



#### IOC

1. 创建对象的方式

   1. 使用无参构造创建对象，默认

   2. 假设需要有参构造函数创建对象

      1. 下标赋值

         ```
         <bean id="user" class="com.lfb.pojo.User">
             <constructor-arg index="0" value="李飞彪"/>
         </bean>
         ```

      2. 类型，不建议使用

         ```
         <bean id="user" class="com.lfb.pojo.User">
             <constructor-arg type="java.lang.String" value="李飞彪leixing"/>
         </bean>
         ```

      3. 参数名

         ```
         <bean id="user" class="com.lfb.pojo.User">
             <constructor-arg name="name" value="李飞彪canshuming"/>
         </bean>
         ```

   3. ​	在配置文件加载的时候，容器中管理的对象就已经初始化了

2. spring配置

   1. 别名**alias**
   
      ```
      <!--添加了别名，也可以使用别名获取到这个对象-->
          <alias name="user" alias="user2"/>
      ```
   
   2. Bean的配置
   
      ```
      id:唯一标识符，相当于对象名
      class：bean对象所对应的全限定名：包名+类型
      name:也是别名，可以同时取多个别名
      
      <bean id="usert" class="com.lfb.pojo.UserT">
          <constructor-arg name="name" value="李飞彪canshuming"/>
      </bean>
      ```
   
   3. import配置
   
      一般用于团队开发使用，可以将多个配置文件，导入合并为一个，将不同的beans.xml合并为一个总的applicationContext.xml

#### 依赖注入

1. ##### 构造器注入

2. ##### se't方式注入

   1. 依赖注入：set注入
      1. 依赖：bean对象的创建依赖于容器
      2. 注入：bean对象中的所有属性由容器注入

3. **扩展方式注入**需要导入约束

   1. p命名空间注入，可以直接注入属性的值：property

   1. c命名空间注入，可以通过构造器注入：property

4. **bean的作用域**

   ![image-20210910202038143](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210910202038143.png)

   1. singleton单例模式（spring默认机制）
   2. prototype原型模式：每次从容器中get时，都会产生一个新对象

#### bean自动装配

1. 自动装配是spring满足bean依赖一种方式

2. spring在上下文中自动寻找，并自动给bean装配属性

3. spring中三种装配的方式

   1. 在xml中显式的配置

      autowire="byname":会自动在容器上下文中查找，和自己对象se't方法后面的值对应的bean id;需要包证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致

      autowire="type"会自动在容器上下文中查找，和自己对象属性类型相同的bean；需要包证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一样

   2. 在Java中显式的配置

   3. 隐式的自动装配bean[重要]

4. 使用注解实现自动装配

   1. jdk1.5支持的注解，spring2.5支持注解

   2. 使用注解须知

      1. 导入约束：context约束

      2. 配置注解的支持<context:annotation-config/>

      3. ```
         <?xml version="1.0" encoding="UTF-8"?>
         <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
                 http://www.springframework.org/schema/beans/spring-beans.xsd
                 http://www.springframework.org/schema/context
                 http://www.springframework.org/schema/context/spring-context.xsd">
         
             <context:annotation-config/>
         
         </beans>
         ```

      4. @Autowired直接在属性上使用即可也可以在set方式上使用

         使用Autowired我们可以**不用编写Set方法**了,前提是你**这个自动装配的属性在1oC (Spring)容器中存在,且符合名字byname!**

         如果@Autowired自动装配的环境比较复杂,自动装配无法通过一个注解【@Autowired】完成的时候、我们可以使用@Qualifier(value="xxx")配置@Autowired的使用,指定一个唯一的bean对象注入! 

      5. @resource（name="")注解

      6. **@Autowired和@resource**的区别

         - 都是用来自动装配的，都可以放到属性字段上
         - @Autowired通过byname的方式实现，而且必须要求这个对象存在【常用】
         - @Resource默认通过byname的方式实现,如果找不到名字,则通过byType实现! 如果两个都找不到则报错
         - 执行顺序不同：@ Autowired通过byType的方式实现.@ Resource默认通过byname的方式实现。

#### 使用注解开发

在spring4之后，要使用注解开发，必须要包证aop的包导入

使用注解需要导入context约束，增加注解的支持

```
@Component//组件放在类上，说明这个类被spring管理了，就是bean
```

1. bean

2. 属性如何注入

   ```
   @Component
   public class User {
   
       public String name;
       //相当于<property name="name" value = "lifeibiao"/>
       @Value("李飞彪")
       public void setName(String name) {
           this.name = name;
       }
   }
   ```

3. **衍生的注解**

   @Component有几个衍生注解,我们在web开发中,会按照mvc三层架构分层!

   dao 【@Repositoryo】

   service 【@Service】

   controller 【@controller】

   这四个注解功能都是一样的,都是代表将某个类注册到Spring中,装配Bean 

4. **自动装配置**

   **@Autowired** :自动装配通过类型。名字

   如果Autowired不能唯一自动装配上属性,则需要通过@Qualifier(value="xx*")

   **@Nu1lable** 字段标记了这个注解,说明这个字段可以为null;

   **@Resource** ：自动装配通过名字。类型。 

5. 作用域

6. 小结

   xml和注解：

   - xml更加万能，适用于任何场合，维护简单方便
   - 注解不是自己类使用不了，维护相对复杂
   - xml和注解的最佳实践：
     - 注解只负责完成属性的注入
     - 使用过程中，只需要注意一个问题：必须让注解生效，就需要开启注解的支持

#### 使用Java的方式配置spring

现在完全不使用spring的xml配置了，全权交给Java来做，

JavaConfig是spring的一个子项目，在spring4之后成为一个核心功能 



1. ```
   AnnotationConfigApplicationContext
   ```

1. ```java
   //配置文件
   package com.lfb.config;
   
   import com.lfb.pojo.User;
   import org.springframework.beans.factory.annotation.Configurable;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.Import;
   
   @Configuration
   @ComponentScan("com.lfb.pojo")
   @Import(LfbConfig.class)
   //这个也会被spring容器托管，注册到容器中，因为本来就是一个@Component
   //@Configuration代表这是一个配置类，和之前的bean.xml是一样的
   public class LfbConfig {
       //注册一个bean，相当于之前写的一个bean标签
       //这个方法的名字就相当于bean标签中的id属性
       //这个方法的返回值就相当于bean标签中的class属性
       @Bean
       public User getUser(){
           return new User();//返回要注入bean的对象
       }
   }
   
   ```

   ```java
   
   //测试类
   import com.lfb.config.LfbConfig;
   import com.lfb.pojo.User;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.annotation.AnnotationConfigApplicationContext;
   
   public class MyTest {
       public static void main(String[] args) {
           //如果完全使用了配置类方法去做，只能通过AnnotationConfig上下文获取容器，通过配置类的class对象加载
           ApplicationContext context = new AnnotationConfigApplicationContext(LfbConfig.class);
           User user = (User)context.getBean("getUser");
           System.out.println(user.getName());
       }
   }
   ```

   

   实体类

   ```java
   package com.lfb.pojo;
   
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.stereotype.Component;
   
   import javax.xml.bind.annotation.XmlValue;
   //这个注解说明这个类被spring接管了，注册到了容器中
   @Component
   public class User {
       private String name;
   
       public String getName() {
           return name;
       }
       @Value("lifeibiao")//属性注入值
       public void setName(String name) {
           this.name = name;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "name='" + name + '\'' +
                   '}';
       }
   }
   ```

2. 这种纯Java的配置方式，在spring boot中随处可见

#### 代理模式

是SpringAOP的底层【SpringAOP和SpringMVC】

单例模式的分类：

- 静态代理

  - 角色分析

    - 抽象角色：一般会使用接口或者抽象类来解决
    - 真实角色：被代理的角色
    - 代理角色：代理真实角色，代理真实角色后，一般会做一些附属操作
    - 客户：访问代理对象的人

    代理模式的好处：

    - 可以使真正角色的操作更加纯粹，不用去关注一些公共的业务
    - 公共交给代理角色，实现业务的分工
    -  公共业务发生扩展的时候，方便集中管理

    缺点：

    - 一个真实角色就会产生一个代理角色，代码量会翻倍-开发效率会变低。

- 动态代理

  - 角色分析：与静态代理一样
  - 动态代理的代理类是动态生成的,不是我们直接写好的!
  - 动态代理分为两大类:基于**接口**的动态代理,基于**类**的动态代理。
    - 基于接口一JDK动态代理【我们在这里使用】
    - 基于类: cglib
    - java字节码实现: javasist 
  - 好处
    - 可以使真正角色的操作更加纯粹，不用去关注一些公共的业务
    - 公共交给代理角色，实现业务的分工
    -  公共业务发生扩展的时候，方便集中管理
    - 一个动态代理代理的是一个接口，一般就是对应的一类业务
    - 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

#### AOP

**什么是AOP**

AOP (Aspect Oriented Programming)意为:面向切面编程,通过**预编译方式和运行期动态代理实现程序功能的统一维护的一种技术**。AOP是OOP的延续,是软件开发中的一个热点,也是Spring框架中的一个重要内容,是函数式编程的一种衍生范型。**利用AOP可以对业务逻辑的各个部分进行隔离,从而使得业务逻辑各部分之间的耦合度降低**,提高程序的可重用性,同时提高了开发的效率。

![image-20210912152400542](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210912152400542.png)

1. AOP在spring中的作用

   提供声明式事务，允许用户自定义切面

   - **横切关注点:**跨越应用程序多个模块的方法或功能。即是,与我们业务逻辑无关的,但是我们需要关注的部分,就是横切关注点。如日志,安全,缓存,事务等等...

   - **切面(ASPECT)** :横切关注点被模块化的特殊对象。即,它是一个类。

   - **通知(Advice)** :切面必须要完成的工作。即,它是类中的一个方法。

   - **目标（Target)** :被通知对象。

   - **代理(Proxy)** :向目标对象应用通知之后创建的对象。

   - **切入点(PointCut)** :切面通知执行的"地点"的定义。

   - **连接点(JointPoint)** :与切入点匹配的执行点。

     ![image-20210912152811973](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210912152811973.png)

1. 使用spring实现AOP

   需要导入的依赖包

   ```xml
   <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjweaver</artifactId>
       <version>1.9.7</version>
       <scope>runtime</scope>
   </dependency>
   ```

   1. 方式一：使用springAPI的接口【主要springapi接口实现】
   2. 方式二：使用自定义类实现【主要是切面定义】
   3. 方式三：使用注解实现 

#### 整合Mybatis 

步骤：

1. 导入相关jar包
   - junit
   - mybatis
   - mysql数据库
   - spring相关的
   - aop织入
   - mybatis-spring【new】
2. 编写配置文件
3. 测试

回忆Mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写mapper.xml
5. 测试
