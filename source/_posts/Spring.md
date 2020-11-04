---
title: Spring
date: 2020-02-28 11:08:14
tags: Spring
categories:
  框架
---

### 1.IoC和DI

 IoC（Inverse of Control：控制反转，Spring的原作者Rod Johnson）：  A组件（service）需要调用B组件(dao)，成为A组件依赖B组件。方式一、new一个；方式二、通过工厂获取一个；方式三、由容器提供了一个。方式一、二（传统）需要A组件主动去获取；方式三方式A组件(service)只要被动接收依赖的对象（dao）就行了——从A组件的角度来看：A组件由原来的主动获取变成被动接受，控制被反过来 —— 叫控制反转。

 DI（Dependency Injection：依赖注入）： Martine Fowler。——从容器的角度来看：容器将被依赖B组件(dao)注入给A组件(service)  ——依赖注入。正因为有容器的依赖注入，A组件才得以接受容器的注入，实现控制反转。

### 2.依赖注入

依赖注入可分为3种：
1. 接口注入。很少使用。
2. 设值注入：就是通过property元素控制调用setter方法，就是所谓的设值注入。
3. 构造注入：就是constructor-arg控制调用有参数的构造器，由构造器来注入被依赖组件。就是所谓的构造器注入。

- bean元素 ：驱动使用new调用构造器。 默认它总是调用无参数的构造器。如果想控制它调用有参数的构造器，就需要在<bean.../>元素里添加<constructor-arg.../>子元素，每个该元素代表一个构造器参数。

- property元素： 驱动它调用setter方法。 对象创建出来之后，立即就会被调用。
- constructor-arg元素： 驱动调用有参数的构造器。

构造注入和set注入的区别：
构造注入 在对象建立时就准备好所有资源,如果要建立的对象关系很多(参数多)，
使用构造注入会在建构函式上留下一长串参数,且不易记忆,但是如果想要让一些数据成员或资源变为只读或私有,可以选择构造注入。

set注入 在对象建立好后,使用set注入内容，可以有明确的名称，可以了解注入的对象是什么，setXXX当然比Constructor上某个参数的位置代表某个对象好，但是使用set注入由于提供了setXXX方法,所以不能保证相关的数据成员或资源在执行时不会被更改设定

总结：如果想要让一些数据成员或资源变为只读或私有,可以选择构造注入。大多数时候都是使用set注入。

####  2.1设值注入
User类：
```java
public class User {
	
	private int id;
	private  String name;
	private char sex;
	
	private Date bDate;
	
	public User() {
		System.out.println("=====经过User的无参构造器====");
	}
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public char getSex() {
		return sex;
	}
	public void setSex(char sex) {
		this.sex = sex;
	}
	public Date getbDate() {
		return bDate;
	}
	public void setbDate(Date bDate) {
		this.bDate = bDate;
	}
	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", sex=" + sex + ", bDate=" + bDate + "]";
	}
}
```
Test类：
```java
public class Test {

/**
*强内聚（一个类的功能越多越好） 低耦合（对象所依赖的尽量少）
* 依赖关系（耦合），service依赖dao
  * */
  public static void main(String[] args) {
      
  //加载spring核心配置文件，让spring容器进行初始化
   ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
      
   //从容器中获取User对象
   User user = context.getBean("user",User.class);
   System.out.println("user:"+user);    
  }
}

```
applicationContext.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

      <!-- 
         User user = new User();
         user.setId(1);
         user.setName("jack");
         user.setSex("男");
         user.setbDate(birdate);
       -->

      <bean id="user" class="com.zx.bean.User">
            <property name="id" value="1"></property>
            <property name="name" value="jack"></property>
            <!-- 通过value指定sex的值为男 -->
            <property name="sex" value="男"></property>
            <!-- 通过ref指定需要传入的bean的id -->
            <property name="bDate" ref="birdate"></property>
      </bean>

     <bean id="birdate" class="java.util.Date"></bean>

