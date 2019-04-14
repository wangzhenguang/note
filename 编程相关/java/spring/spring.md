



## spring-bean配置

spring三种主要装配配置：

- 在XML中进行显示配置
- 在Java中进行显示配置
- 隐式的bean发现机制和自动装配

@ComponentScan、Configuration、@Component

```xml
<context:component-scan></context:component-scan>
```

### 注解配置方式

用到的注解 @ComponentScan、Configuration、@Component

- @Configuration 注解表明此类是配置类，该类应该包含spring如何创建bean的细节。

  ```java
  //javaConfig中装配bean
  @ComponentScan
  @Configuration
  class Config{
      @Bean 
      public Foo foo(){
          return new Foo();
      }
  }
  ```

  

- @ComponentScan 扫描此注解下包和子包

  注解属性 basePackages ，basePackagesClasses 设置多个扫描包

  ​	 @ComponentScan(basePackages={"package1","package2"})

  ​	@ComponentScan(basePackagesClasses={Class1.class,Class2.class}) 两种方式

- @Component 

  告诉spring需要创建这个注解标注的类。

  @Component("name") 自定义bean的id 

  @Named("name") 自定义id（Java DI)

- @Autowired

  可以作用在构造器、setter方法还是其他的方法。Spring都会尝试满足方法参数上所申明的依赖。

  如果没有匹配的，那么会抛出异常。

  可以通过@Autowired(required=false) ，可以让这个bean处于未装配状态。



### xml配置方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:c="http://www.springframework.org/schema/c"
xmlns:p="http://www.springframework.org/schema/p"
xmlns:p="http://www.springframework.org/schema/util"   
xsi:schemaLocation="http://www.springframework.org/schema/beans
                    http://www.springframework.org/schema/beans/spring-beans.xsd
                    http://www.springframework.org/schema/util
                    http://www.springframework.org/schema/spring-util.xsd
                    http://www.springframework.org/schema/context">
	<context:component-scan base-package="package1"/>
    
   <!--申明一个bean--> 
    <bean id="class1" class="Class1">
    	<constructor-arg ref="class2"/>
    </bean>
    
    <!--使用c命名空间来申明-->
    <bean id="class1" class="Class1" >
            <!--c为命名空间， argname表示构造函数属性名只， ref代表注入bean的引用-->
    	<c:argname-ref="class2"/>
        
    </bean>
    <!-- 参数索引配置，自有一个参数可以省略-->
    <bean id="class1" class="Class1" >
    	<c:_0-ref="class2"/>
    </bean>
     <bean id="class1" class="Class1" >
    	<c:_-ref="class2"/>
    </bean>
    
    <!-- 设置字面量 -->
    <bean id="class1" class="Class1">
    	<constructor-arg value="设置字面量"/>
    </bean>
    <bean id="class1" class="Class1" >
    	<c:_="设置字面量"/>
    </bean>
    
     <!-- 设置null参数 -->
    <bean id="class1" class="Class1">
        <constructor-arg ><null/></constructor-arg>
    </bean>
    <!-- 设置list参数 -->
    <bean id="class1" class="Class1">
        <constructor-arg >
        	<list>
            	<value>1</value>
                <value>2</value>
            </list>
        </constructor-arg>
    </bean> 
     <bean id="class1" class="Class1">
        <constructor-arg >
        	<list>
            	<ref bean="class1"/>
                <ref bean="class2"/>
            </list>
        </constructor-arg>
    </bean> 
    
     <!-- 设置属性 -->
    <bean id="class1" class="Class1">
       <property name="p1" ref="p1" />
    </bean> 
    <!-- p命名空间写法,用法和c一样 -->
  	<bean id="class1" class="Class1" 
          p:p1-ref="p1"></bean>
     <!--util命名空间 简化bean配置 -->
    <util:list id="list1">
    	<value>value1</value>
        <value>value20</value>
    </util:list>
    
</beans>
```

util-命名空间中的元素

| 元素               | 描述                                               |
| ------------------ | -------------------------------------------------- |
| util:constant      | 引用某个类型的public static 域，并将其暴露为bean   |
| util:list          | 创建一个List类型的bean                             |
| util:map           | 创建一个Map类型的bean，其中包含值或引用            |
| util:properties    | 创建一个java.util.Properties类型的bean             |
| util:property-path | 引用一个bean的属性（或内嵌属性），并将其暴露为bean |
| util:set           | 创建一个java.util.Set类型的bean，其中包含值或引用  |



### 混合配置

使用@Import 、@ImportResource

```java
@Configuration
@Import({Config1.class,Config2.class}) //导入javaconfig
@ImportResource("classpath:config.xml")
class Config{
}
```

xml中配置引用JavaConfig

```xml
<beans>
	<bean class='Config'/>
    <import resource='config.xml' />
