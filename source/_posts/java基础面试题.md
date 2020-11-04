---
title: java基础面试题
date: 2019-12-21 14:44:55
tags:
   面试题
categories:
   java基础面试题
---

    第一题：哪句是编译失败的呢？为什么呢？
	 byte b1=3,b2=4,b;
	 b=b1+b2;//出错,因为在JAVA虚拟机中默认是int类型转换，虚拟机不知道b1，b2 中的数值到底是多少，所有会抛出可能损失精度的错误;
        //虚拟机会把  b1,b2  转换成 int类型在进行运算，两个int类型的数值相加结果任然是int类型，int类型 不能赋值给 byte类型
	b=3+4;//没有错，java虚拟机会计算出结果7然后赋值给b.
		

	第二题：
	byte by = 130;有没有问题?有问题如何解决?结果是多少呢?  byte 的数值范围是 -128~127  130已经超出byte取值范围 运算是会砍掉前面的 三个8位，结果会变成-126，详细计算过程看原码补码反码那篇。

	第三题:哪句是编译失败的呢？为什么呢？
	byte b = 10;//没有错，检查右边没有超过byte的范围（-128到127）
    b = b + 1;//出错,这句java虚拟机会把，b转换成int类型 相当于  int b + int 类型的1 然后赋值给Byte类型 int是4个字节  byte是1个字节 所有会抛出损失精度.
	b++;//这句java虚拟机会做一个自动转换动作  相当于  b=(byte)(b+1)所以不会报错
    b+=1;//同上，如果b是127， 那么加1后变成128了，成了int类型了，超过了byte类型的最大范围了，系统会强转，把int类型的 前面三个高位丢弃，丢弃后，如果剩下的一位中，最高位为1，则取反然后加1，就成了负数了，如果最高位为0，直接把值赋给b 。
<font color='red'>++,--,+=,-=运算，系统会做一个自动转换动作</font>

