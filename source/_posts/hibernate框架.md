---
title: hibernate框架
date: 2020-03-05 22:23:04
tags: hibernate
categories: 
- javaWeb
- java高级技术
---

### hibernate框架
&emsp;&emsp;作为SSH三大框架之一的Hibernate，是用来把程序的Dao层和数据库打交道用的，它封装了JDBC的步骤，是我们对数据库的操作更加简单，更加快捷。利用Hibernate框架我们就可以不再编写重复的JDBC代码，不再反复的测试我们的SQL语句写的如何。这里这需要我们简单配置，调用框架给我们提供的方法，就可以完成对数据增删改查。

#### 1.环境搭建

1.导入jar包 （required下所有的jar包 + lib 下 c3p0 的jar包 + 数据库 jar 包）
2.创建一个需要持久化的类
```java
package org.fkjava.pojo;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

/*
 * @Entity:声明当前类是一个持久化bean，声明之后hibernate才会管理该bean,该注解必须存在
 * @Table:声明当前类是与数据库中哪一个表对应，如果没有则将类名作为表名
 *  - name：指定表名  
 *  -indexes：指定索引的相关信息
 * @Id：声明当前列是主键
 * @GeneratedValue：指定主键的生成策略
 *  - strategy=GenerationType.IDENTITY 用于mysql或者 sql server代表主键自动增长
 *  - strategy=GenerationType.SEQUENCE 用于oracle或者db2 表示通过序列进行主键值的生成
 * @Column:声明当前属性列的相关信息
 *   - name:指定列名
 *   - length：指定列的长度
 *   - unique：是否唯一
 *   
 * @Transient：声明该属性 不用于数据库关联，是临时使用的字段
 * 
 * @Id：声明该属性做主键
 * @GeneratedValue:指定主键的生成策略
 *  -  IDENTITY ：自增长。要求底层数据库支持自增加。比如MySQL、SQL Server
 *  -  SEQUENCE ：用sequence。要求底层数据库支持SEQUENCE 。比如Oracle、DB 2
 *  - AUTO  ：让系统自动判断数据库的类型
 * */

@Entity
@Table(name="TB_USER")
public class User {
	
	// @Id表示该Field是一个主键  ,  当需要多个字段一起作联合主键时，在属性上声明多个@Id,联合主键不需要声明主键的生成策略。
	//  复合类型作主键时，使用的是 @EmbeddedId 
	// @GeneratedValue表示主键自动生成，strategy表示主键生成策略，
	// IDENTITY是mysql或sql server的自动增长
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)  
	@Column(name="id")
	private Integer id;
	
	// @Column(name="name")表示当前的Field（name）对应数据库的Column（name）
	@Column(name="name") 
	private String name;
	@Column(name="sex")
	private String sex;
	@Column(name="age")
	private Integer age;
	
	public User() {
		super();
		// TODO Auto-generated constructor stub
	}
	public User(String name, String sex, Integer age) {
		super();
		this.name = name;
		this.sex = sex;
		this.age = age;
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", sex=" + sex + ", age="
				+ age + "]";
	}
	
}

```
3.配置hibernate.cfg.xml文件
```xml
<!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
	<!-- 数据源 -->
 <session-factory>
	<!-- 配置Hibernat的数据库方言，注意mysql5之后是MySQL5InnoDBDialect -->
	<property name="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>
	
	<!-- 连接数据库的参数 -->
	<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
	<!--  jdbc:mysql:///j1510 直接连本机-->
	<property name="hibernate.connection.url">jdbc:mysql://127.0.0.1:3306/j1601</property>
	<property name="hibernate.connection.username">root</property>
	<property name="hibernate.connection.password">root</property>
	
	<!-- 配置DataSource -->
	<!-- 最大连接数 -->
	<property name="hibernate.c3p0.max_size">200</property>
	<!-- 最小连接数 -->
	<property name="hibernate.c3p0.min_size">2</property>
	<!-- 获得连接的超时时间,如果超过这个时间,会抛出异常，单位毫秒 -->
	<property name="hibernate.c3p0.timeout">5000</property>
	<!-- 最大的PreparedStatement的数量 -->
	<property name="hibernate.c3p0.max_statements">100</property>
	<!-- 每隔3000秒检查连接池里的空闲连接 ，单位是秒-->
	<property name="hibernate.c3p0.idle_test_period">3000</property>
	<!-- 当连接池里面的连接用完的时候，C3P0一下获取的新的连接数 -->
	<property name="hibernate.c3p0.acquire_increment">2</property>
	<!-- 每次是否验证连接是否可用 -->
	<property name="hibernate.c3p0.validate">false</property>
	
	<!-- sql语句显示在控制台 -->
	<property name="hibernate.show_sql">true</property>
	<property name="hibernate.format_sql">true</property>
	
	<!-- 
		create：每次都会重新创建表
		update：如果有表，就不管；如果没表，就创建 ，如果有更新，会更新
		create-drop：每次都会重新创建表，之后删除，测试
	-->
	<property name="hibernate.hbm2ddl.auto">update</property>
	
	<!-- 告诉Hibernate自定义的映射文件
		如果是xml文件：rosource
		如果是类文件：class
		-->
	<mapping class="org.fkjava.pojo.User"/>
	
 </session-factory>
</hibernate-configuration>
```
4.写测试类进行测试
```java
package org.fkjava.test;

import java.io.Serializable;

import org.fkjava.pojo.User;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.hibernate.cfg.Configuration;

public class Test {

   public static void main(String[] args) {
			
	//获取连接池  通过连接获取连接  
	
	//Hibernate4的写法读取配置文件    创建Configuration实例的时候默认加载  hibernate.properties配置文件  ,
	//通过hibernate.properties管理数据源比较麻烦  所以现在主流的方式 通过hibernate.cfg.xml来管理数据源相关信息
	//new Configuration().configure()：默认加载src下的hibernate.cfg.xml文件
		//Configuration cfg = new Configuration().configure(); 
		// SessionFactory：整个数据库映射关系在程序中的内存映像，封装了数据源等信息。
	//	SessionFactory sessionFactory = cfg.buildSessionFactory(new StandardServiceRegistryBuilder().applySettings(cfg.getProperties()).build());

	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();
		
	// 插入一条数据
	// Session：负责CRUD操作，封装了连接。 
	Session session = sessionFactory.openSession();
	// 开启事务对象
	Transaction tx = session.beginTransaction();
	// User对象
	User user = new User("rose","女",19);
	// 插入数据
	Serializable id = session.save(user);
	System.out.println("插入的数据id值["+id+"]");
	// 提交事务 
	tx.commit();
	// 关闭
	session.close();		
	}
}
```

