**常用类主要内容：**

1. 字符串相关的类
2. JDK8之前的日期时间API
3. JDK8中的新日期时间API
4. Java比较器
5. System类
6. Math类
7. BigInteger和BigDecimal



### 内容一：字符串相关的类

#### String类

**String:字符串，使用一对""引起来表示。**

1. String声明为final的，不可被继承
2. String实现了Serializable接口：表示字符串是支持序列化的。
               实现了Comparable接口：表示String可以比较大小
3. String内部定义了final char[] value用于存储字符串数据
4. String:代表不可变的字符序列。简称：不可变性。
        **体现：**
   1. 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值。
   2.  当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
   3. 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
5. 通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在字符串常量池中。
6. 字符串常量池中是不会存储相同内容的字符串的。

##### **String的特性**

-  **String类：代表字符串。**Java 程序中的所有字符串字面值（如 "abc" ）都作为此类的实例实现。 
-  String是一个final类，代表**<u>不可变的字符序列</u>**。 
-  **字符串是常量**，用双引号引起来表示。它们的值在创建之后不能更改。 
-  String对象的字符内容是**存储在一个字符数组value[]**中的。 



**String:代表不可变的字符序列。简称：不可变性。**
        **体现：**

1. 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值。
2. 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
3. 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

![](..\..\image\java高级\Java常用类\String的不可变性.jpg)

**：凡是对一个字符串的任何改变字符串的操作，都必须在常量池新造一个字符串，不会再原有字符串上改变**

**：一个字符串被两个字符串变量指定，两个字符串变量指向的地址值是相等的；其中一个变量修改值时，字符串不被修改，而是另外生成一个新字符串被指向。**



**String类实例化方式：**

- 方式一：通过字面量定义的方式
- 方式二：通过new()+构造器的方式

```java
String str = "hello";
//本质上this.value = new char[0];
String s1 = new String(); 
//this.value = original.value;
String s2 = new String(String original); 
//this.value = Arrays.copyOf(value, value.length);
String s3 = new String(char[] a); 
String s4 = new String(char[] a,int startIndex,int count);
```

**String特性：不同实例化方式对比**

![不同实例化方式对比](..\..\image\java高级\Java常用类\不同实例化方式对比.png)

**面试题：String类通过字面量&通过new＋构造器实例化的方式的区别**

```java
public void test2(){
        //通过字面量定义的方式：此时的s1和s2的数据javaEE声明在方法区中的字符串常量池中。
        String s1 = "javaEE";
        String s2 = "javaEE";
        //通过new + 构造器的方式:此时的s3和s4保存的地址值，是数据在堆空间中开辟空间以后对应的地址值。
        String s3 = new String("javaEE");
        String s4 = new String("javaEE");

        System.out.println(s1 == s2);//true
        System.out.println(s1 == s3);//false
        System.out.println(s1 == s4);//false
        System.out.println(s3 == s4);//false
```



**面试题：String s = new String("text");的方式创建对象，在内存中创建了多少个对象？**

​			**：两个**，一个是在堆空间中new的结构，另一个是char[]对应在常量池中的数据"text"



**String特性：不同拼接操作的对比**

```java
public void test3(){
        String s1 = "javaEE";
        String s2 = "hadoop";

        String s3 = "javaEEhadoop";
        String s4 = "javaEE" + "hadoop";
        String s5 = s1 + "hadoop";
        String s6 = "javaEE" + s2;
        String s7 = s1 + s2;

        System.out.println(s3 == s4);//true
        System.out.println(s3 == s5);//false
        System.out.println(s3 == s6);//false
        System.out.println(s3 == s7);//false
        System.out.println(s5 == s6);//false
        System.out.println(s5 == s7);//false
        System.out.println(s6 == s7);//false

        String s8 = s6.intern();//返回值得到的s8使用的常量值中已经存在的“javaEEhadoop”
        System.out.println(s3 == s8);//true


    }
```

**结论：**

-  常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量。 
-  只要其中有一个是变量，结果就在堆中 
-  如果拼接的结果调用intern()方法，返回值就在常量池中 



**面试题：带有String类型的值传递**

```java
public class StringTest {

    String str = new String("good");
    char[] ch = { 't', 'e', 's', 't' };

    public void change(String str, char ch[]) {
        str = "test ok";//String的不可变性，这个str只是指向good，改变时不影响原值
        ch[0] = 'b';
    }
    public static void main(String[] args) {
        StringTest ex = new StringTest();
        ex.change(ex.str, ex.ch);
        System.out.println(ex.str);//good
        System.out.println(ex.ch);//best
    }
}

```



**String类常用的方法**

