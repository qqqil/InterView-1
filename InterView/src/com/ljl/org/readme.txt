 /**
 *@Date:2013-3-20
 *@DEMO:Interview
 *@Date:2015-1-13下午11:15:49
 *@Author:liangjilong
 *@Email:jilongliang@sina.com
 *@Weibo:http://weibo.com/jilongliang
 *@Version:1.0
 *@Description：
 */
 
 

1、OO设计原则
	归根结底是开放封闭原则，程序对外开放，对内封闭。
    对外开放是说程序的扩展性，封闭原则是说类要添加功能，
    最好通过其他类扩展，而不修改原有的类
    
2、 String详解   
 	http://www.cnblogs.com/heima-jieqi/archive/2012/04/10/2440086.html

==================================================================
 
 
 Spring的事务:声明式事务和编程式事务.

采用声明式事务

1、声明式事务配置
	* 配置SessionFactory
	* 配置事务管理器
	* 事务的传播特性
	* 那些类那些方法使用事务

2、编写业务逻辑方法
	* 继承HibernateDaoSupport类，使用HibernateTemplate来持久化，HibernateTemplate是
	  Hibernate Session的轻量级封装
	* 默认情况下运行期异常才会回滚（包括继承了RuntimeException子类），普通异常是不会滚的
	* 编写业务逻辑方法时，最好将异常一直向上抛出，在表示层（struts）处理
	* 关于事务边界的设置，通常设置到业务层，不要添加到Dao上  

3、了解事务的几种传播特性
	1.	propagation_required: 如果存在一个事务，则支持当前事务.如果没有事务则开启
	2.	propagation_supports: 如果存在一个事务，支持当前事务.如果没有事务，则非事务的执行
	3.	propagation_mandatory: 如果已经存在一个事务，支持当前事务.如果没有一个活动的事务，则抛出异常.
	4.	propagation_requires_new: 总是开启一个新的事务.如果一个事务已经存在，则将这个存在的事务挂起.
	5.	propagation_not_supported: 总是非事务地执行，并挂起任何存在的事务.
	6.	propagation_never: 总是非事务地执行，如果存在一个活动事务，则抛出异常
	7.	propagation_nested：如果一个活动的事务存在，则运行在一个嵌套的事务中. 如果没有活动事务, 
	     则按TransactionDefinition.PROPAGATION_REQUIRED 属性执行

4、Spring事务的隔离级别
	1.	isolation_default： 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别.
	     另外四个与JDBC的隔离级别相对应
	2.	ISOLATION_READ_UNCOMMITTED： 这是事务最低的隔离级别，它充许令外一个事务可以看到这个事务未提交的数据.
	     这种隔离级别会产生脏读，不可重复读和幻像读.
	3.	ISOLATION_READ_COMMITTED： 保证一个事务修改的数据提交后才能被另外一个事务读取.另外一个事务不能读取该事务未提交的数据
	4.	ISOLATION_REPEATABLE_READ： 这种事务隔离级别可以防止脏读，不可重复读.但是可能出现幻像读.
	     它除了保证一个事务不能读取另一个事务未提交的数据外，还保证了避免下面的情况产生(不可重复读).
	5.	ISOLATION_SERIALIZABLE 这是花费最高代价但是最可靠的事务隔离级别.事务被处理为顺序执行.
	     除了防止脏读，不可重复读外，还避免了幻像读.		    

---------======================================================================================================================

spring对AOP的只是（采用Annotation注解的方式）