#### 2.查询 ：get()方法和 load()方法

```java
 //Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();
	
	//根据get方法获取对象的信息   get(对象的class类型，主键id的值) 特点:1、get是立即加载   2、如果找不到指定的数据则返回  null
	//User user = session.get(User.class, 2);
	//System.out.println("user:"+user);
	
	//根据load方法也可以获取对象的信息 load(对象的class类型，主键id的值) 特点:1、load指的是延时加载   2、找不到指定的记录的时候就会抛出异常
	User u = session.load(User.class, 4);
	System.out.println("u:"+u.getName());
```

####  3.更新 ：saveOrUpdate()方法和 update()方法
```java
     //构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();
	
	//获取事务对象
	Transaction tr = session.beginTransaction();

	User  user = new User();
	user.setName("翠花002");
	user.setAge(20);
	user.setSex('女');
	
	user.setId(7);
	
	/**
	* saveOrUpdate方法既可以进行添加也可以进行更新操作    
	* 通过判定user对象中是否有主键id值决定进行添加还是更新，
	* 
	* 通过update方法 或者 saveOrUpdate方法进行更新的时候，会将对象中所有的列的信息进行更新，如果没有设置值，会使用默认值（int类型默认为0，引用类型默认为null）
	* 
	* */
	//1、通过saveOrUpdate方法进行更新
	//session.saveOrUpdate(user);
	
	//2、调用update方法进行更新
	//session.update(user);
	
	//3、在持久化状态下进行更新操作
	User u = session.get(User.class, 2);
	u.setName("tom");
	
	//提交事务
	tr.commit();
	//关闭连接
	session.close();
	
```
####  4.新增 ：saveOrUpdate()方法、save()方法、persist()方法
```java
//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();
	
	//获取事务对象
	Transaction tr = session.beginTransaction();

	//创建User对象
	User  user = new User();
	user.setName("翠花003");
	user.setAge(20);
	user.setSex('女');
	
	//通过save方法保存数据   特征 1、save有返回值，返回的是自动生成的主键的值    2、不能进行级联保存
	//Serializable id = session.save(user);   
	//System.out.println("id:"+id);
	
	//通过persist方法保存数据   特征 1、persist没有返回值   2、可以进行级联保存
	//session.persist(user);
	
	user.setId(8);
	/**
	* saveOrUpdate方法既可以进行添加也可以进行更新操作    
	* 通过判定user对象中是否有主键id值决定进行添加还是更新
	* 
	* 通过update方法 或者 saveOrUpdate方法进行更新的时候，会将对象中所有的列的信息进行更新
	* 
	* */
	session.saveOrUpdate(user);
	
	//提交事务
	tr.commit();
	//关闭连接
	session.close();
```
####  5.删除 ：saveOrUpdate()方法、save()方法、persist()方法
```java
//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction tr = session.beginTransaction();

	User  user = new User();
	//指定需要删除的用户的id
	user.setId(7);

	session.delete(user);

	//提交事务
	tr.commit();
	//关闭连接
	session.close();
```

