1. **System类**

   1. System类代表系统，系统级的很多属性和控制方法都放置在该类的内部。该类位于java.lang包。

   2. 由于该类的构造器是private的，所以无法创建该类的对象，也就是无法实例化该类。其内部的成员变量和成员方法都是static的，所以也可以很方便的进行调用。**理解为工具类**

   3. 方法：

      ```java
      //系统目前日期时间
      native long currentTimeMillis()
      //退出程序，参数为退出状态
      void exit(int status)
      //垃圾回收
      void gc()
      //获取属性，属性名从参数key传入
      String getProperty(String key)
      ```

      

2. **Math类**

   1. java.lang.Math提供了一系列静态方法用于科学计算。其方法的参数和返回值类型一般为double型。

3. **BigInteger&BigDecimal**

   **说明：**
   ① java.math包的BigInteger可以**表示不可变的任意精度的整数**

   ② 要求**数字精度比较高**，用到java.math.BigDecimal类**适用于浮点数据类型**

   代码举例：

   ```java
   @Test
       public void test2() {
           BigInteger bi = new BigInteger("1243324112234324324325235245346567657653");
           BigDecimal bd = new BigDecimal("12435.351");
           BigDecimal bd2 = new BigDecimal("11");
           System.out.println(bi);
   //         System.out.println(bd.divide(bd2));
           System.out.println(bd.divide(bd2, BigDecimal.ROUND_HALF_UP));
           System.out.println(bd.divide(bd2, 25, BigDecimal.ROUND_HALF_UP));
   
       }
   ```

   