</beans>
```





## profile配置

@Profile注解应用在类级别上，这个配置类中bean只有dev profile激活时才会创建。

```java
@Configuration
@Profile("dev")
class Config{
    @Bean(destroyMethod="shutdown")
    public DataSource dataSource(){
        return new EmbeddedDataBaseBuilder()
            .addScript("classpath:schema.sql")
            .addScript("classpath:test-data.sql")
            .build();
    }
    
    
}
```

spring3.2开始方法级别上使用@Profile

```java
@Configuration
class DataSourceConfig{
    @Bean(destoryMethod="shutdown")
    @Profile("dev")
    public DataSource embeddedDataSource(){
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .addScript("classpath:schema.sql")
            .addScript("classpath:test-data.sql")
            .build();
    }
    
    @Bean
    @Profile("prod")
    public DataSource jndiDataSource(){
        JndiObjecFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
        jndiObjectFactoryBean.setJndiName("jdbc/myDS");
        jndiObjectFactoryBean.setResourceRef(true);
        jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
        return (DataSource) jndiObjectFactoryBean.getObject();
    }
}
```



### xml中配置profile

```xml
<beans profile="dev"> 
	<jdbc:embedded-database id="dataSource">
    	<jdbc:script location="classpath:schema.sql"/>
        <jdbc:script location="classpath:test-data.sql"/>
    </jdbc:embedded-database>
    
    <!--配置在bean标签中-->
    <bean profile="prod">
    	<jee:jndi-lookup id="dataSource"
                         jndi-name="jdbc/myDatabase"
                         resource-ref="true"
                         proxy-interface="javax.sql.DataSource"/>
    </bean>
    
</beans>
```

### 激活Profile

spring在确定哪个profile处于激活状态时，需要读取两个属性（spring.profiles.active、spring.profiles.default)。

如果设置了active就使用active的值，如果没有就取default。都没有那么只会创建那些没有定义在profile中的bean。

可以通过以下方式来设置：

- 作为DispatcherServlet的初始化参数；
- 作为Web应用的上下文参数；
- 作为JNDI条目；
- 作为环境变量；
- 作为jvm的系统属性
- 在继承测试类上，使用@ActiveProfiles注解设置。

在web应用中，设置spring.profiles.default的web.xml示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app version="2.5">
	<context-param>
    	<param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/root-context.xml</param-value>
    </context-param>
	<context-param>
        <!--为上下文设置profile-->
    	<param-name>spring.profiles.default</param-name>
        <param-value>dev</param-value>
    </context-param>	
    <servlet>
    	<init-param>
             <!--为servlet设置默认profile-->
        	<param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </init-param>
    </servlet>

</web-app>
```



## 



### AOP

配置案例

```xml
<aop:config>
	<aop:aspect ref="foo">
        <!--定义切点-->
        <aop:pointcut id="bar" expression="execution(* *.barMethod(..))" />
        <!--前置通知-->
        <aop:before pointcut-ref="bar" method="fooBeforeMethod"/>
        <!--后置通知-->
        <aop:after pointcut-ref="bar" method="fooAfterMethod"/>
    </aop:aspect>
</aop:config>
```

### 常用上下文

- AnnotationConfigApplicationContext:从一个或多个基于java的配置类中加载Spring应用上下文。

- AnnotationConfigWebApplicationContext: 从一个或多个基于Java的配置类中加载Spring web应用上下文。

- ClassPathXmlApplicationContext:从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件做为类资源。

  ```java
  ApplicationContext context = new ClassPathXmlApplicationContext("test.xml"); //类路径下查找
  ```

  

- FileSystemXmlApplicationContext : 从文件系统下的一个或多个XML配置文件中加载上下文定义。
  ```java
  ApplicationContext context = new FileSystemXmlApplicationContext("/Users/test/test.xml");//文件系统下查找
  ```
- XmlWebApplicationContext: 从Web应用下的一个或多个XML配置文件中加载上下文定义。


### bean的生命周期

![](../../../img/spring-bean.png)

1. Spring对bean进行示例化
2. Spring将值和bean的引用注入到bean对应的属性中；
3. 如果bean实现了BeanNameAware接口，Spring将Bean的ID传给setBeanName()
4. 如果实现了BeanFacotryAware接口，Spring将调用setBeanFacotry(),将BeanFactory容器示例传入
5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext(),将bean所在的应用上下文的引用传入进来。
6. 如果Bean实现了BeanPostProcessor接口，Spring将调用他们的postProcessBeforeInitialization()
7. 如果bean实现了InitializingBean接口，Spring将调用他们的afterPropertiesSet(). 如果使用了initMethod声明初始化方法，该方法也会调用
8. 如果实现了BEanPostProcessor接口，Spring将调用postProcessAfterInitialization
9. bean初始化完毕，将驻留在上下文中。直到上下文销毁
10. 如果bean实现了DisposableBean接口，Spring将调用destroy接口方法。如果bean使用了destroyMethod声明销毁方法，该方法也会被调用









### todo Instrumentation   sping web service 	Integration	Batch		

