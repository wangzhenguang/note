



### DI

```java
@ComponentScan // 启用注解扫描，默认会扫描与配置类相同的包。
@Configuration
public class Foo{}


```

xml版

```xml
<context:component-scan base-package="package.xxx"/>
```







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

