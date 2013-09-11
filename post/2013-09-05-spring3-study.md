# Spring 依赖注入总结

## 1. DI

Spring内置了两各基础DI容器：BeanFactory, ApplicationContext. 企业级环境一般用后者。

### 1.1 多种依赖注入方式
#### 1.1.1 setter注入
通过调用setter方法建立对象间的依赖关系。

xml配置文件 beanfactory.xml

	<bean id="someService" class="com.xxx.xx.SomeServiceImpl" >
		<property name="someDependencyService" ref = "someDependencyService" />
	</bean>

依赖注入

	// 从classpath装载xml配置信息
	Resource resource = new ClassPathResource("beanfactory.xml");
	// 实例化IoC容器，xml内的各受管Bean并未实例化
	BeanFactory factory = new XmlBeanFactory(resource);
	// 获得受管的Bean
	ISomeService someService = (ISomeService) factory.getBean("someService");
	someService.xxx();

>初始化BeanFactory实例扣，IoC容器只在使用到对应的POJO时才会进行实例化。

#### 1.1.2. 构造函数注入

xml配置文件 beanfactory.xml

	<bean id="someService" class="com.xxx.xx.SomeServiceImpl" >
		<constructor-arg index="0" ref = "someDependencyService1" />
		<constructor-arg type="java.util.Properties"  value = null />
	</bean>

#### 1.1.3. 属性注入

借助@Autowired，@Resource等注解

代码

	@Autowired
	private ISomeDependencyService someDependencyService;

xml配置文件

	<bean id="someDependencyService" class="com.xxx.someDependencyServiceImpl" >
	</bean>

	<bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor" />

属性注入

	//从classpath上装载xml配置信息
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationcontext.xml");
	//获得受管的Bean

	ISomeService someService = applicationContext.getBean(ISomeService.class);

>配置文件不用给出对象间依赖关系

#### 1.1.4. 方法注入

### 1.2 Autowiring智能注入
#### 1.2.1 <bean>元素的autowire属性

	<bean id="someService" class="com.xxx.SomeServiceImpl" autowire="byType" />

* `byName` 通过属性名完成autowire操作。如果受管Bean a中含有b属性，则IoC容器会去所有受管Bean中寻找id为b的受管Bean。
* `byType` 通过属性类型完成autowire操作。在IoC容器中寻找同类型的受管Bean，找到多个异常。

#### 1.2.2 基于@Required注解加强协作者管理
启动时保证作用的方法被调用到，但不能保证一定注入成功，可以注入null即骗过了`@Required`。

#### 1.2.3 @Inject
是javax.inject引入，默认时一旦@Autowired或@Inject注解作用到构造函数、属性或方法上，则协作者必须存在，否则启动失败。即默认具有@Required能力，可以设置关闭。

	@Autowired(required=false)
	private ISomeSerivce someService;

#### 1.2.4 设置优先级
DI容器中多个受管Bean都符合条件时，可设置`primary`优先选择。如果没有设置则启动异常。

	<bean id="someSerivce" class="com.xxx.SomeServiceImpl" primary="true" />

同样的可以设置`autowire-candidate=false`，让受管的Bean不参与到Autowiring策略中。

>如果需要有序地将各个受管Bean注入可以配置`@Qualified`。