####  6.close()、clear() 和 evict()
```java
    //Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();
	
	//获取事务对象
	Transaction tr = session.beginTransaction();

	//根据id获取用户信息   对象会session关联
	User user = session.get(User.class, 2);
	
	//清空session中所有的缓存，意味着之前与session有关联的对象全部会与session失去关联，变成脱管状态
	session.clear();
	
	//从session中清除指定的对象，清除之后该对象会变成脱管状态
	session.evict(user);
	
	//提交事务
	tr.commit();
	//关闭连接
	session.close();
```

### 7.集合映射
```java
......
@Entity
@Table(name="tb_user")
public class User implements Serializable{

   private static final long serialVersionUID = 1L;

   @Id
   @GeneratedValue(strategy=GenerationType.IDENTITY)
   private int id;
   @Column(name="tb_name",length=10,unique=true)
   private String name;   
   private int age;
   private char sex;
   private String email;
   
   
   /*
    * @ElementCollection:指定当前属性是一个集合属性 
    *  - targetClass：每一个元素的类型
    *  - fetch ：指定抓取策略   FetchType.LAZY 是懒加载的意思     FetchType.EAGER是立即加载的意思
    * 
    * @CollectionTable：指定表的信息，通过该表维护集合属性中的数据 
    *  - @JoinColumn
    * 
    * @OrderColumn:序列名称
    * 
    * */
   @ElementCollection(targetClass=String.class,fetch=FetchType.LAZY)
   @CollectionTable(name="user_name",
   	joinColumns={@JoinColumn(name="user_id")})
   @OrderColumn(name="nick_order")
   private String[] names; // 数组
	   
.......     
}
```

```java
public class Test {

  public static void main(String[] args) {

	//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();

	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction transaction = session.beginTransaction();

	/* User user = new User();
	user.setName("悟空");
	user.setEmail("584614151@qq.com");
	user.setAge(20);
	user.setSex('男');
	
	String[] names = {"齐天大圣","弼马温","美猴王"};
	//指定别名信息
	user.setNames(names);
	session.save(user);*/
	
	User u = session.get(User.class, 1);
	//提交事务
	transaction.commit();
	
 }
}

```
### 8.一对多关联关系配置以及单项/双向关联

