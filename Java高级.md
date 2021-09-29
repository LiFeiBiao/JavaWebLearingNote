### Java高级

#### 多线程

1. **程序(program)**是为完成特定任务、用某种语言编写的一组指令的集合。即指一 段**静态的代码，静态对象**。

2. **进程(process)**是**程序的一次执行过程**，或是正在运行的一个程序。是一个动态 的过程：有它自身的**产生、存在和消亡**的过程。——生命周期

   - 程序是静态的，进程是动态的
   - **进程作为资源分配的单位，**系统在运行时会为每个进程分配不同的内存区域

3. **线程(thread)**，进程可进一步细化为线程，是一个程序内部的一条执行路径。

   - 若一个进程同一时间并行执行多个线程，就是支持多线程的
   - **线程作为调度和执行的单位**，每个线程拥有独立的**运行栈**和**程序计数器(pc)**，**线程切换的开 销小**
   - 一个进程中的多个线程共享相同的内存单元/内存地址空间它们从同一堆中分配对象，可以 访问相同的变量和对象。这就使得线程间通信更简便、高效。但**多个线程操作共享的系统资 源可能就会带来安全的隐患**


##### **多线程的创建和使用**

- **多线程的创建**

  - **方式一：继承于Thread类**

    1. ```
       1. 创建一个继承于Thread类的子类
       2. 重写Thread类的run() --> 将此线程执行的操作声明在run()中
       3. 创建Thread类的子类的对象
       4. 通过此对象调用start()：作用：①启动当前线程②调用当前线程的run()
       ```

  - 创建Thread类的匿名子类的方式

  - **方式二：实现Runnable接口**

    1. 创建一个实现了Runnable接口的类
    2. 实现类中实现Runnable接口中的抽象方法run()
    3. 创建实现类的对象
    4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
    5. 通过Thread类的对象调用start().
       - 启动线程
       - 调用当前线程的run()——>调用了Runnable类型的target的run()

  - **比较两种线程创建方式**

    1. 开发中优先选用实**现Runnable的方法**创建线程
       - 实现的方式没有类的单继承的局限性
       - 实现的方式更适合来**处理多个线程有共享数据的情况**
    2. 联系：public   class  Thread  implements  Runnable
    3. 相同点：两种方法都**需要重写run()方法**，将**线程要执行的逻辑声明在run()中。**

- Thread类中常用方法

  - 1、start():启用当前线程：调用当前线程的run();
  - 2、run()：重写Thread类中的此方法，创建的线程要执行的操作声明在此方法中
  - 3、currentThread()：静态方法，返回执行当前代码的线程
  - 4、getName():获取当前线程的名字
  - 5、setName():设置当前线程的名字
  - 6、yield():释放当前线程的执行权
  - 7、join():在线程a中调用线程b的join方法，则线程a阻塞，直到线程b完全执行完以后，线程a才结束阻塞状态
  - 8、stop（）：强制线程结束生命周期（已过时）
  - 9、sleep（long  millitime）：让当前线程睡眠指定的millitime毫秒。在指定的millitime毫秒时间内，当前线程处于阻塞状态
  - 10、isAlive():判断当前线程是否存活

- 线程的优先级

  - ```
    * MAX_PRIORITY：10
    * MIN _PRIORITY：1
    * NORM_PRIORITY：5  -->默认优先级
    ```

  - 如何获取和设置当前线程的优先级：

    1. getPriority():获取线程的优先级
    2. setPriority():设置线程的优先级
    3. **高优先级的线程要抢占低优先级线程cpu的执行权。但是只是从概率上讲**，高优先级的线程高概率的情况下执行。并不意味着只有当高优先级的线程执行完以后，低优先级的线程才执行。


##### **线程的生命周期**

1. ![image-20210817091413948](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210817091413948.png)

