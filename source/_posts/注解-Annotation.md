---
title: 注解--Annotation
date: 2019-12-30 20:34:36
tags: 注解
categories:
- javaWeb
- java高级技术
---

▲所有Java类都是Object的子类。
▲所有Annotation都是java.lang.annotation.Annotation的子类
▲Annotation通常作用在package、Class、Field、Method上面。

### 元注解
JDK提供了几个特殊的Annotation，称为元Annotation，只能用在Annotation上面，元Annotation就是用于修饰注解的注解。比较常用的元Annotation，比如@Target、@Retention

    @Target(vaule= ElementType.TYPE)
    @Target注解表示定义的Annotation作用在哪里。
    ElementType.TYPE：只能作用在class、interface、Enum上面。
    ElementType.Field：只能作用在Field上面
    ElementType.Method：只能作用在Method上面
<font color='red'>如果不指定@Target,那么意味着作用位置不受限制</font>    

    @Retention注解表示Annotation的保留策略
    RetentionPolicy.Class：运行时不保留，不可以通过反射读取。
    RetentionPolicy.RUNTIME：运行是保留，可以通过反射读取。自定义注解90%是这种。
    RetentionPolicy.SOURCE：丢弃。


### 自定义注解类编写的一些规则
  1. Annotation定义为@interface, 所有的Annotation会自动继承java.lang.annotation.Annotation这一接口,并且不能再去继承别的类或是接口.
  2. 参数成员只能用public或默认(default)这两个访问权修饰
  3. 参数成员只能用基本数据类型和String、Enum、Class、annotations等数据类型,以及这一些类型的数组.
  4. 要获取类方法和字段的注解信息，必须通过Java的反射技术来获取 Annotation对象,除此之外没有别的获取注解对象的方法

```java
package com.zx.注解;

import java.lang.reflect.Method;

public class Test {

	public static void main(String[] args) {
		
	  //获取注解中静态成员的值
	  boolean f = MyOverride.flag;
	  System.out.println("f:"+f);
	  //获取People的class类型
	  Class<?> clazz = People.class;	
	  
	  //获取People类上的MyOverride注解实例
	  MyOverride myOverride = clazz.getAnnotation(MyOverride.class);
	  String name = myOverride.name();
	  System.out.println("name:"+name);
	  
	  String val = myOverride.value();
	  System.out.println("val:"+val);
	  
      //注解作用在字段上，获取字段信息
	  try {
		  Method method = clazz.getMethod("getId");
		  MyOverride override = method.getAnnotation(MyOverride.class);
		  System.out.println("override.name():"+override.value());
	  } catch (Exception e) {
		// TODO: handle exception
		e.printStackTrace();
	   }
		  
	}

}

```

```java
package com.zx.注解;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Method;

@Target(value=ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyOverride {

	 //静态   final成员变量
	 public  boolean flag = true;
	 
	 //实例成员变量
	 public String name() default "jack";
	 
	 public String value() default "123";

}

```

```java
package com.zx.注解;

@MyOverride("abc")
public class People {

	private  int id;
    private String userName;
	   
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getUserName() {
		return userName;
	}
	public void setUserName(String userName) {
		this.userName = userName;
	}
	   	   
}

```