Studnet类：
```java 
.......
@Entity
@Table(name="tb_student")
public class Student implements Serializable{

   private static final long serialVersionUID = 1L;

   @Id
   @GeneratedValue(strategy=GenerationType.IDENTITY)
   private int id;
   @Column(name="tb_name",length=10,unique=true)
   private String name;   
   private int age;
   private char sex;
   private String email;
   
   /**
    * @ManyToOne : 多对一
    *  - targetEntity：当前类所关联的实体bean的类型
	*  - fetch 指定抓取策略   FetchType.LAZY：懒加载    FetchType.EAGER：立即加载
	*  - cascade指定级联策略   CascadeType.PERSIST代表级联保存   CascadeType.REMOVE代表级联删除   
                         CascadeType.ALL：级联相关操作都支持
    * @JoinColumn：指定外键的信息
    *  - name：指定外键的名字
    *  - referencedColumnName：外键class_id关联到Class班级表的哪一个列
    *  - foreignKey 指定外键的约束名，不写的话 会有默认生成
    * */
   
   //学生关联班级
   @ManyToOne(targetEntity=Clazz.class,fetch=FetchType.EAGER,cascade=CascadeType.PERSIST)
   @JoinColumn(name="clazz_id",referencedColumnName="c_id", 
			foreignKey=@ForeignKey(name="FK_clazz_id"))
   private Clazz clazz;
.......
	   	   	
}

```
Clzz类：
```java

@Entity
@Table(name = "tb_clazz")
public class Clazz implements Serializable {

	private static final long serialVersionUID = 1L;

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name="c_id")
	private int id;// 班级主键id

	private String code;// 班级编号

	@Column(name = "tb_name", length = 10, unique = true)
	private String name;// 班级名字

	private String bzr;// 班主任
	
	/*
	 *  @OneToMany:一对多
	 *  - targetEntity：当前类所关联的实体bean的类型
	 *  - fetch 指定抓取策略   FetchType.LAZY：懒加载    FetchType.EAGER：立即加载
	 *  - cascade指定级联策略   CascadeType.PERSIST代表级联保存   CascadeType.REMOVE代表级联删除   
                          CascadeType.ALL：级联相关操作都支持
        - mappedBy 指定关联关系对方实体bean的哪一个属性来维护                   
	 * */
	//班级关联学生
	@OneToMany(targetEntity=Student.class,fetch=FetchType.EAGER,
			cascade=CascadeType.ALL,mappedBy="clazz")
    private  List<Student> students;

.......
}
```

```java
public class Test {

	public static void main(String[] args) {

	//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();

	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction transaction = session.beginTransaction();
	
	Clazz clazz = new Clazz();
	clazz.setCode("j233");
	clazz.setName("java就业班");
	clazz.setBzr("刘老师");
	
	Student s1 = new Student();
	s1.setName("jack");
	s1.setAge(20);
	s1.setEmail("584614151@qq.com");
	s1.setSex('男');
	
	s1.setClazz(clazz);

	//通过 persist 保存学生信息 同时级联保存班级信息
	//session.persist(s1);
	
	Student s2 = new Student();
	s2.setName("rose");
	s2.setAge(21);
	s2.setEmail("584616151@qq.com");
	s2.setSex('男');
	s2.setClazz(clazz);
	
	//session.persist(s2);
	
	//查询班级信息
	Clazz c1 = session.get(Clazz.class, 1);
	System.out.println("班级名字:"+c1.getName());
	
	System.out.println(c1.getStudents());
	
	
	//提交事务
	transaction.commit();
	session.close();
	
	}

```

```java
public class Test {

  public static void main(String[] args) {

	//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();

	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction transaction = session.beginTransaction();
	
	Clazz clazz = new Clazz();
	clazz.setCode("j233");
	clazz.setName("java就业班");
	clazz.setBzr("刘老师");
			
	Student s1 = new Student();
	s1.setName("jack");
	s1.setAge(20);
	s1.setEmail("584614151@qq.com");
	s1.setSex('男');
	
	s1.setClazz(clazz);

	//通过 persist 保存学生信息 同时级联保存班级信息
	//session.persist(s1);
	
	Student s2 = new Student();
	s2.setName("rose");
	s2.setAge(21);
	s2.setEmail("584616151@qq.com");
	s2.setSex('男');
	s2.setClazz(clazz);
	
	//session.persist(s2);
			
	//查询班级信息
	Clazz c1 = session.get(Clazz.class, 1);
	System.out.println("班级名字:"+c1.getName());
	
	System.out.println(c1.getStudents());
		
	//提交事务
	transaction.commit();
	session.close();
	
	}
}
```

###  9.一对一关联关系配置

User类：

