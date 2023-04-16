## Comparable&Comparator

**在Java中经常会涉及到对象数组的排序问题，那么就涉及到对象之间的比较问题。**

Java实现对象排序的方式有两种： 

- **自然排序：**java.lang.Comparable
- **定制排序：**java.util.Comparator



1. **Java比较器的使用背景：**

   Java中的对象，正常情况下，只能进行比较：==  或  != 。不能使用 > 或 < 的
   但是在开发场景中，我们需要对**多个对象进行排序**，言外之意，就需要**比较对象的大小**。
   如何实现？使用两个接口中的任何一个：**Comparable 或 Comparator**

2. **自然排序：使用Comparable接口**

   1. **说明：**

      1. 像**String、包装类**等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象大小的方式。**不用考虑这些类的compareTo(obj)重写**
      2. 像String、包装类重写compareTo()方法以后，进行了**从小到大的排列**
      3. 重写compareTo(obj)的规则：
          如果当前对象this大于形参对象obj，则返回正整数，
          如果当前对象this小于形参对象obj，则返回负整数，
          如果当前对象this等于形参对象obj，则返回零。
      4. 对于自定义类来说，如果需要排序，我们可以**让自定义类实现Comparable接口**，**重写compareTo(obj)方法**。在compareTo(obj)方法中指明如何排序

   2. **自定义类代码举例：**

      ```java
      public class Goods implements  Comparable{
      
          private String name;
          private double price;
      
          //指明商品比较大小的方式:照价格从低到高排序,再照产品名称从高到低排序
          @Override
          public int compareTo(Object o) {
      //        System.out.println("**************");
              if(o instanceof Goods){
                  Goods goods = (Goods)o;
                  //方式一：
                  if(this.price > goods.price){
                      return 1;
                  }else if(this.price < goods.price){
                      return -1;
                  }else{
      //                return 0;
                     return -this.name.compareTo(goods.name);
                  }
                  //方式二：
      //           return Double.compare(this.price,goods.price);
              }
      //        return 0;
              throw new RuntimeException("传入的数据类型不一致！");
          }
      // getter、setter、toString()、构造器：省略
      }
      
      ```

3. **定制排序：使用Comparator接口**

   1. **说明**

      1. 背景：当元素的类型没实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用 Comparator 的对象来排序
      2. 重写compare(Object o1,Object o2)方法，比较o1和o2的大小：
         如果方法返回正整数，则表示o1大于o2；
         如果返回0，表示相等；
         返回负整数，表示o1小于o2。

   2. **代码举例：**

      ```java
      Comparator com = new Comparator() {
          //指明商品比较大小的方式:照产品名称从低到高排序,再照价格从高到低排序
          @Override
          public int compare(Object o1, Object o2) {
              if(o1 instanceof Goods && o2 instanceof Goods){
                  Goods g1 = (Goods)o1;
                  Goods g2 = (Goods)o2;
                  if(g1.getName().equals(g2.getName())){
                      return -Double.compare(g1.getPrice(),g2.getPrice());
                  }else{
                      return g1.getName().compareTo(g2.getName());
                  }
              }
              throw new RuntimeException("输入的数据类型不一致");
          }
      }
      
      使用：
      Arrays.sort(goods,com);
      Collections.sort(coll,com);
      new TreeSet(com);
      ```

4. **两种排序方式对比**

   1. Comparable接口的方式一旦一定，保证Comparable接口实现类的对象在任何位置都可以比较大小。
   *    Comparator接口属于临时性的比较。