```java
int length()：返回字符串的长度： return value.length
char charAt(int index)： 返回某索引处的字符return value[index]
boolean isEmpty()：判断是否是空字符串：return value.length == 0
String toLowerCase()：使用默认语言环境，将 String 中的所有字符转换为小写
String toUpperCase()：使用默认语言环境，将 String 中的所有字符转换为大写
String trim()：返回字符串的副本，忽略前导空白和尾部空白
boolean equals(Object obj)：比较字符串的内容是否相同
boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大小写
String concat(String str)：将指定字符串连接到此字符串的结尾。 等价于用“+”
int compareTo(String anotherString)：比较两个字符串的大小
String substring(int beginIndex)：返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。
String substring(int beginIndex, int endIndex) ：返回一个新字符串，它是此字符串从beginIndex开始截取到endIndex(不包含)的一个子字符串。
    
boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束
boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始
boolean startsWith(String prefix, int toffset)：测试此字符串从指定索引开始的子字符串是否以指定前缀开始

boolean contains(CharSequence s)：当且仅当此字符串包含指定的 char 值序列时，返回 true
int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引
int indexOf(String str, int fromIndex)：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始
int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引
int lastIndexOf(String str, int fromIndex)：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索

注：indexOf和lastIndexOf方法如果未找到都是返回-1

替换：
String replace(char oldChar, char newChar)：返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。
String replace(CharSequence target, CharSequence replacement)：使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。
String replaceAll(String regex, String replacement)：使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。
String replaceFirst(String regex, String replacement)：使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。
匹配:
boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式。
切片：
String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。
String[] split(String regex, int limit)：根据匹配给定的正则表达式来拆分此字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中。
```



#### String&StringBuffer&StringBuilder

**String、StringBuffer、StringBuilder三者的异同？**

- String:不可变的字符序列；**底层使用char[]存储**
- StringBuffer:**可变的字符序列**；**线程安全的**，效率低；底层使用char[]存储
- StringBuilder:**可变的字符序列**；jdk5.0新增的，**线程不安全的**，效率高；底层使用char[]存储



##### StringBuffer&StringBuilder源码分析

**以StringBuffer为例**

- capacity创建StringBuffer的char数组长度

```java
String str = new String();//char[] value = new char[0];
String str1 = new String("abc");//char数组的capacity为16，添加了{'a','b','c'}
//System.out.println(sb1.length());//3
//char[] value = new char[]{'a','b','c'};

StringBuffer sb1 = new StringBuffer();//char[] value = new char[16];底层创建了一个长度是16的数组。--默认capacity为16
System.out.println(sb1.length());//
sb1.append('a');//value[0] = 'a';
sb1.append('b');//value[1] = 'b';

StringBuffer sb2 = new StringBuffer("abc");//char[] value = new char["abc".length() + 16];
```



**问题1**. System.out.println(sb2.length());//3 **默认char数组capacity长度为16，但是StringBuffer的length()方法返回值为3**
**问题2**. 扩容问题:如果要添加的数据底层数组盛不下了，那就需要扩容底层的数组。
             默认情况下，**扩容为原来容量的2倍 + 2**，同时将原有数组中的元素复制到新的数组中。

 **指导意义**：开发中建议大家使用：StringBuffer(int capacity) 或 StringBuilder(int capacity)，**开发中建议使用构造器指定capacity的值，更具实际情况指定capacity，尽量避免底层char数组的扩容**



##### StringBuffer的常用方法：

```java
StringBuffer append(xxx)：提供了很多的append()方法，用于进行字符串拼接
StringBuffer delete(int start,int end)：删除指定位置的内容
StringBuffer replace(int start, int end, String str)：把[start,end)位置替换为str
StringBuffer insert(int offset, xxx)：在指定位置插入xxx
StringBuffer reverse() ：把当前字符序列逆转
public int indexOf(String str)
public String substring(int start,int end):返回一个从start开始到end索引结束的左闭右开区间的子字符串
public int length()
public char charAt(int n )
public void setCharAt(int n ,char ch)
```

**总结：**
        增：append(xxx)
        删：delete(int start,int end)
        改：setCharAt(int n ,char ch) / replace(int start, int end, String str)
        查：charAt(int n )
        插：insert(int offset, xxx)
        长度：length();
        *遍历：for() + charAt() / toString()



##### 对比String、StringBuffer、StringBuilder三者的效率：

​    **从高到低排列：** *StringBuilder > StringBuffer > String*



##### String与StringBuffer、StringBuilder之间的转换

**String -->StringBuffer、StringBuilder:**调用StringBuffer、StringBuilder构造器
**StringBuffer、StringBuilder -->String:**①调用String构造器；②StringBuffer、StringBuilder的toString()



##### JVM中字符串常量池存放位置说明：

jdk 1.6 (jdk 6.0 ,java 6.0):字符串常量池存储在方法区（永久区）
jdk 1.7:字符串常量池存储在堆空间
jdk 1.8:字符串常量池存储在方法区（元空间）