```java
......
@Entity
@Table(name = "tb_user")
public class User implements Serializable {

	private static final long serialVersionUID = 1L;

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name="c_id")
	private int id;// 用户id

	@Column(name = "tb_name", length = 10, unique = true)
	private String name;// 用户名字
	
	@OneToOne(targetEntity=Card.class,fetch=FetchType.EAGER,cascade=CascadeType.PERSIST)
	@JoinColumn(name="card_id")
	private Card card;

.......
}

```

Card类：
```java
......
@Entity
@Table(name = "tb_card")
public class Card implements Serializable {

	private static final long serialVersionUID = 1L;

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	@Column(name = "tb_code", length = 10, unique = true)
	private String code;

	private String address;

	/*
	 * cascade=CascadeType.PERSIST:保存Card对象的时候，
	 * 如果Card对象中有User对象的信息，那么可以进行级联保存，保存Card的时候同时将User信息进行保存
	 * 
	 * */ 
	@OneToOne(targetEntity=User.class,fetch=FetchType.EAGER,cascade=CascadeType.PERSIST)
	private User user;

.......
}
```
Test类：
```java

public class Test {

	public static void main(String[] args) {

	//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction transaction = session.beginTransaction();

	User user = new User();
	user.setName("jack");

	Card card = new Card();
	card.setCode("0000001...");
	card.setAddress("长沙开福区...");
	//card.setUser(user);
	
	user.setCard(card);
	
	session.persist(user);

	//提交事务
	transaction.commit();
	session.close();
	
	}

}

```

###  10.多对多关联关系配置

Student类:
```java 
.......
@Entity
@Table(name = "tb_student")
public class Student implements Serializable {

	private static final long serialVersionUID = 1L;

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	@Column(name = "tb_name", length = 10, unique = true)
	private String name;
	private int age;
	private char sex;
	private String email;

	@ManyToMany(targetEntity=Course.class,fetch=FetchType.LAZY,cascade=CascadeType.PERSIST)
	@JoinTable(name="tb_s_c",joinColumns= {@JoinColumn(name="s_id")}
	           ,inverseJoinColumns = {@JoinColumn(name="c_id")})
	private List<Course> courses = new ArrayList<>();

.......
}
```
Course类：
```java
......
@Entity
@Table(name = "tb_course")
public class Course implements Serializable {

	private static final long serialVersionUID = 1L;

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name="c_id")
	private int id;// 课程id

	@Column(name = "tb_name", length = 10, unique = true)
	private String name;// 课程名字
	
	private float grade;//课程学分
	
	/*
	 * @ManyToMany:多对多
	 * 
	 * @JoinTable：指定中间表的相关信息
	 *   - name：指定中间表的名字
	 *   - joinColumns：指定当前表在中间表 的 外键信息
	 *   - inverseJoinColumns：指定Student对应的表在中间表中的外键信息
	 * 
	 * */
	@ManyToMany(targetEntity=Student.class,fetch=FetchType.LAZY)
	@JoinTable(name="tb_s_c",joinColumns= {@JoinColumn(name="c_id")}
	           ,inverseJoinColumns = {@JoinColumn(name="s_id")})
	private List<Student> students = new ArrayList<>();

......
  }
```

