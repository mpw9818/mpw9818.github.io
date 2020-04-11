---
title: XML文件创建&解析&更新&删除
date: 2019-12-28 15:58:06
tags: .XML文件
categories:
   javaWeb
---
## 什么是XML？
&emsp;&emsp;XML,可扩展标记语言。XML语言出现的根本目标在于如何准确有效的描述有关系的数据。Xml技术是w3c组织发布的，目前推荐遵循的是W3C组织于2000发布的XML1.0规范。W3C并非一个由官方所批准的标准组织，它的成员由IBM、Microsoft这样的公司所组成。W3C只是发布推荐标准，但是由于该组织自身的权威性往往成为事实上的标准。

## XML和HTML的区别 
```html
HTML例子
<table></table> 有开头有结尾
<p>、<br> 只有开头,由此可见，html标记并不是非常严谨的
HTML是专门用来在页面显示数据的，更多的是排版工作。

XML例子：
<lcl></lcl>  xml有开头必须有结尾
```

## XML的作用
1.<font color='red'>数据存储</font>：可以使用XML文件存储数据。
2.<font color='red'>信息配置</font>：XML技术除用于保存数据之外，它还经常用作软件配置文件，在web开发中，到处可见XML配置文件，web项目web.xml，Struts2、Hibernate、Spring都使用XML做配置。
3.<font color='red'>数据交换</font>：通过网络交换数据的时候，XML是最佳方案，因为任何的语言的都支持XML的生成以及解析。微信支付有用到。

## 语法规则
```java
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```
（1）.xml的第一行一定是xml的定义，包括版本和字符编码。
（2）note称为根元素（节点）,一个xml必须有并且只有一个根元素。
（3）所有 XML 元素都须有关闭标签|结束标签    。
（4）标签对大小写敏感。
（5）必须正确地嵌套。
（6）<to>是note的子元素,子元素下面还可以有子元素。
（7）属性值须加引号。

## XML约束
&emsp;&emsp;在XML技术里，可以编写一个文档来约束一个XML文档的书写规范，这称之为XML约束。

### 为什么需要XML约束？
&emsp;&emsp;对于一个格式良好的XML文档，我们只能保证这个文档的格式符合XML规范，但是元素与元素的关系、元素与属性的关系，属性的取值是否正确，我们就无法得知了。如果仅仅是在有限的应用中使用，或者作为配置文件，数据的存储传输，它是能很好的满足我们的应用。但是作为和其他应用的数据交换，就有必要提供一种机制，保证XML文档结构，元素与元素之间的关系，属性的取值是否符合要求了。

### 两种约束
1.DTD(Document Type Definition)，全称为文档类型定义吗，功能有限，现在不用了。
2.<font color='red'>Schema</font>，功能强大，DTD的替代者。
>相对DTD而言，XML Schema具有如下优势：
1.XML Schema 基于 XML 编写，可读性更好。
2.支持为元素内容或属性值指定数据类型，功能更完善，更强大。
3.可针对未来的需求进行扩展。
4.XML Schema 支持命名空间。
5.XML Schema 是 W3C 标准。

### XML解析的API --jar包
XML解析需要包jar包
（1）JAXP： sun公司提供的API
（2）jdom: 一个解析XML的开源框架,解析XML很方便.里面大多数都是抽象类和类。
（3）<font color='red'>dom4j</font>: 一个解析XML的开源框架,解析XML很方便.里面大多数都是接口，比jdom更加灵活。
（4）JAXB解析 : Java API For XML Binding，JDK 5的时候自动的一个API，能够自动实现XML和Java对象的转换。

## DOM4J操作XML案例

