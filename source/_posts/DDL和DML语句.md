---
title: DDL和DML语句
date: 2019-12-18 22:08:14
tags: oracle数据库
categories:
  数据库
---
Transaction Control:事务控制,COMMIT、ROLLBACK、SAVEPOINT等

1.SQL语句主要可以划分为以下几类：
&emsp;&emsp;DDL(Data Definition Language):数据定义语言，定义对数据库对象(库、表、列、索引)的操作。CREATE、DROP、ALTER、RENAME、 TRUNCATE等
&emsp;&emsp;DML(Data Manipulation Language): 数据操作语言，定义对数据库记录的操作。INSERT、DELETE、UPDATE
&emsp;&emsp;DCL(Data Control Language): 数据控制语言，定义对数据库、表、字段、用户的访问权限和安全级别。GRANT、REVOKE等


2.在数据库系统中，SQL语句不区分大小写(建议用大写) 。但字符串常量区分大小写。SQL语句可单行或多行书写，以“;”结尾。

-- 字符串常量(数据)区分大小写
SELECT * FROM Dba_Users;
SELECT * FROM Dba_Users WHERE username='J1510';
SELECT * FROM Dba_Users WHERE username='j1510';


## 常用的数据类型
数值型：int  number
字符型：
char：固定长度字符。CHAR(8),存入“jack”，数据库开辟8个字节的空间存储数据。速度快
varchar：可变长度。varchar(8),存入“jack”，数据库开辟4个字节的空间存储数据。节省空间
varchar2：可变长度。oracle不建议存储空字符，建议存储null，为了oracle的兼容性，推荐使用。
日期型：date

## 创建表
创建  表  表名(
列名[字段]  列类型(长度),
列名  列类型(长度),
列名  列类型(长度)  
);

CREATE TABLE tb_user(
NAME VARCHAR2(18),
sex CHAR(3),
age INT
);
注意：列和列之间用","隔开，最后一个列不需要","

-- 查询表
-- 查询 所有列 从  表名 (命令语言，编程语言)
SELECT * FROM tb_user;


-- 修改  Alter
-- 增加两个字段address,phone
-- 修改 表  表名  add （列 类型[长度],列 类型[长度]）
ALTER TABLE tb_user ADD(address VARCHAR2(100),phone VARCHAR2(21));


-- 删除字段
-- 修改 表  表名  DROP （列）
ALTER TABLE tb_user DROP (address,phone);


-- 修改字段的长度
-- 修改 表  表名  MODIFY （列 类型[长度]）
ALTER TABLE tb_user MODIFY (address VARCHAR2(200));

-- 修改列名
-- 修改 表  表名  RENAME COLUMN 旧列名 to  新列名
ALTER TABLE tb_user RENAME COLUMN phone TO iphone6s;


-- 修改表名
-- 改名  旧表名 to  新表名
RENAME tb_user TO tb_student;
RENAME tb_student TO tb_user;

-- 删除表
-- 删除  表  表名
-- 注意：drop 会删除表结构和表所有数据，并且不能恢复，慎用
DROP TABLE tb_user;


-- 注释  简单了解
-- 注释  on 表  表名  is  '自己的注释'
COMMENT  on table tb_user is '用户表';
COMMENT  on column tb_user.name is '姓名';


--  用户.表名[对象名]
-- 默认查询当前用户的表
SELECT * FROM tb_user;
SELECT * FROM j1510.tb_user;
SELECT * FROM scott.emp;



-- 数据字典分类  
-- dba:所有
-- ALL:用户所能访问的所有对象
-- user:当前用户
SELECT * FROM user_tables;
SELECT * FROM all_tables;
SELECT * FROM dba_tables;


-- 查询scott用户的dept表，前提是有查询scott表的权限
SELECT * FROM scott.dept;