</beans>
```

####  2.2构造注入

Test类：
```java
public class Test {

/**
  * 强内聚（一个类的功能越多越好） 低耦合（对象所依赖的尽量少）
  * 依赖关系（耦合），service依赖dao
  * */
  public static void main(String[] args) {
    
    //加载spring核心配置文件，让spring容器进行初始化
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    
    //从容器中根据bean的id获取User对象   如果id不存在则在程序运行的时候会出现异常
    User user = context.getBean("user",User.class);
    System.out.println("user:"+user);   
    
    //从容器中获取User对象
    User user02 = context.getBean("user02",User.class);
    System.out.println("user02:"+user02);   
  }
}

```
applicationContext.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    
      <!-- 
         User user = new User(1,"rose",'女',birdate);         
     -->

      <bean id="user" class="com.zx.bean.User">
         <!-- 
            index:指定参数下标
            value：指定参数的值
            type:指定参数的类型
          -->
         <constructor-arg index="0" value="1" type="int"></constructor-arg> 
         <constructor-arg index="1" value="rose" type="java.lang.String"></constructor-arg> 
         <constructor-arg index="2" value="女" type="char"></constructor-arg> 
         <constructor-arg index="3" ref="birdate" type="java.util.Date"></constructor-arg> 
         
      </bean>
      
      <bean id="user02" class="com.zx.bean.User">
         <!-- 
            name:指定参数名字
            value：指定参数的值
            type:指定参数的类型
          -->
         <constructor-arg name="id" value="1" type="int"></constructor-arg> 
         <constructor-arg name="name" value="jack" type="java.lang.String"></constructor-arg> 
         <constructor-arg name="sex" value="男" type="char"></constructor-arg> 
         <constructor-arg name="bDate" ref="birdate" type="java.util.Date"></constructor-arg> 
         
      </bean>

     <bean id="birdate" class="java.util.Date"></bean>

</beans>
```

### 3.Bean别名

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

 
      <!-- 
                     给bean声明别名
                             方案一:通过name属性
                             方案二:通过标签通过<alias.../>元素专门指定执行别名信息
       -->
      <bean id="user" class="com.zx.bean.User" name="us">
      
      </bean>

       <alias name="us" alias="userInfo"/>
</beans>
```

### 4.简化配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
 
   <!--  <bean id="user" class="com.zx.bean.User">
        <property name="id" value="1"></property>
        <property name="name" value="jack"></property>
    </bean> -->
    
    <bean id="user" class="com.zx.bean.User" p:id="1" p:name="jack" p:sex="女" p:bDate-ref="bdate">   
    </bean>
    
    <bean id="us" class="com.zx.bean.User" c:_0="2" c:_1="tom" c:_2="男" c:_3-ref="bdate">   
    </bean>

    <bean id="bdate" class="java.util.Date"></bean>
     
</beans>
```
### 5.嵌套bean
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

 
     <bean id="user" class="com.zx.bean.User" p:id="1" p:name="jack" p:sex="男">
         
         <property name="dog">
            <!-- 
                                配置嵌套bean 
            1、嵌套bean无需配置id，因为不能通过id从容器中获取到
            
            -->
            <bean class="com.zx.bean.Dog" p:id="2" p:dogName="泰迪"></bean>
         </property>
     </bean>
     
     
       <bean id="user02" class="com.zx.bean.User" c:_0="1" c:_1="jack" c:_2="男">
           <constructor-arg index="3">
      <!--          配置嵌套bean  -->
               <bean class="com.zx.bean.Dog" p:id="2" p:dogName="阿拉斯加"></bean>
           </constructor-arg>
       </bean>
     