1、spring依赖库
	* SPRING_HOME/dist/spring.jar
	* SPRING_HOME/lib/jakarta-commons/commons-logging.jar
	* SPRING_HOME/lib/log4j/log4j-1.2.14.jar
	* SPRING_HOME/lib/aspectj/*.jar
	
2、采用Aspect定义切面

2、在Aspect定义Pointcut和Advice

4、启用AspectJ对Annotation的支持并且将Aspect类和目标对象配置到Ioc容器中

注意：在这种方法定义中，切入点的方法是不被执行的，它存在的目的仅仅是为了重用切入点
即Advice中通过方法名引用这个切人点

AOP:
	* Cross cutting concern
	* Aspect
	* Advice
	* Pointcut
	* Joinpoint
	* Weave
	* Target Object
	* Proxy
	* Introduction
---------======================================================================================================================
spring对AOP的只是（采用配置文件的方式）

1、spring依赖库
	* SPRING_HOME/dist/spring.jar
	* SPRING_HOME/lib/jakarta-commons/commons-logging.jar
	* SPRING_HOME/lib/log4j/log4j-1.2.14.jar
	* SPRING_HOME/lib/aspectj/*.jar
2、配置如下
	<aop:config>
		<aop:aspect id="security" ref="securityHandler">
			<aop:pointcut id="allAddMethod" expression="execution(* com.bjsxt.spring.UserManagerImpl.add*(..))"/>
			<aop:before method="checkSecurity" pointcut-ref="allAddMethod"/>
		</aop:aspect>
	</aop:config>	
		
---------======================================================================================================================
spring对AOP的支持

Aspect默认情况下不用实现接口，但对于目标对象（UserManagerImpl.java），在默认情况下必须实现接口
如果没有实现接口必须引入CGLIB库

我们可以通过Advice中添加一个JoinPoint参数，这个值会由spring自动传入，从JoinPoint中可以取得
参数值、方法名等等
---------======================================================================================================================

spring对AOP的支持

1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP
2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP
3、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换


如何强制使用CGLIB实现AOP？
	* 添加CGLIB库，SPRING_HOME/cglib/*.jar
	* 在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>
	
JDK动态代理和CGLIB字节码生成的区别？
	* JDK动态代理只能对实现了接口的类生成代理，而不能针对类
	* CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法
	  因为是继承，所以该类或方法最好不要声明成final	
---------======================================================================================================================
采用编程式事务

1、getCurrentSession()与openSession()的区别？
	* 采用getCurrentSession()创建的session会绑定到当前线程中，而采用openSession()
	  创建的session则不会
	* 采用getCurrentSession()创建的session在commit或rollback时会自动关闭，而采用openSession()
	  创建的session必须手动关闭
	  
2、使用getCurrentSession()需要在hibernate.cfg.xml文件中加入如下配置：
	* 如果使用的是本地事务（jdbc事务）
	<property name="hibernate.current_session_context_class">thread</property>
	* 如果使用的是全局事务（jta事务）
	<property name="hibernate.current_session_context_class">jta</property>	    		

---------======================================================================================================================


1、spring的普通属性注入	
	参见：spring文档3.3章节
	
什么是属性编辑器，作用？
	* 自定义属性编辑器，spring配置文件中的字符串转换成相应的对象进行注入
	spring已经有内置的属性编辑器，我们可以根据需求自己定义属性编辑器
	
	* 如何定义属性编辑器？
		* 继承PropertyEditorSupport类，覆写setAsText()方法，参见：UtilDatePropertyEditor.java
		* 将属性编辑器注册到spring中，参见：applicationContext-editor.xml
		
依赖对象的注入方式，可以采用：
	* ref属性
	* <ref>标签
	* 内部<bean>来定义
	
如何将公共的注入定义描述出来？
	* 通过<bean>标签定义公共的属性，指定abstract=true
	* 具有相同属性的类在<bean>标签中指定其parent属性
	
	参见：applicationContext-other.xml
				
---------======================================================================================================================
spring Bean的作用域：
	
scope可以取值：	
	* singleton:每次调用getBean的时候返回相同的实例
	* prototype:每次调用getBean的时候返回不同的实例
---------======================================================================================================================
spring+struts的集成(第一种集成方案)
原理：在Action中取得BeanFactory对象，然后通过BeanFactory获取业务逻辑对象

1、spring和struts依赖库配置
	* 配置struts
		--拷贝struts类库和jstl类库
		--修改web.xml文件来配置ActionServlet
		--提供struts-config.xml文件
		--提供国际化资源文件
	* 配置spring
		--拷贝spring类库
		--提供spring配置文件 
		
2、在struts的Action中调用如下代码取得BeanFactory
	BeanFactory factory = WebApplicationContextUtils.getRequiredWebApplicationContext(request.getSession().getServletContext());

3、通过BeanFactory取得业务对象，调用业务逻辑方法			
---------======================================================================================================================
		
spring+struts的集成(第二种集成方案)
原理：将业务逻辑对象通过spring注入到Action中，从而避免了在Action类中的直接代码查询

1、spring和struts依赖库配置
	* 配置struts
		--拷贝struts类库和jstl类库
		--修改web.xml文件来配置ActionServlet
		--提供struts-config.xml文件
		--提供国际化资源文件
	* 配置spring
		--拷贝spring类库
		--提供spring配置文件 
2、因为Action需要调用业务逻辑方法，所以需要在Action中提供setter方法，让spring将业务逻辑对象注入过来

3、在struts-config.xml文件中配置Action
 	* <action>标签中的type属性需要修改为org.springframework.web.struts.DelegatingActionProxy
 	 DelegatingActionProxy是一个Action，主要作用是取得BeanFactory，然后根据<action>中的path属性值
 	 到IoC容器中取得本次请求对应的Action
 	 
4、在spring配置文件中需要定义struts的Action,如：
	<bean name="/login" class="com.bjsxt.usermgr.actions.LoginAction" scope="prototype">
		<property name="userManager" ref="userManager"/>
	</bean>
	* 必须使用name属性，name属性值必须和struts-config.xml文件中<action>标签的path属性值一致
	* 必须注入业务逻辑对象
	* 建议将scope设置为prototype,这样就避免了struts Action的线程安全问题
	 	 		
		
			
			






 