- **线程的同步**
  1. 在Java中通过同步机制，解决线程的安全问题
  2. **方式一同步代码块**
     - **synchronized(同步监视器){//需要被同步的代码}**
     - **操作共享数据的代码，即为需要被同步的代码**——>不能包含代码多了，也不能包含代码少了
     - 共享数据：多个线程共同
     - **同步监视器**：俗称**锁**任何一个类的对象都可以充当锁
     - 要求：多个线程必须要共用同一把锁。
     - 在实现Runnable接口创建多线程的方式中，可以考虑使用this充当同步监视器
  3. **方式二同步方法**
     - 如果操作共享数据的代码完整的声明在一个方法中，我们将此方法声明同步的。
     - 同步方法仍然涉及到同步监视器，只是不需要显式的声明
     - 非静态的同步方法，同步监视器是this
     - **静态的同步方法，同步监视器是：当前类本身**
  4. 同步的方式，解决了线程的安全问题。--好处；操作同步代码时，只能一个线程参与，其它线程等待，相当于一个单线程的过程，效率低
  
- **线程的死锁**

  1. 死锁的理解：不同的线程占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁
  2.  说明：
     - 出现死锁后不会出现异常，不会出现提示，只是所有的线程处于阻塞状态，无法继续
     - 使用同步时，避免出现死锁

- 解决线程安全问题的方式三：**Lock锁**    ------JDK5.0新增

  1. 实例化ReentrantLock
  2. 调用锁定方法lock();
  3. 调用解锁方法unlock();


##### **synchronized和lock的异同？**

- 都可以解决线程安全问题

- synchronized机制在执行完相应的同步代码以后，**自动释放**同步监视器：Lock需要手动的启动同步（Lock()）**手动结束**同步（unlock()）;

- 优先使用顺序：

  ①**Lock（）**②**同步代码块已经进入了方法体，分配了相应资源**）③**同步方法（在方法体外）**

- **如何解决线程安全问题？有几种方式**

  - 三种方式

- **线程的通信**

  - 三种方法：
    1. **wait():**一旦执行此方法，当前线程进入阻塞状态，并释放同步监视器
    2. **notify():**执行此方法，就会唤醒被wait()的线程，若有多个线程，唤醒优先级最高的线程
    3. **notifyAll():**唤醒所有被wait()的线程。
  - 三种方法必须使用在**同步代码块或同步方法中**
  - 三个方法的**调用者**，必须是**同步代码块或同步方法中的同步监视器**；否则会出现**IllegalMonitorstateException**异常
  - 三个方法定义在java.lang.Object类中


##### **面试题：**

1. **sleep（）和wait()方法的异同**
   - 相同点:一旦执行方法，都可以是当前线程进入**阻塞状态**
   - 不同点：
     - 声明的位置不同：**Thread类中声明sleep()方法**；Object类中声明wait()方法
     - 调用的范围不同：sleep()可以在任何需要的场景下调用。**wait()必须使用在同步代码块或同步方法中**
     - **关于是否释放同步监视器**：如果两个方法都使用在同步代码块或同步方法中，sleep()**不会释放锁**，wait（）会释放锁

- **JDK5.0新增线程创建方式**
  1. 方式一：**实现Callable接口**
     - 相比run()方法，可以有返回值
     - 方法可以抛出异常
     - 支持泛型的返回值
     - 需要借助FutureTask类，比如获取返回结果
  2. 方式二：**使用线程池**
     1. 好处：
        - **提高响应速度（减少创建新线程时间）**
        - **降低资源消耗**（**重复利用线程池中线程**，不需要每次都创建）
        - **便于线程管理**
          - **corePoolSize**:核心池的大小
          - **maximumPoolSize**:最大线程数
          - **keepAliveTime**:线程没有任务时最多保持多长时间后会终止

5. 一个Java应用程序java.exe,其中至少有三个线程：**main()主线程，gc()垃圾回收线程，异常处理线程**。如果发生异常，会影响主线程。

#### 常用类

##### 字符串相关的类

1. String：字符串	使用一对“”引起来表示
   - string声明为final，不可被继承
   - string实现了Serializable接口：表示字符串是支持序列化的
   - 实现类Comparable接口：表示String可以比较大小
   - String内部定义了final  char[]   value用于存储字符串数据
   - String：代表不可变的字符序列。简称：不可变性
     - **当对字符串重新赋值时**，需要重写指定内存区域赋值，不能使用原有value赋值
     - **当对现有的字符串进行连接操作时**，需要重写指定内存区域赋值，不能使用原有value修改
     - 当调用string的replace方法修改指定字符或字符串时，需要重写指定内存区域赋值，不能使用原有value修改
   - 通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在**字符串常量池**中
   - 字符串常量池中不会存储相同内容的字符串的。
   
2. String：实例化方式
   - 方式一：通过字面量定义的方式：**方法区的字符串常量池中**
   - 方式二;通过new + 构造器的方式：数据在**堆空间**中
   - 面试题：String s = new String("abc");方式创建对象，在内存中创建了几个对象？
                 两个:一个是堆空间中new结构，另一个是char[]对应的常量池中的数据："abc"
   - 结论：
     - 常量与常量的拼接结果在常量池中，且常量池中不会存在相同内容的常量
     - 只要其中有一个是变量，结果就在堆中。
     - 如果拼接的结果调用intern()方法，返回值就在常量池中
   
3. **String与基本数据类型和包装类**之间的转换
   - String——>基本数据类型，包装类，调用包装类的静态方法：parseXxx(str)
   
   - 基本数据类型，包装类——>String,调用String重载的valueOf(xxx)
   
   - ```java
         public void test1(){
             String str1 = "123";
     //        int num = (int)str1;//错误的
             int num = Integer.parseInt(str1);
     
             String str2 = String.valueOf(num);//"123"
             String str3 = num + "";
     
             System.out.println(str1 == str3);
         }
     ```
   
     
   
4. **String与char[]数组之间的转换**
   - String——>char[],调用String类的toCharArray()
   - char[]——>String，调用String的构造器
   
5. **string常用函数**

   ```java
   StringBuffer的常用方法：
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

   

6. **String与byte[]数组之间的转换**
   - 编码String——>byte[],调用String类的getBytes()
   - 解码：byte[]——>String，调用String类的构造器
     - **编码**：字符串——>字节（看的懂——>看不懂二进制数据）
     - **解码：编码的逆过程**，字节——>字符串（看不懂二进制数据——>看的懂）
     - 解码时要求解码使用的字符集必须与编码时使用的字符集一致，否则会出现乱码

7. **StringBuffer和StringBuilder使用**
   - **String与StringBuffer和StringBuilder异同**
     1. String：不可变字符序列，底层使用char[]存储
     2. StringBuffer:可变的字符序列，**线程安全的，效率低**；底层使用char[]存储
     3. StringBuilder：可变的字符序列，**线程不安全，效率高  jdk5.0新增**；底层使用char[]存储
     4. 三者的效率：StringBuilder  >  StringBuffer   >   String

##### 日期和时间API

1. JDK8.0之前的日期和时间API
   - System类中的currentTimeMillis()  称为**时间戳**
   
2. java.util.Date类

   - java.sql.Date类

3. 两个构造器的使用

   - 构造器一：Date()：创建一个对应当前时间的Date对象
   - 构造器二：创建指定毫秒数的Date对象

4. 两个方法的使用

   - toString():显示当前的年、月、日、时、分、秒
   - getTime():获取当前Date对象对应的毫秒数。（时间戳）

5. java.sql.Date对应着数据库中的日期类型的变量

   如何实例化

   如何将java.util.Date对象转换为java.sql.Date对象

6. jdk 8之前的日期时间的API测试

   - System类中currentTimeMillis();

   - java.util.Date和子类java.sql.Date

   - SimpleDateFormat

     1. 格式化：日期 --->字符串
     2. 解析：格式化的逆过程，字符串 ---> 日期

   - Calendar日历类（抽象类）的使用

     1. 实例化

        方式一：创建其子类（GregorianCalendar）的对象

        方式二：调用其静态方法getInstance()

     2. 常用方法：

        get()

        set()——calendar可变性

        add()

        getTime:日历类——>Date

        setTime:Date —— >日历类

7. jdk 8中日期时间API的测试

   - LocalDate、LocalTime、LocalDateTime 的使用
   - LocalDateTime相较于LocalDate、LocalTime，使用频率要高
   - 类似于Calendar
   - Instant的使用
   - DateTimeFormatter:格式化或解析日期、时间类，似于SimpleDateFormat

##### Java比较器

1. Java中的对象，正常情况下，只能进行比较：==  或  != 。不能使用 > 或 < 的，但是在开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小，如何实现？使用两个接口中的任何一个：Comparable 或 Comparator

2. **Comparable接口与Comparator**的使用的对比：

   Comparable接口的方式一旦一定，**保证Comparable接口实现类的对象在任何位置都可以比较大小**。

   **Comparator接口属于临时性的比较**。

1. **Comparable接口**的使用举例：  **自然排序**
   
   - 像String、包装类等实现了Comparable接口，重写了**compareTo(obj)**方法，给出了比较两个对象大小的方式。
   - 像String、包装类重写compareTo()方法以后，进行了从小到大的排列
   - 重写**compareTo(obj)的规则**：
     - 如果当前对象this**大于**形参对象obj，则返回**正整数，**
     - 如果当前对象this**小于**形参对象obj，则返回**负整数**，
     - 如果当前对象this**等于**形参对象obj，则返回**零**
   - 对于自定义类来说，如果需要排序，我们可以让**自定义类实现Comparable接口，重写compareTo(obj)方法**。
     - 在compareTo(obj)方法中指明如何排序
2. **Comparator**接口的使用：**定制排序**
   
   1. 背景：当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用 **Comparator 的对象来排序**
   2. 重写**compare(Object o1,Object o2)方法，比较o1和o2的大小**：
      - 如果方法返回正整数，则表示o1大于o2；
      - 如果返回0，表示相等；
      - 返回负整数，表示o1小于o2。

##### 枚举类与注解

1. 枚举类的使用

   - 类的对象只有有限个，确定的。此类为枚举类
   - 当需要定义一组常量时，强烈建议使用枚举类。
   - 如果枚举类中只有一个对象，则可以作为单例模式的实现方式
2. 如何定义枚举类

   - 方式一：JDK5.0之前，自定义枚举类
   - 方式二：jdk5.0,可以使用enum关键字定义枚举类
3. enum常用方法

   - values（）返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值。
   - valueOf(String str)可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。如不是，会有运行时异常：IllegalArgumentException。
   - toString()返回当前枚举类对象常量的名称
4. **使用enum关键字定义的枚举类实现接口的情况**

   - 情况一：实现接口，在enum类中实现抽象方法
   - 情况二：让枚举类的对象分别实现接口中的抽象方法

##### Object类

- Object 类是一个特殊的类，是所有类的父类

- ```
  public final native Class<?> getClass()//native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。
  
  public native int hashCode() //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
  public boolean equals(Object obj)//用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。
  
  protected native Object clone() throws CloneNotSupportedException//naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。
  
  public String toString()//返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。
  
  public final native void notify()//native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
  
  public final native void notifyAll()//native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
  
  public final native void wait(long timeout) throws InterruptedException//native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。
  
  public final void wait(long timeout, int nanos) throws InterruptedException//多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。
  
  public final void wait() throws InterruptedException//跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
  
  protected void finalize() throws Throwable { }//实例被垃圾回收器回收的时候触发的操作
  ```



#### 注解

1. 框架 = 注解 + 反射 + 设计模式

2. 理解Annotation

   - jdk 5.0 新增的功能
   - Annotation 其实就是代码里的特殊标记, 这些标记可以在编译, 类加载, 运行时被读取, 并执行相应的处理。通过使用 Annotation,程序员可以在不改变原有逻辑的情况下, 在源文件中嵌入一些补充信息。
   - 在JavaSE中，注解的使用目的比较简单，例如标记过时的功能，忽略警告等。在JavaEE/Android，中注解占据了更重要的角色，例如用来配置应用程序的任何切面，代替JavaEE旧版中所遗留的繁冗，代码和XML配置等。

3. Annocation的使用示例

   - 示例一：生成文档相关的注解

   - 示例二：在编译时进行格式检查(JDK内置的三个基本注解)

     @Override: 限定重写父类方法, 该注解只能用于方法

     @Deprecated: 用于表示所修饰的元素(类, 方法等)已过时。通常是因为所修饰的结构危险或存在更好的选择

     @SuppressWarnings: 抑制编译器警告

   - 示例三：跟踪代码依赖性，实现替代配置文件功能

4. 如何自定义注解，参照@SuppressWarnings定义

   - 注解声明为@interface
   - 内部定义成员，通常使用value，
   - 可以指定成员的默认值，使用default定义
   - 如果自定义注解没有成员，表明是一个标识作用
   - 如果注解有成员，在使用注解时，需要指明成员的值
   - 自定义注解必须配上注解的信息处理流程（使用反射）才有意义

5. jdk提供的**元注解**——修饰其它注解的注解

   - **Retention**：指定所修饰的 Annotation 的生命周期：SOURCE\CLASS（默认行为）\RUNTIME只有声明为RUNTIME生命周期的注解，才能通过反射获取
   - **Target**:用于指定被修饰的 Annotation 能用于修饰哪些程序元素
   - **Documented**:表示所修饰的注解在被javadoc解析时，保留下来。
   - **Inherited**:被它修饰的 Annotation 将具有继承性。

6. 通过反射获取注解信息 ---到反射内容时系统讲解

7. jdk 8 中注解的新特性：可重复注解、类型注解

   - 可重复注解：① 在MyAnnotation上声明@Repeatable，成员值为MyAnnotations.class

     ​						② MyAnnotation的Target和Retention等元注解与MyAnnotations相同。

   - 类型注解：

     - ElementType.TYPE_PARAMETER 表示该注解能写在类型变量的声明语句中（如：泛型声明）。
     - ElementType.TYPE_USE 表示该注解能写在使用类型的任何语句中。


#### Java集合

1. 概述：

   - 集合、数组都是对多个数据进行存储操作的结构，简称**Java容器**。

     说明：此时的存储，主要指的是**内存层面的存储**，不涉及到持久化的存储（.txt,.jpg,.avi，数据库中）

   - **数组**在存储多个数据方面的特点：

     - **一旦初始化以后，其长度就确定了**

     - 数组一旦定义好，其元素的类型也就确定了。我们也就只能操作指定类型的数据了。

       比如：String[] arr;int[] arr1;Object[] arr2;

   - 数组在存储多个数据方面的缺点：

     - 一旦初始化以后，**其长度就不可修改**。
     - 数组中提供的方法非常有限，对于**添加、删除、插入数据**等操作，非常不便，同时效率不高。
     -  获取**数组中实际元素的个数的需求**，数组没有现成的属性或方法可用
     - 数组存储数据的特点：**有序、可重复**。对于无序、不可重复的需求，不能满足。

2. 集合框架：

   ![image-20210925084637034](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210925084637034.png)

   1. |----Collection接口：单列集合，用来存储一个一个的对象
      - |----**List接口**：存储有序的、可重复的数据。  -->“动态”数组
        
        - |----**ArrayList**、**LinkedList**、**Vector**
        
      - |----**Set接口**：存储无序的、不可重复的数据   -->高中讲的“集合”
        
        -  |----**HashSet、LinkedHashSet、TreeSet**
        
      - #### Queue

        -  **PriorityQueue**: Object[]  数组来实现**二叉堆**
        -  **ArrayQueue**: Object[]**数组 + 双指针**
   2. |----Map接口：双列集合，用来存储**一对(key - value)一对的数据**   -->高中函数：y = f(x)

      - |----**HashMap、LinkedHashMap、TreeMap**、Hashtable、Properties

3. 集合元素的遍历使用迭代器Iterator接口

   - 1.内部的方法：**hasNext() 和  next()**

   -  2.集合对象每次调用**iterator()方法都得到一个全新的迭代器对象**，

     默认游标都在集合的第一个元素之前。

   - 内部定义了remove(),可以在遍历的时候，删除集合中的元素。此方法不同于集合直接调用remove()

4. jdk 5.0 新增了foreach循环，用于遍历集合、数组


##### Collection 子接口之List接口

- 存储有序的可重复的数据——>动态数组，替换原有的数组

- 面试题：

  **ArrayList,LinkedList,Vector三者的异同？**

  相同点：三者都是实现List接口，存储数据的特点相同；存储有序的可重复的数据

  不同点：

  - ArrayList：作为List接口的主要实现类；**线程不安全的，效率高**；底层使用Object[] elementData存储
  - LinkedList：对于频繁的插入、删除操作，使用此类效率比ArrayList高；底层使用**双向链表存储**(**JDK1.6 之前为循环链表**，JDK1.7 取消了循环)
  - Vector：作为List接口的古老实现类；**线程安全的，效率低**；底层使用Object[] elementData存储

-  **ArrayList的源码分析：**

  1. **jdk 7情况下**

     - **ArrayList list = new ArrayList()**;//底层创建了**长度是10**的Object[]数组elementData

     - **list.add(123)**;//elementData[0] = new Integer(123);

       。。。。

       list.add(11);//如果此次的添加导致底层elementData数组容量不够，则扩容。

       默认情况下，**扩容为原来的容量的1.5倍**，同时需要将原有数组中的数据复制到新的数组中。

     - 结论：**建议开发中使用带参的构造器**：ArrayList list = new ArrayList(int capacity)

  2. **jdk 8中ArrayList的变化：**

     - ArrayList list = new ArrayList();//底层Object[] elementData初始化为{}.**并没有创建**长度为10的数组

     - list.add(123);//**第一次调用add()时**，**底层才创建了长度10的数组**，并将数据123添加到elementData[0]

       。。。。。

       后续的添加和扩容操作与jdk 7 无异。

  3. 小结：

     - **jdk7**中的ArrayList的对象的创建类似于**单例的饿汉式**，而**jdk8**中的ArrayList的对象的创建类似于**单例的懒汉式**，**延迟了数组的创建，节省内存**

  4. **LinkedList的源码分析：**

     - LinkedList list = new LinkedList(); 内部声明了Node类型的first和last属性，默认值为null

     - list.add(123);//将123封装到Node中，创建了Node对象。

     - 其中，Node定义为：体现了LinkedList的双向链表的说法

       private static class Node<E> {

       ​		E item;

       ​		Node<E> next;

       ​		Node<E> prev;

       ​		Node(Node<E> prev, E element, Node<E> next) {

       ​				this.item = element;

       ​				this.next = next;

       ​				this.prev = prev;

       ​		}

       }

  5. **Vector的源码分析**：jdk7和jdk8中通过Vector()构造器创建对象时，**底层都创建了长度为10的数组**。在扩容方面，默认扩容为原来的**数组长度的2倍。**

- **List接口中的常用方法：**

  - void **add**(int index, Object ele):在index位置插入ele元素
  - boolean **addAll**(int index, Collection eles):从index位置开始将eles中的所有元素添加进来
  - Object **get**(int index):获取指定index位置的元素
  - int **indexOf**(Object obj):返回obj在集合中首次出现的位置
  - int **lastIndexOf**(Object obj):返回obj在当前集合中末次出现的位置
  - Object **remove**(int index):移除指定index位置的元素，并返回此元素
  - Object **set**(int index, Object ele):设置指定index位置的元素为ele
  - List **subList**(int fromIndex, int toIndex):返回从fromIndex到toIndex位置的子集合
  - **常用方法：**
    1. 增：add(Object obj)
    2. 删：remove(int index) / remove(Object obj)
    3. 改：set(int index, Object ele)
    4. 查：get(int index)
    5. 插：add(int index, Object ele)
    6. 长度：size()
    7. 遍历：**① Iterator迭代器方式；② 增强for循环；③ 普通的循环；**

##### Collection 子接口之set接口

- set接口的框架

  - |----Collection接口：单列集合，用来存储一个一个的对象

    ​		|----Set接口：存储无序的、不可重复的数据   -->高中讲的“集合”

    ​				|----**HashSet**：作为Set接口的主要实现类；线程不安全的；可以存储null值

    ​						|----**LinkedHashSet**：作为HashSet的子类；遍历其内部数据时，可以按照添加的顺序遍历，对于频繁的遍历																操作，LinkedHashSet效率高于HashSet.

    ​				|----**TreeSet**：可以按照添加对象的指定属性，进行排序。

- Set接口中没有额外定义新的方法，使用的都是Collection中声明过的方法。

- 要求：

  1. 向Set(主要指：HashSet、LinkedHashSet)中添加的数据，其所在的类一定要重写hashCode()和equals()
  2. 重写的hashCode()和equals()尽可能保持一致性：相等的对象必须具有相等的散列码；
  3. 重写两个方法的小技巧：对象中用作 equals() 方法比较的 Field，都应该用来计算 hashCode 值。

- **Set：存储无序的、不可重复的数据**

  1. 无序性：不等于随机性。存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的。
  2. 不可重复性：保证添加的元素按照equals()判断时，不能返回true.即：相同的元素只能添加一个。

- **添加元素的过程：以HashSet为例：**

  我们向HashSet中添加元素a,首先调用元素a所在类的hashCode()方法，计算元素a的哈希值，此哈希值接着通过某种算法计算出在HashSet底层数组中的存放位置（即为：索引位置），判断数组此位置上是否已经有元素：

  ​		如果此位置上没有其他元素，则元素a添加成功。 --->情况1

  ​		如果此位置上有其他元素b(或以链表形式存在的多个元素），则比较元素a与元素b的hash值：

  ​				如果hash值不相同，则元素a添加成功。--->情况2

  ​				如果hash值相同，进而需要调用元素a所在类的equals()方法：

  ​						equals()返回true,元素a添加失败

  ​						equals()返回false,则元素a添加成功。--->情况3

  对于添加成功的情况2和情况3而言：元素a 与已经存在指定索引位置上数据以链表的方式存储。

  jdk 7 :元素a放到数组中，指向原来的元素。

  jdk 8 :原来的元素在数组中，指向元素a

  **总结：七上八下**

​		**HashSet底层：数组+链表的结构。**

- **LinkedHashSet的使用**

  LinkedHashSet作为HashSet的子类，在添加数据的同时，每个数据还维护了两个引用，记录此数据前一个数据和后一个数据。

  优点：对于频繁的遍历操作，LinkedHashSet效率高于HashSet

- **TreeSet**

  1. 向TreeSet中添加的数据，**要求是相同类的对象。**
  2. 两种排序方式：**自然排序（实现Comparable接口） 和 定制排序（Comparator）**
  3. 自然排序中，比较两个对象是否相同的标准为：**compareTo()返回0**.不再是equals().
  4. 定制排序中，比较两个对象是否相同的标准为：**compare()返回0**.不再是equals().
  
- ### **比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同**

  1. `HashSet` 是 `Set` 接口的主要实现类 ，`HashSet` 的底层是 `HashMap`，**线程不安全的**，可以存储 null 值
  2. `LinkedHashSet` 是 `HashSet` 的子类，能够**按照添加的顺序**遍历；
  3. `TreeSet` 底层使用**红黑树**，元素是**有序**的，排序的方式有**自然排序和定制排序**。

##### Collection 子接口之 Queue

1. **Queue** 

   - **是单端队列**，只能从一端插入元素，另一端删除元素，实现上一般遵循 **先进先出（FIFO）** 规则。
   - `Queue` 扩展了 `Collection` 的接口，根据 **因为容量问题而导致操作失败后处理方式的不同** 可以分为两类方法: 一种在操作失败后会抛出异常，另一种则会返回特殊值。
   - ![image-20210925213935983](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210925213935983.png)

2. **Deque**

   - 是**双端队列**，在队列的两端均可以插入或删除元素。
   - Deque 扩展了 Queue 的接口, 增加了在队首和队尾进行插入和删除的方法，同样根据失败后处理方式的不同分为两类：
   - ![image-20210925214118270](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210925214118270.png)
   - Deque 还提供有 **push() 和 pop()** 等其他方法，可用于模拟栈。

3. ### [ArrayDeque 与 LinkedList 的区别]

   - `ArrayDeque` 和 `LinkedList` 都实现了 **Deque**接口，两者都具有队列的功能，但两者有什么区别呢？
     - `ArrayDeque` 是基于**可变长的数组和双指针**来实现，而 `LinkedList` 则通过**链表**来实现。
     - `ArrayDeque` **不**支持存储 `NULL` 数据，但 `LinkedList` 支持。
     - `ArrayDeque` 是在 JDK1.6 才被引入的，而`LinkedList` 早在 JDK1.2 时就已经存在。
     - `ArrayDeque` 插入时**可能存在扩容过程**, 不过均摊后的插入操作依然为 O(1)。虽然 `LinkedList` **不需要扩容，但是每次插入数据时均需要申请新的堆空间**，均摊性能相比更慢。
   - 从性能的角度上，选用 `ArrayDeque` 来实现队列要比 `LinkedList` 更好。此外，`ArrayDeque` 也可以用于实现栈。

4. ### [PriorityQueue]

   - `PriorityQueue` 是在 JDK1.5 中被引入的, 其与 `Queue` 的区别在于元素**出队顺序是与优先级相关的**，即总是优先级最高的元素先出队。
     - `PriorityQueue` 利用了**二叉堆**的数据结构来实现的，**底层使用可变长的数组**来存储数据
     - `PriorityQueue` 通过**堆元素的上浮和下沉**，实现了在 **O(logn) 的时间复杂度**内插入元素和删除堆顶元素。
     - `PriorityQueue` 是**非线程安全**的，且不支持存储 `NULL` 和 `non-comparable` 的对象。
     - `PriorityQueue` 默认是**小顶堆**，但可以接收一个 `Comparator` 作为构造参数，从而来自定义元素优先级的先后。
   - `PriorityQueue` 在面试中可能更多的会出现在手撕算法的时候，典型例题包括堆排序、求第K大的数、带权图的遍历等，所以需要会熟练使用才行。

##### **Map接口**

- **map实现类的结构**

  |----Map:双列数据，**存储key-value对的数据**   ---类似于高中的函数：y = f(x)

  ​		|----**HashMap**:作为Map的主要实现类；**线程不安全的，效率高**；**存储null的key和value**

  ​				|----**LinkedHashMap**:保证在遍历map元素时，可以按照添加的**顺序实现遍历**。

  ​						原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素。

  ​						对于频繁的遍历操作，此类执行效率高于HashMap。

  ​		|----**TreeMap**:保证**按照添加的key-value对进行排序**，实现排序遍历。此时考虑key的**自然排序或定制排序**；底层使用**红黑树**

  ​		|----**Hashtable**:作为古老的实现类；线程安全的，效率低；**不能**存储null的key和value

  ​				|----Properties:常用来处理配置文件。key和value都是String类型

  HashMap的底层：**数组+链表**  （jdk7及之前）**数组+链表+红黑树 （jdk 8）**

- **面试题：**

  1. HashMap的底层实现原理？
  2. HashMap 和 Hashtable的异同？
  3. CurrentHashMap 与 Hashtable的异同？（暂时不讲）

- **map结构的理解**

  1. Map中的**key:无序的、不可重复的**，使用**Set存储所有的key**  ---> **key所在的类要重写equals()和hashCode()** （以HashMap为例）
  2. Map中的**value:无序的、可重复的，使用Collection存储所有的value** --->value所在的类要重写equals()
  3. 一个键值对：key-value构成了一个Entry对象。
  4. Map中的**entry:无序的、不可重复的**，使用**Set存储所有的entry**

- **HashMap的底层实现原理？以jdk7为例说明：**

  ​		HashMap map = new HashMap():在实例化以后，底层创建了长度是**16**的一维数组Entry[] table。

  ​		...可能已经执行过多次put...

  ​		**map.put(key1,value1):**

  1. 首先，**调用key1所在类的hashCode()**计算key1哈希值，**此哈希值经过某种算法计算以后**，得到在Entry数组中的存放位置。

  ​		如果此位置上的数据为空，此时的key1-value1添加成功。 ---**-情况1**

  ​		如果此位置上的数据不为空，(意味着此位置上存在一个或多个数据(**以链表形式存在**)),比较key1和已经存在的一个或多个数据的**哈希值**：

  ​				如果key1的哈希值与已经存在的数据的**哈希值都不相同**，此时key1-value1添加成功。----**情况2**

  ​				如果key1的哈希值和已经存在的某一个数据(key2-value2)的哈希值相同，继续比较：调用key1所在类的**equals(key2)**方法，比较：

  ​						如果equals()返回false:此时key1-value1添加成功。----**情况3**

  ​						如果equals()返回true:**使用value1替换value2**。

  ​		补充：关于情况2和情况3：此时key1-value1和原来的数据以**链表**的方式存储。

  ​		在不断的添加过程中，会涉及到扩容问题，当超出临界值(且要存放的位置非空)时，扩容。默认的扩容方式：**扩容为原来容量的2倍**，**并将原有的数据复制过来**。

- **jdk8 相较于jdk7在底层实现方面的不同：**

  - **new HashMap()**:底层没有创建一个长度为16的数组
  - jdk 8底层的数组是：**Node[],而非Entry[]**
  - **首次调用put()方法时，底层创建长度为16的数组**
  - jdk7底层结构只有：**数组+链表**。jdk8中底层结构：**数组+链表+红黑树**。
    1. 形成链表时，七上八下（jdk7:新的元素指向旧的元素。jdk8：旧的元素指向新的元素）
    2. 当数组的某一个索引位置上的元素以**链表形式存在的数据个数 > 8 且当前数组的长度 > 64时**，此时此索引位置上的所数据改为使用红黑树存储。
  - **DEFAULT_INITIAL_CAPACITY** : HashMap的**默认容量，16**
  -  **DEFAULT_LOAD_FACTOR**：HashMap的默认**加载因子：0.75**
  - threshold：**扩容的临界值，=容量*填充因子**：16 * 0.75 => 12
  - **TREEIFY_THRESHOLD**：**Bucket中**链表长度大于该默认值，转化为红黑树:8
  - **MIN_TREEIFY_CAPACITY**：桶中的Node**被树化时最小的hash表容量:64**

- **LinkedHashMap的底层实现原理（了解）**

  源码中：

   static class Entry<K,V> extends HashMap.Node<K,V> {

  ​		Entry<K,V> before, after;//能够记录添加的元素的先后顺序

  ​		Entry(int hash, K key, V value, Node<K,V> next) {

  ​				super(hash, key, value, next);

  ​		}

  }

- ### [HashMap 和 Hashtable 的区别]

  - **线程是否安全：** `HashMap` 是非线程安全的，`Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰。（如果你要保证线程安全的话就使用 `ConcurrentHashMap` 吧！）；
  - **效率：** 因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。另外，`Hashtable` 基本被淘汰，不要在代码中使用它；
  - **对 Null key 和 Null value 的支持：** `HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；**Hashtable 不允许有 null 键和 null 值**，否则会抛出 `NullPointerException`。
  - **初始容量大小和每次扩充容量大小的不同 ：** ① 创建时如果不指定容量初始值，`Hashtable` 默认的**初始大小为 11**，之后每次扩充，**容量变为原来的 2n+1**。`HashMap` 默认的**初始化大小为 16**。之后每次扩充，容量变**为原来的 2 倍**。② 创建时如果给定了容量初始值，那么 **Hashtable 会直接使用你给定的大小**，而 `HashMap` 会将其**扩充为 2 的幂次方大小**（`HashMap` 中的`tableSizeFor()`方法保证，下面给出了源代码）。也就是说 `HashMap` 总是使用 **2 的幂**作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。
  - **底层数据结构：** JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当**链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）**时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。

- ### [HashMap 和 HashSet 区别]

  - 如果你看过 `HashSet` 源码的话就应该知道：`HashSet` 底层就是基于 `HashMap` 实现的。（`HashSet` 的源码非常非常少，因为除了 `clone()`、`writeObject()`、`readObject()`是 `HashSet` 自己不得不实现之外，其他方法都是直接调用 `HashMap` 中的方法
  - ![image-20210925224033420](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210925224033420.png)

- ### [HashMap 和 TreeMap 区别]

  - `TreeMap` 和`HashMap` 都继承自`AbstractMap` ，但是需要注意的是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口。
  - ![image-20210925224424866](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210925224424866.png)
  - 实现 `NavigableMap` 接口让 `TreeMap` 有了对集合内元素的**搜索的能力**。
  - 实现`SortedMap`接口让 `TreeMap` 有了对集合中的元素根据键**排序的能力**。默认是按 key 的升序排序，
  - **相比于`HashMap`来说 `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力**

- ### [ConcurrentHashMap 和 Hashtable 的区别]

  - `ConcurrentHashMap` 和 `Hashtable` 的区别主要体现在实现线程安全的方式上不同
    - **底层数据结构：** JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。`Hashtable` 和 JDK1.8 之前的 `HashMap` 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
    - **实现线程安全的方式（重要）：** ① **在 JDK1.7 的时候，`ConcurrentHashMap`（分段锁）** 对整个桶数组进行了分割分段(`Segment`)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 **到了 JDK1.8 的时候已经摒弃了 `Segment` 的概念，而是直接用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 `synchronized` 和 CAS 来操作。（JDK1.6 以后 对 `synchronized` 锁做了很多优化）** 整个看起来就像是优化过且线程安全的 `HashMap`，虽然在 JDK1.8 中还能看到 `Segment` 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。
    - ![image-20210926094129293](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210926094129293.png)
    - ![image-20210926094234571](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210926094234571.png)
    - ![image-20210926094300189](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20210926094300189.png)

- **Map中定义的方法：**

  1. **添加、删除、修改操作**：
     1. Object **put**(Object key,Object value)：将指定key-value添加到(或修改)当前map对象中
     2. void **putAll**(Map m):将m中的所有key-value对存放到当前map中
     3. Object **remove**(Object key)：移除指定key的key-value对，并返回value
     4. void **clear()**：清空当前map中的所有数据
  2. 元素查询的操作：
     1. Object **get**(Object key)：获取指定key对应的value
     2. boolean **containsKey**(Object key)：是否包含指定的key
     3. boolean **containsValue**(Object value)：是否包含指定的value
     4. int **size()**：返回map中key-value对的个数
     5. boolean **isEmpty()**：判断当前map是否为空
     6. boolean **equals**(Object obj)：判断当前map和参数对象obj是否相等
  3. 元视图操作的方法：
     1. Set **keySet()**：返回所有key构成的Set集合
     2. Collection **values()**：返回所有value构成的Collection集合
     3.  Set **entrySet()**：返回所有key-value对构成的Set集合
  4. 总结：常用方法：
     1. 添加：**put**(Object key,Object value)
     2. 删除：**remove**(Object key)
     3. 修改：**put**(Object key,Object value)
     4. 查询：**get**(Object key)
     5. 长度：**size()**
     6. 遍历：**keySet() / values() / entrySet()**

- **TreeMap**

  向TreeMap中添加key-value，要求key必须是由同一个类创建的对象

  因为要按照key进行排序：**自然排序 、定制排序**

1. **Collections工具类的使用**
   1. Collections:操作Collection、Map的工具类
   2. **面试题：Collection 和 Collections的区别？**
      - **reverse(List)**：反转 List 中元素的顺序
      - **shuffle(List)**：对 List 集合元素进行随机排序
      - sort(List)：根据元素的自然顺序对指定 List 集合元素按升序排序
      - sort(List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序
      - swap(List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换
      - Object **max**(Collection)：根据元素的自然顺序，返回给定集合中的最大元素
      - Object **max**(Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最大元素
      - Object min(Collection)
      - Object min(Collection，Comparator)
      - int **frequency**(Collection，Object)：返回指定集合中指定元素的出现次数
      - void **copy**(List dest,List src)：将src中的内容复制到dest中
      - boolean **replaceAll**(List list，Object oldVal，Object newVal)：使用新值替换 List 对象的所有旧值
      - **Collections 类中提供了多个 synchronizedXxx() 方法，该方法可使将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题**

##### 如何选用集合？

主要根据集合的特点来选用，比如我们需要**根据键值获取到元素值**时就选用 `Map` 接口下的集合，需要**排序时**选择 `TreeMap`,**不需要排序**时就选择 `HashMap`,需要保证**线程安全**就选用 `ConcurrentHashMap`。

当我们只需要**存放元素值**时，就选择实现`Collection` 接口的集合，需要**保证元素唯一时**选择实现 `Set` 接口的集合比如 `TreeSet` 或 `HashSet`，不需要就选择实现 `List` 接口的比如 `ArrayList` 或 `LinkedList`，然后再根据实现这些接口的集合的特点来选用。

##### 无序性和不可重复性的含义？

- 什么是无序性？无序性不等于随机性 ，无序性是指存储的数据在**底层数组中并非按照数组索引的顺序添加** ，而是根据数据的哈希值决定的。
- 什么是不可重复性？不可重复性是指添加的元素按照 **equals()判断时 ，返回 false**，需要同时重写 equals()方法和 HashCode()方法。

##### **哈希码**

- 在Java中，**哈希码**代表了**对象的一种特征**，例如我们判断某两个字符串是否==，如果其**哈希码相等，则这两个字符串是相等的**。其次，**哈希码是一种数据结构的算法**。常见的哈希码的算法有：
  - 1：**Object类的hashCode**.返回对象的**内存地址经过处理后的结构**，由于**每个对象的内存地址都不一样**，所以哈希码也不一样。
  - 2：String类的hashCode.根据String类包含的字符串的内容，**根据一种特殊算法返回哈希码，只要字符串内容相同，返回的哈希码也相同。**
  - 3：Integer类，返回的哈希码就是Integer对象里所包含的那个整数的数值，例如Integer i1=new Integer(100),i1.hashCode的值就是100 。由此可见，**2个一样大小的Integer对象，返回的哈希码也一样**。


#### 泛型

1. 泛型的使用

   - jdk5.0新增的特性

   - **集合中使用泛型**

     - 集合接口或集合类在jdk5.0时都修改为带泛型的结构

     - 在实例化集合类时，可以指明具体的泛型类型

     - 指明完以后，在集合类或接口中凡是定义类或接口时，内部结构（比如：方法、构造器、属性等）使用到类的泛型的位置，都指定为实例化的泛型类型。

       比如：add(E e)  --->实例化以后：add(Integer e)

     - 注意点：泛型的类型必须是类，不能是基本数据类型。需要用到基本数据类型的位置，拿包装类替换
     - 如果实例化时，没有指明泛型的类型。默认类型为java.lang.Object类型。

   - **如何自定义泛型结构：泛型类，泛型接口，泛型方法**

     - **泛型方法**：在方法中出现了泛型的结构，**泛型参数与类的泛型参数没有任何关系。**；换句话说，泛型方法所属的类是不是泛型类都没有关系。**泛型方法，可以声明为静态的**。原因：泛型参数是在调用方法时确定的。并非在实例化类时确定。

   - 泛型在继承方面的体现

     - 虽然类A是类B的父类，但是G<A> 和G<B>二者不具备子父类关系，二者是并列关系。
     - 补充：类A是类B的父类，A<G> 是 B<G> 的父类

   - **通配符的使用——"?"**

     - 类A是类B的父类，G<A>和G<B>是没有关系的，二者共同的父类是：G<?>

       - 添加(写入)：对于List<?>就不能向其内部添加数据。除了添加null之外。
       - 获取(读取)：允许读取数据，读取的数据类型为Object。

     - **有限制条件的通配符的使用。**

       - ? extends A:

          G<? extends A> 可以作为G<A>和G<B>的父类，其中B是A的子类

       - ? super A:

          G<? super A> 可以作为G<A>和G<B>的父类，其中B是A的父类

#### **IO流**

1. **File类的使用：**

   - File类的一个对象，代表一个文件或一个文件目录(俗称：文件夹)

   - File类声明在java.io包下

   - File类中涉及到关于文件或文件目录的创建、删除、重命名、修改时间、文件大小等方法，并未涉及到写入或读取文件内容的操作。如果需要读取或写入文件内容，必须使用IO流来完成。

   - 后续File类的对象常会作为参数传递到流的构造器中，指明读取或写入的"终点".

   - **如何创建File类的实例**

     - File(String filePath)

     -  File(String parentPath,String childPath)
     - File(File parentFile,String childPath)

   - **路径**

     - 相对路径：相较于某个路径下，指明的路径。
     - 绝对路径：包含盘符在内的文件或文件目录的路径

   - **路径分割符**

     - windows:\\\
     -  unix:/

   - **常用方法**

     | public String getAbsolutePath()：获取绝对路径                |
     | ------------------------------------------------------------ |
     | public String getPath() ：获取路径                           |
     | public String getName() ：获取名称                           |
     | public String getParent()：获取上层文件目录路径。若无，返回null |
     | public long length() ：获取文件长度（即：字节数）。不能获取目录的长度。 |
     | public long lastModified() ：获取最后一次的修改时间，毫秒值  |
     |                                                              |
     | 如下的两个方法适用于文件目录：                               |
     | public String[] list() ：获取指定目录下的所有文件或者文件目录的名称数组 |
     | public File[] listFiles() ：获取指定目录下的所有文件或者文件目录的File数组 |
     | public boolean renameTo(File dest):把文件重命名为指定的文件路径 |

     比如：file1.renameTo(file2)为例：要想保证返回true,需要file1在硬盘中是存在的，且file2不能在硬盘中存在。

     | public boolean isDirectory()：判断是否是文件目录 |
     | ------------------------------------------------ |
     | public boolean isFile() ：判断是否是文件         |
     | public boolean exists() ：判断是否存在           |
     | public boolean canRead() ：判断是否可读          |
     | public boolean canWrite() ：判断是否可写         |
     | public boolean isHidden() ：判断是否隐藏         |

     创建硬盘中对应的文件或文件目录

     | More Actionspublic boolean createNewFile() ：创建文件。若文件存在，则不创建，返回false |
     | ------------------------------------------------------------ |
     | public boolean mkdir() ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的上层目录不存在，也不创建。 |
     | public boolean mkdirs() ：创建文件目录。如果此文件目录存在，就不创建了。如果上层文件目录不存在，一并创建 |

     删除磁盘中的文件或文件目录

     | public boolean delete()：删除文件或者文件夹 |
     | ------------------------------------------- |
     | 删除注意事项：Java中的删除不走回收站。      |

2. **IO流原理及流的分类**

   - 操作数据单位：字节流、字符流
   - 数据的流向：输入流、输出流
   - 流的角色：节点流、处理流

3. **流的体系结构**

   抽象基类               节点流（或文件流）                                        缓冲流（处理流的一种）

   InputStream        FileInputStream   (read(byte[] buffer))        BufferedInputStream (read(byte[] buffer))

   OutputStream     FileOutputStream  (write(byte[] buffer,0,len)  BufferedOutputStream (write(byte[] buffer,0,len) / flush()

   Reader                  FileReader (read(char[] cbuf))                       BufferedReader (read(char[] cbuf) / readLine())

   Writer                   FileWriter (write(char[] cbuf,0,len)                BufferedWriter (write(char[] cbuf,0,len) / flush()

4. **处理流之一：缓冲流的使用**

   - 缓冲流：

   - | BufferedInputStream    |
     | ---------------------- |
     | * BufferedOutputStream |
     | * BufferedReader       |
     | * BufferedWriter       |

     作用：提供流的读取、写入的速度；提高读写速度的原因：**内部提供了一个缓冲区**

     **处理流，就是“套接”在已有的流的基础上。**

5. **处理流之二**：**转换流**

   - 转换流：属于字符流
     - InputStreamReader将一个**字节**的输入流转换为**字符**的输入流
     - OutputStreamWriter将一个**字符**的输出流转换为**字节**的输出流
   - 作用：提供字符流与字节流之间的转换
   - **解码：**字节，字节数组——>字符数组，字符串 
   - **编码**：字符数组，字符串——>字节，字节数组
   - 字符集：
     - ASCII：美国标准信息交换码。用一个字节的7位可以表示。
     - ISO8859-1：拉丁码表。欧洲码表，用一个字节的8位表示。
     - GB2312：中国的中文编码表。最多两个字节编码所有字符
     - GBK：中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码
     - Unicode：国际标准码，融合了目前人类使用的所有字符。为每个字符分配唯一的字符码。所有的文字都用两个字节来表示
     - UTF-8：变长的编码方式，可用1-4个字节来表示一个字符。

6. **其他流**

   - **标准的输入输出流**
     - System.in:标准的输入流，默认从键盘输入
     - System.out:标准的输出流，默认从控制台输出
     -  System类的setIn(InputStream is) / setOut(PrintStream ps)方式重新指定输入和输出的流。
     
   - **打印流：PrintStream 和PrintWriter**
     - 提供了一系列重载的print() 和 println()
     
   - **数据流：DataInputStream 和 DataOutputStream**
     - 作用：用于读取或写出基本数据类型的变量或字符串
     
   - **对象流**

     - ObjectInputStream 和 ObjectOutputStream

     - 作用：用于存储和读取基本数据类型数据或对象的处理流。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。

     - **序列化机制：**

       对象序列化机制允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。当其它程序获取了这种二进制流，就可以恢复成原来的Java对象。

     - **自定义类需要满足如下的要求，方可序列化**

       1.需要实现接口：Serializable（这是一个标识接口）

       2.当前类提供一个全局常量：serialVersionUID

       3.除了当前Person类需要实现Serializable接口之外，还必须保证其内部所有属性也必须是可序列化的。（默认情况下，基本数据类型可序列化）

       ObjectOutputStream和ObjectInputStream不能序列化**static和transient**修饰的成员变量

   - **随机存取文件流**

     - **RandomAccessFile的使用**
       - RandomAccessFile直接继承于java.lang.Object类，实现了DataInput和DataOutput接口
       - RandomAccessFile既可以作为一个输入流，又可以作为一个输出流
       - 如果RandomAccessFile作为输出流时，写出到的文件如果不存在，则在执行过程中自动创建。
       - 如果写出到的文件存在，则会对原有文件内容进行覆盖。（默认情况下，从头覆盖）
       -  可以通过相关的操作，实现RandomAccessFile“插入”数据的效果

   - **jdk 7.0 时，引入了 Path、Paths、Files三个类。**


#### 网络编程

1. 网络编程中有两个主要的问题：

   - **如何准确地定位网络上一台或多台主机；定位主机上的特定的应用**
   - **找到主机后如何可靠高效地进行数据传输**

2. 网络编程中的两个要素：

   - 对应问题一：**IP和端口号**
   - 对应问题二：提供网络通信协议：TCP/IP参考模型（**应用层、传输层、网络层、物理+数据链路层**）

3. 通信要素一：IP和端口号

   - IP:唯一的标识 Internet 上的计算机（通信实体）

   - 在Java中使用InetAddress类代表IP

   - IP分类：IPv4 和 IPv6 ; 万维网 和 局域网

   - 域名:   www.baidu.com   www.mi.com  www.sina.com  www.jd.com   www.vip.com

   - 本地回路地址：127.0.0.1 对应着：localhost

   - 如何实例化InetAddress:两个方法：getByName(String host) 、 getLocalHost()

     ​										两个常用方法：getHostName() / getHostAddress()

   - 端口号：正在计算机上运行的进程。要求：不同的进程有不同的端口号；范围：被规定为一个 16 位的整数 0~65535。

   - 端口号与IP地址的组合得出一个网络套接字：Socket


#### 反射

1. 反射作用：

   1. 调用类的私有结构：比如私有的**构造器，方法，属性**

2. 疑问

   1. 疑问1：通过直接new的方式或反射的方式都可以调用公共的结构，开发中到底用那个？

      建议：直接new的方式。

      什么时候会使用：反射的方式。 反射的特征：动态性

   2. 疑问2：反射机制与面向对象中的封装性是不是矛盾的？如何看待两个技术？

      不矛盾

3. 关于java.lang.class类的理解

   1. **类的加载过程：**

      程序经过javac.exe命令以后，会生成一个或多个字节码文件(.class结尾)。接着我们使用java.exe命令对某个字节码文件进行解释运行。相当于将某个字节码文件加载到内存中。此过程就称为类的加载。加载到内存中的类，我们就称为**运行时类**，此运行时类，就作为Class的一个实例。

   2. 换句话说，**Class的实例就对应着一个运行时类**

   3. **加载到内存中的运行时类**，会缓存一定的时间。在此时间之内，我们可以通**过不同的方式来获取此运行时类。**

   4. 万事万物皆对象？对象.xxx,File,URL,反射，前端，数据库操作

4. **获取Class的实例的 方式**：

   1. 方式一：**调用运行时类的属性：.class**
   2. 方式二：通过运行时类的**对象,调用getClass()**
   3. 方式三：调用**Class的静态方法：forName(String classPath)**
   4. 使用类的加载器**：ClassLoader  (了解)**

5. **通过反射创建运行时类的对象**

   ​	**newInstance():调用此方法，创建对应的运行时类的对象。内部调用了运行时类的空参的构造器。**

   ​	 要想此方法正常的创建运行时类的对象，**要求**：

   ​	1.运行时类必须提供**空参的构造器**

   ​	2.空参的构造器的访问权限得够。通常，设置为**public**。

6. 在javabean中要求提供一个**public的空参构造器**。原因：

   1. 便于通过反射，创建运行时类的对象
   2. 便于子类继承此运行时类时，默认调用super()时，保证父类有此构造器

7. **反射的动态性**

8. 通过反射获取运行时类的整体结构

   1. 获取**属性**结构

      **getFields()**:获取当前运行时类及其父类中声明为public访问权限的属性

      **getDeclaredFields()**:获取当前运行时类中声明的所有属性。**（不包含父类中声明的属性）**

   2. 获取**方法**结构

      1. **getMethods()**:获取当前运行时类及其所有父类中声明为public权限的方法
      2. **getDeclaredMethods()**:获取当前运行时类中声明的所有方法。**（不包含父类中声明的方法）**

   3. 获取**构造器**结构

      1. **getConstructors()**:获取当前运行时类中声明为public的构造器
      2. **getDeclaredConstructors()**:获取当前运行时类中声明的所有的构造器

   4. 获取**运行时类的父类**

   5. 获取**运行时类的接口**

   6. 获取运行时类所在的包

9. **调用运行时类的指定结构（属性，方法）**

#### 静态代理

```java
package com.atguigu.java;

/**
 * 静态代理举例
 * 特点：代理类和被代理类在编译期间，就确定下来了。
 */
interface ClothFactory{
    void produceCloth();
}

//代理类
class ProxyClothFactory implements ClothFactory{
    private ClothFactory factory;//用被代理类对象进行实例化
    public ProxyClothFactory(ClothFactory factory){
        this.factory = factory;
    }
    @Override
    public void produceCloth() {
        System.out.println("代理工厂做一些准备工作");
        factory.produceCloth();
        System.out.println("代理工厂做一些后续的收尾工作");
    }
}

//被代理类
class NikeClothFactory implements ClothFactory{
    @Override
    public void produceCloth() {
        System.out.println("Nike工厂生产一批运动服");
    }
}
public class StaticProxyTest {
    public static void main(String[] args) {
        //创建被代理类的对象
        ClothFactory nike = new NikeClothFactory();
        //创建代理类的对象
        ClothFactory proxyClothFactory = new ProxyClothFactory(nike);
        proxyClothFactory.produceCloth();

    }
}
```

#### 动态代理

1. 想实现动态代理需要解决的问题：
   1. 如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象？
   2. 当通过代理类的对象调用方法时，如何动态的去调用被代理类中的同名方法？
   
2. ```java
   package com.atguigu.java;
   
   import java.lang.reflect.InvocationHandler;
   import java.lang.reflect.Method;
   import java.lang.reflect.Proxy;
   
   /**
    * 动态代理的举例
    */
   
   interface Human{
       String getBelief();
       void eat(String food);
   }
   //被代理类
   class SuperMan implements Human{
       @Override
       public String getBelief() {
           return "I believe I can fly!";
       }
       @Override
       public void eat(String food) {
           System.out.println("我喜欢吃" + food);
       }
   }
   
   class HumanUtil{
       public void method1(){
           System.out.println("====================通用方法一====================");
       }
       public void method2(){
           System.out.println("====================通用方法二====================");
       }
   }
   
   /*
   要想实现动态代理，需要解决的问题？
   问题一：如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象。
   问题二：当通过代理类的对象调用方法a时，如何动态的去调用被代理类中的同名方法a。
    */
   
   class ProxyFactory{
       //调用此方法，返回一个代理类的对象。解决问题一
       public static Object getProxyInstance(Object obj){//obj:被代理类的对象
           MyInvocationHandler handler = new MyInvocationHandler();
           handler.bind(obj);
           return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),handler);
       }
   }
   
   class MyInvocationHandler implements InvocationHandler{
   
       private Object obj;//需要使用被代理类的对象进行赋值
       public void bind(Object obj){
           this.obj = obj;
       }
       //当我们通过代理类的对象，调用方法a时，就会自动的调用如下的方法：invoke()
       //将被代理类要执行的方法a的功能就声明在invoke()中
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           HumanUtil util = new HumanUtil();
           util.method1();
           //method:即为代理类对象调用的方法，此方法也就作为了被代理类对象要调用的方法
           //obj:被代理类的对象
           Object returnValue = method.invoke(obj,args);
           util.method2();
           //上述方法的返回值就作为当前类中的invoke()的返回值。
           return returnValue;
       }
   }
   
   public class ProxyTest {
       public static void main(String[] args) {
           SuperMan superMan = new SuperMan();
           //proxyInstance:代理类的对象
           Human proxyInstance = (Human) ProxyFactory.getProxyInstance(superMan);
           //当通过代理类对象调用方法时，会自动的调用被代理类中同名的方法
           String belief = proxyInstance.getBelief();
           System.out.println(belief);
           proxyInstance.eat("四川麻辣烫");
   
           System.out.println("*****************************");
   
           NikeClothFactory nikeClothFactory = new NikeClothFactory();
   
           ClothFactory proxyClothFactory = (ClothFactory) ProxyFactory.getProxyInstance(nikeClothFactory);
   
           proxyClothFactory.produceCloth();
   
       }
   }
   ```

#### Java8新特性

1. **lambda表达式**

   - 举例： (o1,o2) -> Integer.compare(o1,o2);
   - 格式：
     - lambda操作符 或 箭头操作符
     - 左边：lambda形参列表 （其实就是接口中的抽象方法的形参列表）
     - 右边：lambda体 （其实就是重写的抽象方法的方法体）
   - Lambda表达式的使用：（分为6种情况介绍）
   - 左边：lambda形参列表的参数类型可以省略(类型推断)；如果lambda形参列表只有一个参数，其一对()也可以省略
   - 右边：lambda体应该使用一对{}包裹；如果lambda体只有一条执行语句（可能是return语句），省略这一对{}和return关键字
   - Lambda表达式的本质：作为函数式接口的实例
   - 如果一个接口中，只声明了一个抽象方法，则此接口就称为函数式接口。我们可以在一个接口上使用 @FunctionalInterface 注解，这样做可以检查它是否是一个函数式接口。
   - 所以以前用**匿名实现类**表示的现在都可以用Lambda表达式来写。

2. **java内置的4大核心函数式接口**

   - 消费型接口 Consumer<T>     void accept(T t)
   - 供给型接口 Supplier<T>     T get()
   - 函数型接口 Function<T,R>   R apply(T t)
   - 断定型接口 Predicate<T>    boolean test(T t)

3. **方法引用和构造器引用**

   - **方法引用**的使用：
     1. 使用情境：当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用！
     2. 方法引用，本质上就是Lambda表达式，而Lambda表达式作为函数式接口的实例。所以方法引用，也是函数式接口的实例
     3. 使用格式：  类(或对象) :: 方法名
     4. 具体分为如下的三种情况：
        - 情况1     对象 :: 非静态方法
        - 情况2     类 :: 静态方法
        - 情况3     类 :: 非静态方法
     5. 方法引用使用的要求：要求接口中的抽象方法的形参列表和返回值类型与方法引用的方法的形参列表和返回值类型相同！（针对于情况1和情况2）
   - **构造器**引用
     - 和方法引用类似，函数式接口的抽象方法的形参列表和构造器的形参列表一致。
     - 抽象方法的返回值类型即为构造器所属的类的类型
   - **数组**引用
     - 大家可以把数组看做是一个特殊的类，则写法与构造器引用一致。

4. **强大的StreamAPI**

   1. **Stream**关注的是对数据的运算，与CPU打交道，**集合**关注的是数据的存储，与内存打交道

   2. **Stream 执行流程**

      - Stream的实例化

      - 一系列的中间操作（过滤、映射、...)

      - 终止操作

   3. 说明：

      1.  一个中间操作链，对数据源的数据进行处理
      2. 一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用

   4. 概述

      - Stream 自己**不会存储元素**。
      - Stream 不会**改变源对象**。相反，他们会返回一个持有结果的新Stream。
      - Stream 操作是**延迟执行**的。这意味着他们会等到需要结果的时候才执行

   5. 函数操作：

      1. **映射**
         1. **limit(n)**——截断流，使其元素不超过给定数量。
         2.  **skip(n)** —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
         3. **distinct()**——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
         4. **map(Function f)**——接收一个函数作为参数，将元素转换成其他形式或提取信息，该函数会被应用到每个元素上，并将其映射成一个新的元素。
         5.  **flatMap(Function f)**——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。
      2. **排序：**
         1. **sorted()**——自然排序
         2. **sorted(Comparator com)**——定制排序
      3. **Stream的终止操作**
         1. **匹配与查找**
            1. **allMatch(Predicate p)**——检查是否匹配所有元素。
            2. **anyMatch(Predicate p)**——检查是否至少匹配一个元素。
            3. **noneMatch(Predicate p)**——检查是否没有匹配的元素。
            4.  **findFirst**——返回第一个元素
            5. **findAny**——返回当前流中的任意元素
            6. **count**——返回流中元素的总个数
            7. **max(Comparator c)**——返回流中最大值
            8. **min(Comparator c)**——返回流中最小值
            9. **forEach(Consumer c)**——内部迭代
         2. **归约**
            1. **reduce(T identity, BinaryOperator)**——可以将流中元素反复结合起来，得到一个值。返回 T
            2. **reduce(BinaryOperator)** ——可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
         3. **收集**

5. **Optional类**

   1.  Optional类：为了在程序中避免出现空指针异常而创建的
   2. 操作：
      1. Optional.of(T t) : 创建一个 Optional 实例，t必须非空；
      2. Optional.empty() : 创建一个空的 Optional 实例
      3. Optional.ofNullable(T t)：t可以为null