```java
  常见的算术运算符有哪些?  // + - * / % ++ -- += -= *= /= 
  +运算符的作用有哪些?// 连接符号（当字符串在前面时 必须作为连接符来使用） 算数运算符  
  答:正号,字符串连接符号,算术运算符
  除法和取余的区别? //  / 除法是求商   % 求余数
  ++和--的使用规则?//  a++ 先赋值在运算，  ++a  先运算  再赋值
```
```java
常见的关系运算符有哪些?
答:  ==  > < <= >= !=
关系运算符的结果有什么特点?
答：输出的结果是boolean类型, 要么为 true, 要么为 false.
```
### 基本数据类型的值传递
![](https://i.loli.net/2019/12/21/db7GC36IAwOsVS9.png "基本数据类型的值传递")

### 引用数据类型的值传递   
![](https://i.loli.net/2019/12/21/9qhLmIoZ2xHWaAQ.png "引用数据类型的值传递")


### 1.写一个单例模式

饿汉模式：
```java 
public class Singleton{

    //声明本类一个私有的成员变量
    private static Singleton singleton = new Singleton();

    //构造方法私有
    private Singleton(){

    }

    //提供一个供外界访问的方法, 获取当前类的实例    
    public static  Singleton getSingleton(){

        return singleton;
    }

}

```

懒汉模式：
```java 
public class Singleton{

    //声明本类一个私有的成员变量
    private static Singleton singleton;

    //构造方法私有
    private Singleton(){

    }

    //提供一个供外界访问的方法, 获取当前类的实例, 
    //synchronized:同步锁,可以保证在同一时刻  只有一个线程进入该方法  
    public synchronized static  Singleton getSingleton(){

        if(singleton==null){
            singleton = new Singleton();
        }
        return singleton;
    }

}

```

### 2.冒泡排序的实现

原理：比较两个相邻的元素，将值大的元素交换至右端。

思路：依次比较相邻的两个数，将小数放在前面，大数放在后面。即在第一趟：首先比较第1个和第2个数，将小数放前，大数放后。然后比较第2个数和第3个数，将小数放前，大数放后，如此继续，直至比较最后两个数，将小数放前，大数放后。重复第一趟步骤，直至全部排序完成。

第一趟比较完成后，最后一个数一定是数组中最大的一个数，所以第二趟比较的时候最后一个数不参与比较；

第二趟比较完成后，倒数第二个数也一定是数组中第二大的数，所以第三趟比较的时候最后两个数不参与比较；

依次类推，每一趟比较次数-1；

……

举例说明：要排序数组：int[] arr={6,3,8,2,9,1};   

第一趟排序：

　　　　第一次排序：6和3比较，6大于3，交换位置：  3  6  8  2  9  1

　　　　第二次排序：6和8比较，6小于8，不交换位置：3  6  8  2  9  1

　　　　第三次排序：8和2比较，8大于2，交换位置：  3  6  2  8  9  1

　　　　第四次排序：8和9比较，8小于9，不交换位置：3  6  2  8  9  1

　　　　第五次排序：9和1比较：9大于1，交换位置：  3  6  2  8  1  9

　　　　第一趟总共进行了5次比较， 排序结果：      3  6  2  8  1  9

---------------------------------------------------------------------

第二趟排序：

　　　　第一次排序：3和6比较，3小于6，不交换位置：3  6  2  8  1  9

　　　　第二次排序：6和2比较，6大于2，交换位置：  3  2  6  8  1  9

　　　　第三次排序：6和8比较，6大于8，不交换位置：3  2  6  8  1  9

　　　　第四次排序：8和1比较，8大于1，交换位置：  3  2  6  1  8  9

　　　　第二趟总共进行了4次比较， 排序结果：      3  2  6  1  8  9

---------------------------------------------------------------------

第三趟排序：

　　　　第一次排序：3和2比较，3大于2，交换位置：  2  3  6  1  8  9

　　　　第二次排序：3和6比较，3小于6，不交换位置：2  3  6  1  8  9

　　　　第三次排序：6和1比较，6大于1，交换位置：  2  3  1  6  8  9

　　　　第二趟总共进行了3次比较， 排序结果：         2  3  1  6  8  9

---------------------------------------------------------------------

第四趟排序：

　　　　第一次排序：2和3比较，2小于3，不交换位置：2  3  1  6  8  9

　　　　第二次排序：3和1比较，3大于1，交换位置：  2  1  3  6  8  9

　　　　第二趟总共进行了2次比较， 排序结果：        2  1  3  6  8  9

---------------------------------------------------------------------

第五趟排序：

　　　　第一次排序：2和1比较，2大于1，交换位置：  1  2  3  6  8  9

　　　　第二趟总共进行了1次比较， 排序结果：  1  2  3  6  8  9

---------------------------------------------------------------------

最终结果：1  2  3  6  8  9

---------------------------------------------------------------------

由此可见：N个数字要排序完成，总共进行N-1趟排序，每i趟的排序次数为(N-i)次，所以可以用双重循环语句，外层控制循环多少趟，内层控制每一趟的循环次数，即

```java 
for(int i=1;i<arr.length;i++){

    for(int j=1;j<arr.length-i;j++){

    //交换位置
}
```

冒泡排序的优点：每进行一趟排序，就会少比较一次，因为每进行一趟排序都会找出一个较大值。如上例：第一趟比较之后，排在最后的一个数一定是最大的一个数，第二趟排序的时候，只需要比较除了最后一个数以外的其他的数，同样也能找出一个最大的数排在参与第二趟比较的数后面，第三趟比较的时候，只需要比较除了最后两个数以外的其他的数，以此类推……也就是说，没进行一趟比较，每一趟少比较一次，一定程度上减少了算法的量。

用时间复杂度来说：

　　1.如果我们的数据正序，只需要走一趟即可完成排序。所需的比较次数C和记录移动次数M均达到最小值，即：Cmin=n-1;Mmin=0;所以，冒泡排序最好的时间复杂度为O(n)。

　　2.如果很不幸我们的数据是反序的，则需要进行n-1趟排序。每趟排序要进行n-i次比较(1≤i≤n-1)，且每次比较都必须移动记录三次来达到交换记录位置。在这种情况下，比较和移动次数均达到最大值：冒泡排序的最坏时间复杂度为：O(n2) 。

Cmax = n(n-1) / 2 = O(n2)  Mmax = 3n(n-1) / 2 = O(n2)  
综上所述：冒泡排序总的平均时间复杂度为：O(n2) 。

代码实现：
```java 
/*
 * 冒泡排序
 */
public class BubbleSort {
　　public static void main(String[] args) {
　　　　int[] arr={6,3,8,2,9,1};
　　　　System.out.println("排序前数组为：");
　　　　for(int num:arr){
　　　　　　System.out.print(num+" ");
　　　　}
　　　　for(int i=0;i<arr.length-1;i++){//外层循环控制排序趟数
　　　　　　for(int j=0;j<arr.length-1-i;j++){//内层循环控制每一趟排序多少次
　　　　　　　　if(arr[j]>arr[j+1]){
　　　　　　　　　　int temp=arr[j];
　　　　　　　　　　arr[j]=arr[j+1];
　　　　　　　　　　arr[j+1]=temp;
　　　　　　　　}
　　　　　　}
　　　　} 
　　　　System.out.println();
　　　　System.out.println("排序后的数组为：");
 　　　　for(int num:arr){
 　　　　　　System.out.print(num+" ");
 　　　　} 
　　}
 }
```
### 3.重载与重写的区别

1.重载与重写都是java多态性的不同表现，重载是一个类中多态性的表现，重写是父类与子类之间多态性的体现。
2.重载的特点是方法名相同，形参列表不同（参数个数不同、参数类型不同、参数的多类型顺序不同），与方法的返回值类型无关。重写的特点是方法名相同，形参列表也相同，与方法的返回值类型有关。
3.重写 - 两同两小一大  （两同--方法名相同，形参列表也相同，两小--子类的返回值类型要么与父类相同，要么是父类返回值类型的子类、子类抛出的异常类型要么与父类相同，要么是父类抛出的异常类型的子类，一大--子类的访问权限要么比父类的访问权限大，要么相同。）

### 4.常见的 Runtime（运行时）异常

1.ArithmaticException  算数异常
2.ArrayIndexOutOfBoundsException   数组下标越界异常
3.NullPointerException    空指针
4.ClassCastException   类型转换异常
5.IllegalArgumentException   非法参数异常
6.StringIndexOutOfException   字符串下标越界异常

###  5.Error 与 Exception 的区别

1.Error与Exception 都是继承自 Throwable类
2.Error 一般是指虚拟机相关的问题，如系统崩溃、虚拟机出错误、动态链接失败等，这种错误无法恢复或不可能捕获，将导致应用程序中断。通常应用程序无法处理这些错误，因此应用程序不应该试图使用catch块来捕获Error对象。
3.Exception 表示一种设计或实现问题，是可以预料的意外情况，并且应该被捕获并进行相应处理。

###  6.抽象类和接口的区别

abstract可以用来修饰类和方法，不能修饰成员变量。
1.抽象类是用abstract修饰的类，抽象类不能直接创建实例，抽象类中不一定要有抽象方法，抽象类中也可以有普通方法。但一个类中有抽象方法，那必须声明为抽象类。
2.抽象类中的所有抽象方法必须由具体子类进行实现，如果没有都实现，那么该子类也要声明为一个抽象类。
3.接口是可以说是抽象类的特例，接口中所有的方法都必须是抽象方法，接口中的定义的方法默认是用 public abstract 修饰的，接口中定义的成员变量默认是用 public static final 修饰的。
4.抽象类中可以有构造方法，接口中不能有构造方法。
5.抽象类中可以有普通的成员变量，接口中不能有普通的成员变量。
6.抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。
7.抽象类中的抽象方法的访问类型可以是public，protected和默认访问权限。但接口中的抽象方法只能是public类型的，并且默认即为public abstract类型。即使你不写修饰符，默认也是public的。
8.抽象类中可以包含静态方法，接口中不能包含静态方法。
9.抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。
10.一个类可以只能继承一个抽象类，但可以实现多个接口。

###  7.abstract的method是否可同时是static，是否可同时是native，是否可同时是synchronized? 

abstract的method 不可以是static的，因为抽象的方法是要被子类实现的，而static与子类扯不上关系！

native方法表示该方法要用另外一种依赖平台的编程语言实现的，不存在着被子类实现的问题，所以，它也不能是抽象的，不能与abstract混用。

关于synchronized与abstract不能同时使用。因为synchronized修饰一个方法时，表明将会使用该方法的调用者作为同步监视器，但对于一个abstract方法而言，它所在类是一个抽象类，抽象类也无法创建实例，因此也就无法确定synchronized修饰方法时的同步监视器了，因此synchronized与abstract不能同时使用。 

###  8.构造器 Constructor 是否可被 override （重写）

构造器 Constructor 不能被继承， 因此不能被重写 Override， 但可以被重载 Overload。

###  9.String类是否可以被继承？

String类是用final修饰的最终类，不能被继承。

###  10.try {}里有一个 return 语句，那么紧跟在这个 try 后的 finally {}里的 code 会不会被执行，什么时候被执行，在 return 前还是后？

会执行，在 return 前执行。

###  11.两个对象值相同(x.equals(y) == true)，但却可以有不同的 hash code，这句话对不对？

不对，如果两个对象x和y满足 x.equals(y) == true，它们的哈希码（hash code）应当相同。Java对于eqauls方法和hashCode方法是这样规定的：

    (1)如果两个对象相同（equals方法返回true），那么它们的hashCode值一定要相同；

    (2)如果两个对象的hashCode相同，它们并不一定相同。

首先equals方法必须满足自反性（x.equals(x)必须返回true）、对称性（x.equals(y)返回true时，y.equals(x)也必须返回true）、传递性（x.equals(y)和y.equals(z)都返回true时，x.equals(z)也必须返回true）和一致性（当x和y引用的对象信息没有被修改时，多次调用x.equals(y)应该得到同样的返回值），而且对于任何非null值的引用x，x.equals(null)必须返回false。

实现高质量的equals方法的诀窍包括：

1. 使用==操作符检查”参数是否为这个对象的引用”；

2. 使用instanceof操作符检查”参数是否为正确的类型”；

3. 对于类中的关键属性，检查参数传入对象的属性是否与之相匹配；

4. 编写完equals方法后，问自己它是否满足对称性、传递性、一致性；

5. 重写equals时总是要重写hashCode；

6. 不要将equals方法参数中的Object对象替换为其他的类型，在重写时不要忘掉@Override注解。


###  12.当一个对象被当作参数传递到一个方法后，此方法可改变这个对象的属性，并可返回变化后的结果，那么这里到底是值传递还是引用传递？

值传递，java中只有值传递参数，但一个对象实例作为一个参数被传递到方法中时，参数值就是该对象的引用，对象的内容可以在方法中被改变，但对象的引用是永远不会改变的。

###  13.swtich 是否能作用在 byte 上，是否能作用在 long 上，是否能作用在 String 上

switch支持int和枚举类型，可以用char,byte,short,int类型，jdk1.7中支持string类型，但是不支持long类型。


###  14.ArrayList 和 Vector 的区别, HashMap 和 Hashtable 的区别

ArrayList 和 Vector
 1.ArrayList和 Vector 都继承了 List 接口，它们都是有序、元素可重复的集合。
 2.Vector 是线程安全的，性能比较差，ArrayList 是线程不安全的，性能较好。

即使我们要在多线程环境下使用List集合，也应该选择ArrayList，而不是Vector，因为Java还提供了一个Collections工具类，它可以把ArrayList包装成线程安全的集合类。代码如下：

```java 
List list = Collections.synchronizedList(new ArrayList());
```
HashMap与Hashtable的区别主要有如下两点：
  1.HashMap允许使用null作为key或value，而Hashtable不允许。
  2.HashMap是线程不安全的，因此性能较好；但Hashtable是线程安全的，因此性能较差。

实际上，实际在多线程环境下，Java提供了Collections工具类把HashMap包装成线程安全的类，因此依然应该使用HashMap，如下代码所示：
```java
Map map = Collections. synchronizedMap(new HashMap());
```

###  15.GC 是什么? 为什么要有 GC

  GC 是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存回收会导致程序或系统的不稳定甚至崩溃，Java 提供的 GC 功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。

###  16.float 型 float f=3.4 是否正确?

不对，小数后没有加 f 或 F ，默认是 double 类型的数据。double 转 float ,发生强制类型转换，应写成 float f = (float) 3.4 

###  17.String 与 StringBuffer 的区别。

String 的长度是不可变的， StringBuffer 的长度是可变的。如果你对字符串中的内容经常进行操作，特别是内容要修改时，那么使用 StringBuffer，如果最后需要 String，那么使用 StringBuffer 的 toString()方法。

###  18.运行时异常与一般异常有何异同?

异常表示程序运行过程中可能出现的非正常状态， 运行时异常表示虚拟机的通常操作中可能遇到的异常，是一种常见运行错误。 java 编译器要求方法必须声明抛出可能发生的非运行时异常，但是并不要求必须声明抛出未被捕获的运行时异常。

###  19.说出 ArrayList,Vector, LinkedList 的存储性能和特性

  ArrayList 和 Vector 都是使用数组方式存储数据， 此数组元素数大于实际存储的数据以便增加和插入元素， 它们都允许直接按序号索引元素， 但是插入元素要涉及数组元素移动等内存操作， 所以索引数据快而插入数据慢， Vector 由于使用了 synchronized 方法（线程安全），通常性能上较 ArrayList 差， 而LinkedList 使用双向链表实现存储， 按序号索引数据需要进行前向或后向遍历， 但是插入数据时只需要记录本项的前后项即可，所以插入速度较快。

###  20.什么是Java序列化，如何实现Java序列化？或者请解释Serializable接口的作用。

序列化就是一种用来处理对象流的机制，所谓对象流也就是将对象的内容进行流化。可以对流化后的对象进行读写操作，也可将流化后的对象传输于网络之间。序列化是为了解决在对 对象流进行读写操作时所引发的问题。

序列化的实现： 将需要被序列化的类实现 Serializable 接口，该接口没有需要实现的方法， implements Serializable 只是为了标注该对象是可被序列化的， 然后使用一个输出流(如：FileOutputStream)来构造一个 ObjectOutputStream(对象流)对象， 接着， 使用 ObjectOutputStream 对象的 writeObject(Object obj)方法就可以将参数为 obj 的对象写出(即保存其状态)，要恢复的话则用输入流。

序列化对于Java开发非常重要，例如在web开发中，如果对象需要保存在了Session中，Tomcat在某些时候需要把Session中的对象序列化到硬盘，因此放入Session中的对象必须是可序列化的，要么实现Serializable接口，要么实现Externalizable接口。还有，如果一个对象要经过网络传输（比如RMI远程方法调用的形参或返回值），这个对象也应该是可序列化的。

###  21.多线程有几种实现方法,都是什么?同步有几种实现方法,都是什么?

多线程有四种实现方法：
1.继承 Thread 类，重写 run 方法，创建该线程类的实例，并调用 start()方法来启动多线程。
2.实现 Runnable 类，重写 run 方法，创建该线程类的实例，并将该实例作为 target 来创建 Thread 的实例，然后调用Thread对象的 start()方法来启动多线程。
3.实现Callable接口，重写 run 方法，创建该线程类的实例，并将该实例作为 target 来创建 FutureTask的实例，并将该实例作为 target 来创建 Thread 的实例，然后调用Thread对象的 start()方法来启动多线程。

同步有三种实现方法：
1.使用synchronized关键字修饰类或者代码块； （synchronized 保证原子性、可见性和有序性）
2.使用Volatile关键字修饰变量；  （volatile 保证原子性，不保证有序性）
3.在类中加入重入锁。     （保证原子性、可见性和有序性）

代码如下：
```java
1.使用synchronized关键字
//保证原子性和有序性
static class Increase {
        private int i = 0;

        synchronized void increasementAndPrint() {
            System.out.println(i++);
        }
    }

2.使用volatile
//保证原子性，不保证有序性
static class Increase {
    private volatile int i = 0;

    void increasementAndPrint() {
        System.out.println(i++);
    }
}

3.使用重入锁
//保证原子性和有序性
static class Increase {
    private ReentrantLock reentrantLock = new ReentrantLock();
    private  int i = 0;

    void increasementAndPrint() {
        reentrantLock.lock();
        System.out.println(i++);
        reentrantLock.unlock();
    }
}
```
###  22.xml有哪些解析技术？每种技术的特点什么？

DOM、SAX、STAX

DOM : 文档全部载入后再解析，适合对XML的随机访问。
SAX : 边读边解析，适合对XML的顺序访问。

### 打印昨天的当前时刻？

```java 
public class Demo {
 /*
  * Java编程,打印昨天的当前时刻
  */
 public static void main(String[] args){
  Calendar cal = Calendar.getInstance();
  cal.add(Calendar.DATE, -1);
  Date date = cal.getTime();
  SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
  String str = sdf.format(date);
  System.out.println(str);
 }

}
```

###  23.子类和父类的加载顺序

```java 
class A{
    static{
        System.out.print("1");
    }
    public A(){
        System.out.print("A");
    }
}

class B extends A{
    static{
        System.out.print("2");
    }
    public B(){
        System.out.print("B");
    }   
}

public class Hello{
    public static void main(String[] ars){
      A ab = new B(); //执行到此处,结果: （ 12AB ）
      System.out.println("---------");
      ab = new B(); //执行到此处,结果: （  AB  ）
   }
}
```

创建对象时调用顺序如下：

①先初始化父类和子类的静态成员。

②父类非静态成员。

③父类构造方法。

④子类非静态成员。

⑤子类构造方法。

另外需要注意的是，加static的语句块在加载类时就加载初始化了；不加static的语句块即初始化块，是在创建类的对象的时候才加载，在调用构造方法之前要先调用初始化块。

###  24.Oracle语法,每页记录10条 表：CREATE TABLE temp_1(ID INT,NAME VARCHAR2(100))

```java
SELECT rowNumId,ID,NAME from  (SELECT ROWNUM as rowId,ID,NAME from temp_1) t
Where rowNumId between 1 and 10;
```

### 25.SQL语句中IN与EXISTS的区别?

1、适用表的类型不同。
 in是子查询为驱动表，外面的表为被驱动表，故适用于子查询结果集小而外面的表结果集大的情况。
 exists是外面的表为驱动表，子查询里面的表为被驱动表，故适用于外面的表结果集小而子查询结果集大的情况。
2、子查询关联不同。
 exists一般都是关联子查询。对于关联子查询，必须先执行外层查询，接着对所有通过过滤条件的记录，执行内层查询。外层查询和内层查询相互依赖，因为外层查询会把数据传递给内层查询。
 in则一般都是非关联子查询，非关联子查询则必须先完成内层查询之后，外层查询才能介入。
3、执行次数不同。
 IN 语句：只执行一次，确定给定的值是否与子查询或列表中的值相匹配。in在查询的时候，首先查询子查询的表，然后将内表和外表做一个笛卡尔积，然后按照条件进行筛选。所以相对内表比较小的时候，in的速度较快。
 EXISTS语句：执行次数根据表的长度而定。指定一个子查询，检测行的存在。遍历循环外表，然后看外表中的记录有没有和内表的数据一样的。匹配上就将结果放入结果集中。

### 26.一个文件中的数据要在控制台上显示，首先需要（ ）。

A.System.out.print (buffer[i]);      
B.FileOutputStream fout = new FileOutputStream(this.filename);   
C.FileInputStream fin = new FileInputStream(this.filename);  
D.System.in.read(buffer);

    答案：C 一个文件中的数据要在控制台显示，首先需要获取文件中的内容，使用FileInputStream fin = new FileInputStream(this.filename);

### 27.执行语句“int a= '2' ”后，a的值是（ ）

    50，一个简便的记忆法：0：48  A:65  a:97，数字连起来是486597 -> 486 597 -> 486 (486 + 111)

### 28.以下是java concurrent包下的4个类，选出差别最大的一个()

A.Semaphore      
B.ReentrantLock    
C.Future    
D.CountDownLatch   

    答案：C
    A、Semaphore：类，控制某个资源可被同时访问的个数;
    B、ReentrantLock：类，具有与使用synchronized方法和语句所访问的隐式监视器锁相同的一些基本行为和语义，但功能更强大；
    C、Future：接口，表示异步计算的结果；
    D、CountDownLatch： 类，可以用来在一个线程中等待多个线程完成任务的类。

### 29.如果一个list初始化为{5，3，1}，执行以下代码后，其结果为（）？
```java
nums.add(6);
nums.add(0,4);
nums.remove(1);
```
解析：
初始化为{5,3,1}
nums.add(6)后list为{5,3,1,6}
nums.add(0,4)是在0号索引上添加数字4得到list为{4,5,3,1,6}
nums.remove(1)是将1号索引上的5进行remove得到list为{4,3,1,6}

### 30.given the following code,what will be the output?

```java
class Value{
    public int i=15;
}
public class Test{
    public static void main(String argv[]){
        Test t=new Test( );
        t.first( );
    }
 
public void first( ){
    int i=5;
    Value v=new Value( );
    v.i=25;
    second(v,i);
    System.out.println(v.i);
}
 
public void second(Value v,int i){
    i = 0;
    v.i = 20;
    Value val = new Value( );
    v = val;
    System.out.println(v.i+" "+i);
   }
}

```

    答案：15 0 20 ，考察的是值传递与引用传递，Java中原始数据类型都是值传递，传递的是值得副本，形参的改变不会影响实际参数的值， 引用传递传递的是引用类型数据，包括String,数组，列表, map,类对象等类型，形参与实参指向的是同一内存地址，因此形参改变会影响实参的值。
    可能有人会选择B，包括我刚开始也是。总以为v不是已经指向了val了吗？？为什么还是20呢？不应该是15吗？
    其实，原因很简单。现在我们把second（）换一下
    public void second(Value tmp,int i){
        i = 0;
        tmp.i = 20;
        Value val = newValue( );
        tmp = val;
        System.out.println(tmp.i+" "+i);
    }
    这个tmp其实相当于是一个指向原来first中的V这个对象的指针，也就是对v对象的引用而已。但是引用是会改变所指的地址的值的。所以在second中当tmp.i= 20的时候，就把原来first中的v的i值改为20了。接下来，又把tmp指向了新建的一个对象，所以在second中的tmp现在指的是新的对象val，i值为15.当执行完毕second后，在first中在此输出v.i的时候，应为前面second中已经把该位置的i的值改为了20，所以输出的是20.至于疑惑v指向了val，其实只是名字的问题，在second中的v实践也是另外的一个变量，名字相同了而已，这个估计也是纠结的重点。
   
### 30.下面哪段程序能够正确的实现了GBK编码字节流到UTF-8编码字节流的转换：
```java
byte[] src,dst;
```
A.dst=String.fromBytes(src，"GBK").getBytes("UTF-8")   
B.dst=new String(src，"GBK").getBytes("UTF-8")
C.dst=new String("GBK"，src).getBytes()
D.dst=String.encode(String.decode(src，"GBK"))，"UTF-8" )  

<font color='red'>正确答案: B ,先解码再编码,用new String(src，"GBK")解码得到字符串,再用getBytes("UTF-8")得到UTF8编码字节数组</font>

### 31.以下代码的输出结果是？
```java
public class B
{
    public static B t1 = new B();
    public static B t2 = new B();
    {
        System.out.println("构造块");
    }
    static
    {
        System.out.println("静态块");
    }
    public static void main(String[] args)
    {
        B t = new B();
    }
}
```
    正确答案: C   你的答案: A (错误)    
类加载时，<font color='red'>并不是静态块最先初始化,而是静态域，而静态域中包含静态变量、静态块和静态方法,其中需要初始化的是静态变量和静态块.而他们两个的初始化顺序是靠他们俩的位置决定的!</font>
开始时JVM加载B.class，对所有的静态成员进行声明，t1 t2被初始化为默认值，为null，又因为t1 t2需要被显式初始化，所以对t1进行显式初始化，初始化代码块→构造函数（没有就是调用默认的构造函数），咦！静态代码块咋不初始化？因为在开始时已经对static部分进行了初始化，虽然只对static变量进行了初始化，但在初始化t1时也不会再执行static块了，因为JVM认为这是第二次加载类B了，所以static会在t1初始化时被忽略掉，所以直接初始化非static部分，也就是构造块部分（输出''构造块''）接着构造函数（无输出）。接着对t2进行初始化过程同t1相同（输出'构造块'），此时就对所有的static变量都完成了初始化，接着就执行static块部分（输出'静态块'），接着执行，main方法，同样也，new了对象，调用构造函数输出（'构造块'）
总结：程序入口main方法要执行首先要加载类B 2.静态域：分为静态变量，静态方法，静态块。这里面涉及到的是静态变量和静态块，当执行到静态域时，按照静态域的顺序加载。并且静态域只在类的第一次加载时执行 3.每次new对象时，会执行一次构造块和构造方法，构造块总是在构造方法前执行（当然，第一次new时，会先执行静态域，静态域〉构造块〉构造方法） 注意：加载类时并不会调用构造块和构造方法，只有静态域会执行 4.根据前三点，首先加载类B，执行静态域的第一个静态变量，static b1=new B，输出构造块和构造方法（空）。ps:这里为什么不加载静态方法呢？因为执行了静态变量的初始化，意味着已经加载了B的静态域的一部分，这时候不能再加载另一个静态域了，否则属于重复加载 了（静态域必须当成一个整体来看待。否则加载会错乱） 于是，依次static b2 =new B，输出构造块，再执行静态块，完成对整个静态域的加载，再执行main方法，new b，输出构造块。

### 32.以下哪一个正则表达式不能与字符串“https://www.tensorflow.org/”（不含引号）匹配？（）

A.[a-z]+://[a-z.]+/   
B.https[://]www[.]tensorflow[.]org[/]   
C.[htps]+://www.tensorflow.org/   
D.[a-zA-Z.:/]+

    正确答案: B   你的答案: D (错误)
```java
[://]
```
这是个很明显的陷阱
[ ] 里面是不重复的，所以等价于[:/]
如果[ ]后面没有带+号的话，是不会贪婪匹配的，就只能匹配到:/中的其中一个 

### 33.java8中，忽略内部接口的情况，不能用来修饰interface里的方法的有（ ）

A.private
B.public
C.protected
D.static

    正确答案: A C   你的答案: A (错误)

### 33.有关finally语句块说法正确的是（ ）

A.不管catch是否捕获异常，finally语句块都是要被执行的
B.在try语句块或catch语句块中执行到System.exit(0)直接退出程序
C.finally块中的return语句会覆盖try块中的return返回
D.finally 语句块在 catch语句块中的return语句之前执行

    正确答案: A B C   你的答案: A (错误)
A：选项存在疑问，一般情况下不管try{}catch(){}语句块如何结束，finally保证其所包含的语句块最终被执行，但是存在特殊情况：1.try语句没有被执行到，如在try语句之前就返回了；2.在try块中有System.exit(0)），此时finally中的语句块是不会执行的；
B：System.exit(0)。表示将整个虚拟机里的内容都释放，JVM停止工作，此时程序正常退出；
C和D： finally块中的内容会先于try中的return语句执行，如果finall语句块中也有return语句的话，那么直接从finally中返回了，但是不建议在finally中return。