Test类：
```java
public class Test {

  public static void main(String[] args) {

	//构建数据源
	//Hibernate5创建连接工厂
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Session类似于mybaits中的 sqlSession  底层封装了 Connection
	Session session = sessionFactory.openSession();

	//获取事务对象
	Transaction transaction = session.beginTransaction();

	Student s1 = new Student();
	s1.setName("jack");
	s1.setSex('男');
	s1.setAge(20);
	s1.setEmail("43444442@qq.com");
	
	Student s2 = new Student();
	s2.setName("rose");
	s2.setSex('女');
	s2.setAge(20);
	s2.setEmail("43443242@qq.com");

	Course c1 = new Course();
	c1.setName("java课程");
	c1.setGrade(5);
	
	Course c2 = new Course();
	c2.setName("大数据课程");
	c2.setGrade(5);
	
	Course c3 = new Course();
	c3.setName("人工智能课程");
	c3.setGrade(5);
	
	// jack选择了java课程以及大数据课程
	s1.getCourses().add(c1);
	s1.getCourses().add(c2);
	
	//session.persist(s1);
	
	//rose选择了java课程和人工智能课程
	s2.getCourses().add(c1);
	s2.getCourses().add(c3);
	
	//session.persist(s2);
	
//当数据中已经存在课程的信息之后，学生选择课程 ，课进行如下操作  
	Student s3 = new Student();
	s3.setName("tom");
	s3.setSex('男');
	s3.setAge(20);
	s3.setEmail("43466542@qq.com");
			
	//tom选择了大数据  和  人工智能
	Course cc1 = new Course();
	cc1.setId(2);
	
	Course cc2 = new Course();
	cc2.setId(3);
	
	s3.getCourses().add(cc1);	        
	s3.getCourses().add(cc2);
	
	//session.save(s3);
			
	//id为的6的学生  选择了 java   人工智能
	Student s =session.get(Student.class, 6);
	s.getCourses().add(cc1);
	s.getCourses().add(cc2);

	//提交事务
	transaction.commit();
	session.close();
	
	}

}
```

### 11.简单 hql 单表查询语句

Student类：
```java
@Entity  //声明该类为持久化类，如果不声明的话，Hibernate不会去管理该实体bean
@Table(name="tb_Student")//声明改持久化类与哪一张表相对应  , 如果不用注解 @Table 声明表的信息时，会将类名做为表名
public class Student {
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private int id;

	private String name;

	private int age;
	
......
}
```
为数据表添加数据：
```java
public class HibernateSave {

  public static void main(String[] args) {
	//获取 数据库连接池    Hibernate中的连接池 SessionFactory     Mybatis中的连接池SqlSessionFactory

	//configure()方法默认加载 src下的 hibernate.cfg.xml   
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	//构建数据库连接池    SessionFactory类似于 DataSource
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Mybatis中的连接 SqlSession    Hibernate中的连接叫 Session   底层相当于对  Connection	  
	Session session = sessionFactory.openSession();
	
	//获取事务对象
	Transaction transaction = session.beginTransaction();

	Student s1 = new Student();
	s1.setName("科比");
	s1.setAge(38);
	
	Student s2 = new Student();
	s2.setName("麦迪");
	s2.setAge(38);
	
	Student s3 = new Student();
	s3.setName("詹姆斯");
	s3.setAge(30);
	
	session.save(s3);
	session.save(s2);
	session.save(s1);

	//提交事务
	transaction.commit();	  
	session.close();
	  	  
	}
}

```
Test类：