```java
package com.lx;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
//import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;

public class Test {

	public static void main(String[] args) {

	// 生成xml文件
	createXml();
	// 解析xml文件
	readXml();
	// 更新节点信息
	update();
	// 删除xml中节点信息
	delete();
}

// 生成xml文件
public static void createXml() {

	FileOutputStream os = null;
	try {
		// 创建一个空文档(Document对象)，用来封装数据
		Document document = DocumentHelper.createDocument();
		// 创建根元素
		Element root_node = document.addElement("books");
		// 创建第一个book元素
		Element book_node = root_node.addElement("book");
		book_node.addAttribute("id", "1");
		book_node.addElement("name").setText("西游记");
		book_node.addElement("author").setText("吴承恩");
		book_node.addElement("desc").setText("中国古典四大名著之一");
		// 第二个book元素
		Element book_node2 = root_node.addElement("book");

		book_node2.addAttribute("id", "2");// 给book添加属性 key 值
		book_node2.addElement("name").setText("水浒传");
		book_node2.addElement("author").setText("施耐庵");
		book_node2.addElement("desc").setText("108条好汉");

		// 写出到指定的位置包括文件名 ，这里仅仅是将文件生成，还没有内容
		os = new FileOutputStream("src/book.xml");

		// 格式化对象，可以不进行格式化，那么默认使用utf-8
		/*
		 * OutputFormat of = OutputFormat.createPrettyPrint(); of.setEncoding("GBK");
		 */

		// 构建xml的输出对象，拿到输出流像os指向的路径写出
		XMLWriter writer = new XMLWriter(os);
		// 将document中封装的内容写出
		writer.write(document);

	} catch (Exception e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}

}

// 解析xml文件
private static void readXml() {

	FileInputStream in = null;
	try {
		// 通过输入流加载book.xml
		in = new FileInputStream("src/book.xml");
		// 获取文档对象模型
		Document doc = new SAXReader().read(in);
		// 获取根节点 books
		Element rootele = doc.getRootElement();
		// 获取根节点下的所有子节点 book
		List<Element> books = rootele.elements();

		for (Element ele : books) {
			// 获取booked节点下name节点的文本值
			String name = ele.element("name").getText();
			System.out.println("name:" + name);
			// 获取booke节点的id属性值
			String idVal = ele.attribute("id").getText();
			System.out.println("idVal:" + idVal);

		}

	} catch (Exception e) {
		// TODO: handle exception
		e.printStackTrace();
	}

}

// 更新xml中节点信息
public static void update() {

	InputStream in = null;
	OutputStream out = null;
	try {

		// 通过输入流加载book.xml
		in = new FileInputStream("src/book.xml");
		// 获取文档对象模型
		Document document = new SAXReader().read(in);
		// 获取文档根节点
		Element root_node = document.getRootElement();
		// 获取根节点下所有的子节点
		List<Element> book_node = root_node.elements();
		// 遍历所有子节点
		for (Element book : book_node) {

			Attribute id = book.attribute("id");
			if ("1".equals(id.getValue())) {
				id.setValue("001");

			}
			// 将book节点下子节点name文本值 为西游记的改成大话西游
			Element name_node = book.element("name");
			if ("西游记".equals(name_node.getText())) {

				name_node.setText("大话西游");
			}
		}
		// 拿到输出流，写出新文件
		out = new FileOutputStream("src/book.xml");

		// 格式化对象，可以不进行格式化，那么默认使用utf-8
		/*
		 * OutputFormat of = OutputFormat.createPrettyPrint(); of.setEncoding("GBK");
		 */

		// 构建xml的输出对象
		XMLWriter writer = new XMLWriter(out);
		writer.write(document);

	} catch (Exception e) {
		e.printStackTrace();
	}

}

// 删除xml中节点以及属性
private static void delete() {

	InputStream in = null;
	OutputStream os = null;
	try {

		// 指定需要解析的xml文件的路劲
		in = new FileInputStream("src/bool.xml");
		// 加载xml至内存
		Document document = new SAXReader().read(in);
		// 获取根节点
		Element root_node = document.getRootElement();
		// 获取根节点下的所有子节点
		List<Element> book_node = root_node.elements();

		for (Element book : book_node) {
			// 获取id属性
			Attribute id = book.attribute("id");
			if ("1".equals(id.getValue())) {
				// 删除id属性
				book.remove(id);
			}

			// 获取author 元素
			Element author = book.element("author");
			if ("吴承恩".equals(author.getText())) {
				// 删除author 元素
				book.remove(author);
			}

		}

		// 写出路径
		os = new FileOutputStream("src/book.xml");
		// 格式化对象，可以不进行格式化，那么默认使用utf-8
		/*
		 * OutputFormat of = OutputFormat.createPrettyPrint(); of.setEncoding("GBK");
		 */

		// 构建xml的输出对象
			XMLWriter writer = new XMLWriter(os);
			writer.write(document);

		} catch (Exception e) {
			e.printStackTrace();
		}
	}

}

```