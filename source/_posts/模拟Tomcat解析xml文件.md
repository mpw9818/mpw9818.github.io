---
title: 模拟Tomcat解析xml文件
date: 2019-12-28 17:28:52
tags: .XML文件
categories:
   javaWeb
---
#### 模拟Tomcat解析xml文件
&emsp;&emsp;思路：获取用户输入的路径，获取servlet-mapping 节点，如果用户输入的路径与servlet-mapping 下url-pattern值相同，拿到servelt-name的值,获取所以servlet节点,将拿到servelt-name的值与所以servlet节点下的值比较，相同的话，拿到此servlet下的servlet-class的值。然后可以创建实例了，之后通过反射技术就可以调用service方法。

```java
//通过指定的映射地址执行对应的servlet的service方法
 private static void executeService() {
	
	 //创建 Scanner对象用于获取用户输入的信息
	 Scanner scanner = new Scanner(System.in);
	 System.out.println("请输入访问地址:");
	 String url = scanner.next();

	 try {
		   
	   //通过输入流加载  book.xml
	   InputStream  in = new FileInputStream("src/myweb.xml");
	   //获取文档对象模型
	   Document doc = new SAXReader().read(in);
	   //获取根节点    webapp
	   Element rootele = doc.getRootElement();
	   
	   //获取根节点    webapp 下所有的  servlet-mapping节点
	   List<Element> eles  = rootele.elements("servlet-mapping");
	   
	   //获取根节点    webapp 下所有的  servlet节点
	   List<Element> servlets  = rootele.elements("servlet");

	  for(Element ele : eles) {
		  String text = ele.element("url-pattern").getText();
	     if(text.equals(url)) {
		  //映射地址匹配成功获取对应的servlet-name的文本值
		  String servletName = ele.element("servlet-name").getText();

		  for(Element sele : servlets) {
			  String servletNa = sele.element("servlet-name").getText();
			  
			if(servletName.equals(servletNa)) {
			  //根据对应的servlet的名字   == 》 找到对应的class
			  String classInfo = sele.element("servlet-class").getText();

		       System.out.println("classInfo:"+classInfo);
			
			  //根据类的全限名|包名.类名    获取类Class类型
			  Class<?> clazz = Class.forName(classInfo);
		  	  //创建该类的实例
			  Object obj = clazz.newInstance();
			
			  //获取service方法的 Method对象
			  Method method = clazz.getDeclaredMethod("service",String.class,String.class);
			
			  //利用反射技术调用service方法
			  method.invoke(obj,"jack","rose");
					
			return;
		    }
		  }  
	    }
	  }
	   
	   System.out.println("访问地址不存在： 404");
	   
	} catch (Exception e) {
		e.printStackTrace();
	 }	 
}
```