```java
public class HibernateSelect {

   public static void main(String[] args) {

	//获取 数据库连接池    Hibernate中的连接池 SessionFactory     Mybatis中的连接池SqlSessionFactory
	//configure()方法默认加载 src下的 hibernate.cfg.xml   
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	//构建数据库连接池    SessionFactory类似于 DataSource
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Mybatis中的连接 SqlSession    Hibernate中的连接叫 Session   底层相当于对  Connection
	Session session = sessionFactory.openSession();

	// 1. 查询所有的学生信息
	/*Query<Student> query = session.createQuery("select s from Student s");
	List<Student> stus = query.list();
	System.out.println("stus:"+stus);*/

	/* List<Student> stus = session.createQuery("select s from Student s").list();
	System.out.println("stus:"+stus);*/

	/*List<Student> stus = session.createQuery("from Student").list();
	System.out.println("stus:"+stus);*/
		
	// 3. 只查询学生的年龄
	/*List<Integer> ages = session.createQuery("select age from Student").list();
	System.out.println("ages:"+ages);*/

	/* //给类 声明 别名
	List<Integer> ages = session.createQuery("select s.age from Student s").list();
	System.out.println("ages:"+ages);*/
		
	// 4. 只查询学生的姓名
	/* List<String> names = session.createQuery("select name from Student").list();
	System.out.println("names:"+names);*/

	// select了几次 Object[]数组就是多少 
	// 5. 只查询学生的姓名与年龄
		/* List<Student> stus = session.createQuery("select new Student(age,name) from Student").list();
		System.out.println("stus:"+stus);*/

	/*   List<Object[]> list = session.createQuery("select s.name,s.age from Student s").list();
	for(Object[] obj : list){
		System.out.println(Arrays.toString(obj));
	}*/
			
	// select new 子句 可以改变List集合中放什么
	// 6. select new list
	/*List<List<Object[]>> list = session.createQuery("select new list(s.name,s.age) from Student s").list();
	for(List<Object[]> obj : list){
		System.out.println(obj.size()+"--"+obj+"--"+obj.get(0)+"--"+obj.get(1));
	}*/

	// 7. select new map
	// 注意：as name是别名会作为map的key，s.name的结果会作为map的value
	/*List<Map<Object, Object>> list  =  session.createQuery("select new map(s.name as name,s.age as age) from Student s").list();
	for(Map<Object, Object> map : list){
		System.out.println(map);
	}*/

	//根据学生名字查询学生信息    麦迪
	// 1. 参数查询  方式一
	/* Query query =  session.createQuery("select s from Student s where s.name =: name ");
		query.setParameter("name", "麦迪");
		List<Student> stus = query.list();
	System.out.println("stus:"+stus);*/
		
		/*List<Student> stus = session.createQuery("select s from Student s where s.name =: name ").setParameter("name", "麦迪").list();
		System.out.println("stus:"+stus);*/

	//根据学生名字以及学生年龄查询学生信息
	// 2. 参数查询 方式二     ?可以加上数字  不能加英文字母或中文字母等
	/* List<Student> stus = session.createQuery("select s from Student s where s.name = ?1 and s.age = ?2")
			.setParameter("1", "麦迪").setParameter("2", 38).list();
	System.out.println("stus:"+stus);*/

	// 3. 参数查询 方式三
	/* List<Student> stus = session.createQuery("select s from Student s where s.name = ? and s.age = ?")
				.setParameter(0, "麦迪").setParameter(1, 38).list();
		System.out.println("stus:"+stus);*/

	// 4. 查询年龄大于18岁
	/*List<Student> stus = session.createQuery("select s from Student s where s.age > ? ")
			.setParameter(0, 18).list();
		System.out.println("stus:"+stus);*/

	// 5. 当查询只有一条数据时，可以使用uniqueResult
	/* Student stus = (Student)session.createQuery("select s from Student s where id = ? ")
				.setParameter(0, 1).uniqueResult();
		System.out.println("stus:"+stus);*/
	// 6. order by 排序
	/*List<Student> stus = session.createQuery("from Student order by age asc").list();
	System.out.println("stus:"+stus);*/

	// 7. 最多显示N条记录
	/* List<Student> stus = session.createQuery("from Student ").setMaxResults(2).list();
	System.out.println("stus:"+stus);*/

	// 8. 从第几条开始查询
	/* List<Student> stus = session.createQuery("from Student ").setFirstResult(1).list();
	System.out.println("stus:"+stus);*/

	// 9. 分页查询 
	// 分页，4条数据，每页显示2条，计算得出总页数是2
	// 第一页：setFirstResult(0),setMaxResults(2)
	// 第二页：setFirstResult(2),setMaxResults(2)
	// jsp页面传递第几页[0,1]，每页显示几条
	List<Student> stus = session.createQuery("from Student ").setFirstResult(2).setMaxResults(2).list();
	System.out.println("stus:"+stus);

	}
}	  
```

### 12.hql 多表关联查询

学生表（id、name、sex、age、clazz）和班级表 (id,code,students)

