---
title: 模拟hibernate框架
date: 2020-01-05 22:23:04
tags: hibernate
categories: 
- javaWeb
- java高级技术
---

### hibernate框架
&emsp;&emsp;作为SSH三大框架之一的Hibernate，是用来把程序的Dao层和数据库打交道用的，它封装了JDBC的步骤，是我们对数据库的操作更加简单，更加快捷。利用Hibernate框架我们就可以不再编写重复的JDBC代码，不再反复的测试我们的SQL语句写的如何。这里这需要我们简单配置，调用框架给我们提供的方法，就可以完成对数据增删改查。

```java
package com.zx.dao;

import java.beans.PropertyDescriptor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import com.sun.xml.internal.bind.v2.model.core.ID;
import com.zx.annotation.Column;
import com.zx.annotation.Id;
import com.zx.annotation.Table;

//模拟Hibernate框架，实现数据的增 删 改 查等操作
public class HibernateDao<T> {

	
	//根据clazz获取表名
	public  String getTableName(Class<?> clazz) {
		
		// 获取类名作为默认表名
		String tableName = clazz.getName();
		// 获取类名上的注解
		Table table = clazz.getAnnotation(Table.class);
		if (table != null) {
			// 类上有表名注解
			tableName = table.name();
		}		
		return tableName;
	}
	
	  /*//定义方法获取列名
	   public  String getColumnName(Field field) {
		  
		   String columnName = field.getName();
		   //获取字段上的注解
		   Column column = field.getAnnotation(Column.class);
		 
		   if(column != null) {
			   columnName = column.name();
		   }
		   
		   return columnName;
		   
	   }*/
	   
	// 保存数据
	public void save(T t) {
         
		Connection con =null;
		PreparedStatement ps = null;
		try {
			
			// 注册数据库连接
			Class.forName("com.mysql.jdbc.Driver");

			// 获取连接
			con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/book", "root", "root");

			// 创建StringBuffer对象来拼接sql语句 "insert into 表名（列1，列2，列3...） values(?,?,?...)"
			StringBuffer sql = new StringBuffer();
			// 获取表名
			String tableName =this.getTableName(t.getClass());
			
			//获取对象的class类型
			Class<?> clazz = t.getClass();
			Field[]  fields = clazz.getDeclaredFields();
			
			sql.append("insert into " + tableName + "(");

			int len = 0;
			// 遍历字段数组,拼接列名
		      for (int i = 0; i < fields.length; i++) {

			// 获取字段上注解为id的
			Id id = fields[i].getAnnotation(Id.class);
			if (id != null) {
					// 该字段为主键
					continue;
			}

			len++;
			String columnName = fields[i].getName();

			// 获取字段上的注解
			Column column = fields[i].getAnnotation(Column.class);
			if (column != null) {
					columnName = column.name();
				}

			sql.append(i == fields.length - 1 ? columnName + ")" : columnName + ",");

			}

			sql.append(" values(");
			// 拼接values()
			for (int i = 0; i < len; i++) {

				sql.append(i == len - 1 ? "?)" : "?,");

			}

			System.out.println("sql:" + sql.toString());

			// 获取PrepareStatement
			 ps = con.prepareStatement(sql.toString());
			int num = 0;
		    for (int i = 0; i < fields.length; i++) {
				/*
				 * fields[i+1].setAccessible(true);
				 * 
				 * ps.setObject(i+1, fields[i+1].get(t));
				 * System.out.println("fields[i].get(t):"+fields[i+1].get(t));
				 */

			 Id id = fields[i].getAnnotation(Id.class);
			 if (id != null) {
					continue;
			 }
			 System.out.println("I:" + i);
			 // 根据字段名以及Class获取属性描述其对象
			 PropertyDescriptor pd = new PropertyDescriptor(fields[i].getName(), clazz);

			 // 获取字段对应的get方法
			 Method getMethod = pd.getReadMethod();

			 // 利用反射技术，调用方法
			 Object val = getMethod.invoke(t);

			 System.out.println("val:" + val);
			 ps.setObject(i, val);

			}

			// 执行sql
			ps.executeUpdate();

		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			if(ps!=null) {				
				try {
					ps.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
	}

	// 删除数据 ,根据id删
	public void delete(Class<?> clazz, int id) {

		Connection con = null;
		PreparedStatement ps = null;

		try {
			// 获取数据库驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获取连接
			con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/book", "root", "root");
			// 准备sql语句 delete from 表名 where id = ？
			// 获取表名
			String tableName = this.getTableName(clazz);

			StringBuffer sql = new StringBuffer();
			sql.append("delete from " + tableName + " where id= ?");
			System.out.println("del:" + sql.toString());
			// 获取prepareStatement
			ps = con.prepareStatement(sql.toString());
			ps.setInt(1, id);
			// 执行sql
			ps.executeUpdate();

		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}
	}

	// 查询 所有的数据
	public List<T> query(Class<?> clazz) {

		Connection con = null;
		PreparedStatement ps = null;
		ResultSet rs = null;
		try {
			// 获取数据库驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获取连接
			con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/book", "root", "root");
			// 准备sql语句 select *from 表名
			// 获取表 名
			String tableName = this.getTableName(clazz);

			StringBuffer sql = new StringBuffer();
			sql.append("select *from " + tableName);
			System.out.println("del:" + sql.toString());
			// 获取prepareStatement
			ps = con.prepareStatement(sql.toString());
			// 执行sql
			rs = ps.executeQuery();

			// 创建集合用来封装数据
			List<Object> list = new ArrayList<>();
		
			// 获取所有字段
			Field[] fields = clazz.getDeclaredFields();

			while (rs.next()) {

			 // 创建实例
			 Object obj = clazz.newInstance();
			 for (int i = 0; i < fields.length; i++) {

			  //根据字段名以及Class获取属性描述其对象
			   PropertyDescriptor pd = new PropertyDescriptor(fields[i].getName(), clazz);

			   Method setMethod = pd.getWriteMethod();

			   Object val = rs.getObject(fields[i].getName());

			   setMethod.invoke(obj, val);

			  }
			  list.add(obj);
			}

		  return (List<T>) list;

		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		return null;
	}

	// 查询数据 根据主键id查询数据
	public T query(Class<?> clazz,int id) {
		Connection con = null;
		PreparedStatement ps = null;
		ResultSet rs = null;
		try {
			// 获取数据库驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获取连接
			con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/book", "root", "root");
			// 准备sql语句 select *from 表名						
			//获取表名
			String tableName =this.getTableName(clazz);

			StringBuffer sql = new StringBuffer();
			sql.append("select *from " + tableName+" where id =? ");
			System.out.println("del:" + sql.toString());
			// 获取prepareStatement
			ps = con.prepareStatement(sql.toString());
			ps.setInt(1, id);
			// 执行sql
			rs = ps.executeQuery();

			// 创建集合用来封装数据
		
			// 获取所有字段
			Field[] fields = clazz.getDeclaredFields();
		
			while (rs.next()) {

			  // 创建实例
			  Object obj = clazz.newInstance();
			  for (int i = 0; i < fields.length; i++) {

				// 根据字段名以及Class获取属性描述其对象
				PropertyDescriptor pd = new PropertyDescriptor(fields[i].getName(), clazz);

				Method setMethod = pd.getWriteMethod();

				Object val = rs.getObject(fields[i].getName());

				setMethod.invoke(obj, val);

				}
				return (T) obj;
			}
		

		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		return null;
	}
	
	//根据id更新数据  update student set sex='男' where id=4;
     public void update(Class<?> clazz,int id) {
    	 
    	 Connection con = null;
    	 PreparedStatement ps = null;
    	 
    	try {
    		// 获取数据库驱动
 		Class.forName("com.mysql.jdbc.Driver");
 		// 获取连接
 		con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/book", "root", "root");
 		// 准备sql语句 select *from 表名
 						
 		// 获取表名
 		String tableName = this.getTableName(clazz);

 		StringBuffer sql = new StringBuffer();
 		sql.append("update  "+ tableName+" set sex='男' where id= ?");
 		System.out.println("del:" + sql.toString());
 		// 获取prepareStatement
 		ps = con.prepareStatement(sql.toString());
 		ps.setInt(1, id);
 		// 执行sql
 		ps.executeUpdate();

 			
		} catch (Exception e) {
			// TODO: handle exception
		}
    	 
     }
}

```