</beans>
```

### 6.bean的自动装配
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    default-autowire="byName"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

 
   <!--  
      byName：根据setter方法名来自动装配。Spring查找容器中全部Bean，
               找出其中id属性与setter方法名去掉set前缀后同名的Bean来完成注入。
               如果没有找到匹配的Bean实例，则Spring不会进行任何注入，也不报错
      <bean id="user" class="com.zx.bean.User" p:id="1" 
      p:name="jack" p:sex="男" autowire="byName"></bean> 
      
      byType：根据setter方法的形参类型来自动装配。BeanFactory查找容器中全部Bean，
		      如果正好有一个Bean类型与setter形参类型匹配，就自动注入这个Bean；
		      如果有多个这样的Bean，就抛出一个异常：NoUniqueBeanDefinitionException；
		      如果没有找到匹配的Bean实例，则Spring不会进行任何注入，也不报错。
	
	constructor：通过构造器进行注入，首先查找是否有bean的id和形参名字一致，如果有则注入
	如果没有则查找是否有和参数类型匹配的进行注入
		      
     no：不使用自动装配。Bean依赖必须通过ref元素定义
     
     default： 由上级标签<beans>的default-autowire属性确定。这是默认的配置
		       --> 
     
       <!-- <bean id="user" class="com.zx.bean.User" p:id="1" 
      p:name="jack" p:sex="男" autowire="byType"></bean> -->
      
      <!-- <bean id="user" class="com.zx.bean.User" c:_0="1" 
      c:_1="jack" c:_2="男" autowire="constructor"></bean> -->
      
      <!-- <bean id="user" class="com.zx.bean.User" p:id="1" 
      p:name="jack" p:sex="男" autowire="no"></bean> -->
      
      <bean id="user" class="com.zx.bean.User" p:id="1" 
      p:name="jack" p:sex="男" autowire="default"></bean> 
      
      <bean id="dog" class="com.zx.bean.Dog" p:id="3" p:dogName="旺旺"></bean>

</beans>
```
### 7.集合属性的注入
User类：
```java
public class User {
	
	private int id;
	
	private String[] names;
	
	private List<String> address;
	
	private Set<Integer> ages;
	
	private Map<String,String> maps;
	
	private Properties  props;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String[] getNames() {
		return names;
	}

	public void setNames(String[] names) {
		this.names = names;
	}

	public List<String> getAddress() {
		return address;
	}

	public void setAddress(List<String> address) {
		this.address = address;
	}

	public Set<Integer> getAges() {
		return ages;
	}

	public void setAges(Set<Integer> ages) {
		this.ages = ages;
	}

	public Map<String, String> getMaps() {
		return maps;
	}

	public void setMaps(Map<String, String> maps) {
		this.maps = maps;
	}

	public Properties getProps() {
		return props;
	}

	public void setProps(Properties props) {
		this.props = props;
	}

	@Override
	public String toString() {
		return "User [id=" + id + ", names=" + Arrays.toString(names) + ", address=" + address + ", ages=" + ages
				+ ", maps=" + maps + ", props=" + props + "]";
	}
	
}

```
xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    default-autowire="byName"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

 
    <bean id="user" class="com.zx.bean.User" p:id="1">
        <property name="names">
            <array>
                <value>jack</value>
                <value>rose</value>
            </array>
        </property>
    
        <property name="address">
            <list>
                <value>开福区</value>
                <value>岳麓区</value>
            </list>
        </property>
        
        <property name="ages">
            <set>
              <value>20</value>
              <value>21</value>
            </set>
        </property>
        
        <property name="maps">
          <map>
              <entry key="0001" value="中南大学"></entry>
              <entry key="0002" value="湖南大学"></entry>
              <entry key="0003">
                <value>长沙理工</value>
              </entry>
          </map>
        </property>
        
        <property name="props">
            <props>
                <prop key="01">java</prop>
                <prop key="02">python</prop>
            </props>
        </property>
    </bean>
</beans>
```
### 8.抽象bean
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:c="http://www.springframework.org/schema/c"
    default-autowire="byName"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">


     <!-- 配置抽象bean 
         
         abstract="true":声明该 bean是抽象bean
         
         当多个bean中属性名与属性值都一致的情况下  可以将 公共的信息抽取至抽象bean中
         子bean可以通过 parent  指定 抽象父bean的信息,并不需要创建一个Animal类。
     
     -->
     <bean id="animal"   p:id="1" p:name="旺财" p:age="1" abstract="true"></bean>
 
     <bean id="dog" class="com.zx.bean.Dog" parent="animal"></bean>

     <bean id="cat" class="com.zx.bean.Cat"  p:address="开福区XX" parent="animal"></bean>
     
</beans>
```