HibernateSelect类：
```java
public class HibernateSelect {

  public static void main(String[] args) {

	//获取 数据库连接池    Hibernate中的连接池 SessionFactory     Mybatis中的连接池SqlSessionFactory
	//configure()方法默认加载 src下的 hibernate.cfg.xml   
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	//构建数据库连接池    SessionFactory类似于 DataSource
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//获取连接    Mybatis中的连接 SqlSession    Hibernate中的连接叫 Session   底层相当于对  Connection  
	Session session = sessionFactory.openSession();

	// 获取学生年龄大于18岁的班级的编号
	findClazzByStudentAge(session);
	
	//根据班级code查找学生信息
	//findStudentsByClazzCode(session);

	}

	// 根据班级code查找学生信息
  private static void findStudentsByClazzCode(Session session) {
	//学生直接通过  inner join 关联班级 属于显示连接
	//String hql = "select  s from Student s inner join s.clazz c where c.code = 'j233'";
	
	//由于学生关联班级的时候，学生是多方，班级是单方  多方关联单方的时候可以将 inner join 省略掉，省略掉之后属于隐式连接
	String hql = "select  s from Student s where s.clazz.code = 'j233'";
	//获取学生信息
	List<Student> students = session.createQuery(hql).list();
	System.out.println("students:"+students);
	}

	// 获取学生年龄大于18岁的班级
  private static void findClazzByStudentAge(Session session) {
	
	// 单方关联多方的时候不可将 inner join 省略掉！！！
	String hql = "select  distinct c.code  from Clazz c  inner join c.students s  where s.age > 18";
	List<String> codes = session.createQuery(hql).list();
	System.out.println("codes:"+codes);
	}

}	  
		
```

### 13.聚合函数

```java
public class HibernateSelect {

  public static void main(String[] args) {

	//获取 数据库连接池    Hibernate中的连接池 SessionFactory     Mybatis中的连接池SqlSessionFactory
	//configure()方法默认加载 src下的 hibernate.cfg.xml   
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	//构建数据库连接池    SessionFactory类似于 DataSource
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();
	//获取连接    Mybatis中的连接 SqlSession    Hibernate中的连接叫 Session   底层相当于对  Connection  
	Session session = sessionFactory.openSession();

	//计算学生年龄平均值
	//double avgAge = (double)session.createQuery("select avg(age) from Student").uniqueResult();
	//System.out.println("avgAge:"+avgAge);
	
	//计算学生总人数
	long num = (long)session.createQuery("select count(*) from Student").uniqueResult();
	System.out.println("num:"+num);
	
	//计算最大值  max   计算最小值 min  计算总和 sum  返回的记录只有一条  因此可以调用 Query对象的  uniqueResult 方法
  }
}	 
```
### 14.二级缓存
```java

public class HibernateSelect_二级缓存session效果测试 {

  public static void main(String[] args) {

	//获取 数据库连接池    Hibernate中的连接池 SessionFactory     Mybatis中的连接池SqlSessionFactory
	//configure()方法默认加载 src下的 hibernate.cfg.xml   
	StandardServiceRegistry standardServiceRegistry = new StandardServiceRegistryBuilder().configure().build();
	//构建数据库连接池    SessionFactory类似于 DataSource
	SessionFactory   sessionFactory = new MetadataSources(standardServiceRegistry).buildMetadata().buildSessionFactory();

	//hibernate中session可以理解成连接  同时也可以理解成  一级缓存		  
	Session session = sessionFactory.openSession();
	
	/*
		* 根据学生id获取学生信息    由于id为1的学生信息在第一级缓存中没有，在第二级缓存中也没有 ，
	因此此处会发送sql语句，并将查询到的数据存放在一级缓存以及二级缓存中
		* */
	Student s1 = session.get(Student.class, 1);//  查询学生（一级 、二级）  以及  班级 （一级） 
	
	//由于一级缓存中已经有学生id为1的学生信息因此此处不会发送sql语句至数据库
	Student s2 = session.get(Student.class, 1);
	
	//关闭  一级 缓存    那么会将一级缓存中的数据清空，释放内存
	session.close();
	
	//重新获取session
	session = sessionFactory.openSession();
	session.beginTransaction();
	
	//获取学生id为1的学生信息，在一级缓存中已经不存在了，但是二级缓存中还有学生id为1的学生信息，因此此处不会发送sql语句
	Student s3 = session.get(Student.class, 1);//此处会发送sql去查询班级的信息，因为Clazz没有开启二级缓存，同时一级缓存中的班级信息已经被清空
	
	/*
		* 获取学生id为2的学生信息，在一级缓存中没有该学生的信息，接着判断二级缓存中是否有该学生的信息，
		* 如果二级缓存中有该学生的信息，则不发送sql语句，如果没有则发送sql语句，并将数据存放在一级缓存以及二级缓存中
		* */
	Student s4 = session.get(Student.class, 2);
  }
}	  

```