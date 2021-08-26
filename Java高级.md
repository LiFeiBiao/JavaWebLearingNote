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

4. **多线程的创建和使用**

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

       1. 开发中优先选用实现Runnable的方法创建线程
          - 实现的方式没有类的单继承的局限性
          - 实现的方式更适合来处理多个线程有共享数据的情况
       2. 联系：public   class  Thread  implements  Runnable
       3. 相同点：两种方法都需要重写run()方法，将线程要执行的逻辑声明在run()中。

     - 

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

   - **线程的生命周期**

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

   - **synchronized和lock的异同？**

     - 都可以解决线程安全问题

     - synchronized机制在执行完相应的同步代码以后，自动释放同步监视器：Lock需要手动的启动同步（Lock()）手动结束同步（unlock()）;

     - 优先使用顺序：

       ①Lock（）②同步代码块（已经进入了方法体，分配了相应资源）③同步方法（在方法体外）

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

   - **面试题：**

     1. **sleep（）和wait()方法的异同**
        - 相同点:一旦执行方法，都可以是当前线程进入阻塞状态
        - 不同点：
          - 声明的位置不同：Thread类中声明sleep()方法；Object类中声明wait()方法
          - 调用的范围不同：sleep()可以在任何需要的场景下调用。**wait()必须使用在同步代码块或同步方法中**
          - **关于是否释放同步监视器**：如果两个方法都使用在同步代码块或同步方法中，sleep()不会释放锁，wait（）会释放锁

   - **JDK5.0新增线程创建方式**

     1. 方式一：**实现Callable接口**
        - 相比run()方法，可以有返回值
        - 方法可以抛出异常
        - 支持泛型的返回值
        - 需要借助FutureTask类，比如获取返回结果
     2. 方式二：**使用线程池**
        1. 好处：
           - 提高响应速度（减少创建新线程时间）
           - 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
           - 便于线程管理
             - corePoolSize:核心池的大小
             - maximumPoolSize:最大线程数
             - keepAliveTime:线程没有任务时最多保持多长时间后会终止

5. 一个Java应用程序java.exe,其中至少有三个线程：main()主线程，gc()垃圾回收线程，异常处理线程。如果发生异常，会影响主线程。

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
4. **String与char[]数组之间的转换**
   - String——>char[],调用String类的toCharArray()
   - char[]——>String，调用String的构造器
5. **String与byte[]数组之间的转换**
   - 编码String——>byte[],调用String类的getBytes()
   - 解码：byte[]——>String，调用String类的构造器
     - **编码**：字符串——>字节（看的懂——>看不懂二进制数据）
     - **解码：编码的逆过程**，字节——>字符串（看不懂二进制数据——>看的懂）
     - 解码时要求解码使用的字符集必须与编码时使用的字符集一致，否则会出现乱码
6. **StringBuffer和StringBuilder使用**
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

2. Comparable接口与Comparator的使用的对比：

   Comparable接口的方式一旦一定，保证Comparable接口实现类的对象在任何位置都可以比较大小。

   Comparator接口属于临时性的比较。

1. Comparable接口的使用举例：  自然排序
   - 像String、包装类等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象大小的方式。
   - 像String、包装类重写compareTo()方法以后，进行了从小到大的排列
   - 重写compareTo(obj)的规则：
     - 如果当前对象this大于形参对象obj，则返回正整数，
     - 如果当前对象this小于形参对象obj，则返回负整数，
     - 如果当前对象this等于形参对象obj，则返回零
   - 对于自定义类来说，如果需要排序，我们可以让自定义类实现Comparable接口，重写compareTo(obj)方法。
     - 在compareTo(obj)方法中指明如何排序
2. Comparator接口的使用：定制排序
   1. 背景：当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用 Comparator 的对象来排序
   2. 重写compare(Object o1,Object o2)方法，比较o1和o2的大小：
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

5. **注解**

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


##### Java集合

1. 概述：

   - 集合、数组都是对多个数据进行存储操作的结构，简称Java容器。

     说明：此时的存储，主要指的是内存层面的存储，不涉及到持久化的存储（.txt,.jpg,.avi，数据库中）

   - 数组在存储多个数据方面的特点：

     - 一旦初始化以后，其长度就确定了

     - 数组一旦定义好，其元素的类型也就确定了。我们也就只能操作指定类型的数据了。

       比如：String[] arr;int[] arr1;Object[] arr2;

   - 数组在存储多个数据方面的缺点：

     - 一旦初始化以后，其长度就不可修改。
     - 数组中提供的方法非常有限，对于添加、删除、插入数据等操作，非常不便，同时效率不高。
     -  获取数组中实际元素的个数的需求，数组没有现成的属性或方法可用
     - 数组存储数据的特点：有序、可重复。对于无序、不可重复的需求，不能满足。

2. 集合框架：

   1. |----Collection接口：单列集合，用来存储一个一个的对象
      - |----List接口：存储有序的、可重复的数据。  -->“动态”数组
        - |----ArrayList、LinkedList、Vector
      - |----Set接口：存储无序的、不可重复的数据   -->高中讲的“集合”
        -  |----HashSet、LinkedHashSet、TreeSet
   2. |----Map接口：双列集合，用来存储一对(key - value)一对的数据   -->高中函数：y = f(x)
      - |----HashMap、LinkedHashMap、TreeMap、Hashtable、Properties

3. 集合元素的遍历使用迭代器Iterator接口

   - 1.内部的方法：hasNext() 和  next()

   -  2.集合对象每次调用iterator()方法都得到一个全新的迭代器对象，

     默认游标都在集合的第一个元素之前。

   - 内部定义了remove(),可以在遍历的时候，删除集合中的元素。此方法不同于集合直接调用remove()

4. jdk 5.0 新增了foreach循环，用于遍历集合、数组

5. **List接口**

   - 存储有序的可重复的数据——>动态数组，替换原有的数组

   - 面试题：

     **ArrayList,LinkedList,Vector三者的异同？**

     相同点：三者都是实现List接口，存储数据的特点相同；存储有序的可重复的数据

     不同点：

     - ArrayList：作为List接口的主要实现类；线程不安全的，效率高；底层使用Object[] elementData存储
     - LinkedList：对于频繁的插入、删除操作，使用此类效率比ArrayList高；底层使用双向链表存储
     - Vector：作为List接口的古老实现类；线程安全的，效率低；底层使用Object[] elementData存储

   -  **ArrayList的源码分析：**

     1. **jdk 7情况下**

        - ArrayList list = new ArrayList();//底层创建了长度是10的Object[]数组elementData

        - list.add(123);//elementData[0] = new Integer(123);

          。。。。

          list.add(11);//如果此次的添加导致底层elementData数组容量不够，则扩容。

          默认情况下，扩容为原来的容量的1.5倍，同时需要将原有数组中的数据复制到新的数组中。

        - 结论：**建议开发中使用带参的构造器**：ArrayList list = new ArrayList(int capacity)

     2. **jdk 8中ArrayList的变化：**

        - ArrayList list = new ArrayList();//底层Object[] elementData初始化为{}.**并没有创建**长度为10的数组

        - list.add(123);//第一次调用add()时，底层才创建了长度10的数组，并将数据123添加到elementData[0]

          。。。。。

          后续的添加和扩容操作与jdk 7 无异。

     3. 小结：

        - jdk7中的ArrayList的对象的创建类似于单例的饿汉式，而jdk8中的ArrayList的对象的创建类似于单例的懒汉式，延迟了数组的创建，节省内存

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

     5. **Vector的源码分析**：jdk7和jdk8中通过Vector()构造器创建对象时，底层都创建了长度为10的数组。在扩容方面，默认扩容为原来的数组长度的2倍。

   - **List接口中的常用方法：**

     - void add(int index, Object ele):在index位置插入ele元素
     - boolean addAll(int index, Collection eles):从index位置开始将eles中的所有元素添加进来
     - Object get(int index):获取指定index位置的元素
     - int indexOf(Object obj):返回obj在集合中首次出现的位置
     - int lastIndexOf(Object obj):返回obj在当前集合中末次出现的位置
     - Object remove(int index):移除指定index位置的元素，并返回此元素
     - Object set(int index, Object ele):设置指定index位置的元素为ele
     - List subList(int fromIndex, int toIndex):返回从fromIndex到toIndex位置的子集合
     - **常用方法：**
       1. 增：add(Object obj)
       2. 删：remove(int index) / remove(Object obj)
       3. 改：set(int index, Object ele)
       4. 查：get(int index)
       5. 插：add(int index, Object ele)
       6. 长度：size()
       7. 遍历：① Iterator迭代器方式；② 增强for循环；③ 普通的循环；

6. **set接口**

   - set接口的框架

     - |----Collection接口：单列集合，用来存储一个一个的对象

       ​		|----Set接口：存储无序的、不可重复的数据   -->高中讲的“集合”

       ​				|----**HashSet**：作为Set接口的主要实现类；线程不安全的；可以存储null值

       ​						|----LinkedHashSet：作为HashSet的子类；遍历其内部数据时，可以按照添加的顺序遍历，对于频繁的遍历																操作，LinkedHashSet效率高于HashSet.

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
     2. 两种排序方式：自然排序（实现Comparable接口） 和 定制排序（Comparator）
     3. 自然排序中，比较两个对象是否相同的标准为：compareTo()返回0.不再是equals().
     4. 定制排序中，比较两个对象是否相同的标准为：compare()返回0.不再是equals().

7. **Map接口**

   - **map实现类的结构**

     |----Map:双列数据，存储key-value对的数据   ---类似于高中的函数：y = f(x)

     ​		|----HashMap:作为Map的主要实现类；线程不安全的，效率高；**存储null的key和value**

     ​				|----LinkedHashMap:保证在遍历map元素时，可以按照添加的顺序实现遍历。

     ​						原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素。

     ​						对于频繁的遍历操作，此类执行效率高于HashMap。

     ​		|----TreeMap:保证**按照添加的key-value对进行排序**，实现排序遍历。此时考虑key的自然排序或定制排序；**底层使用红黑树**

     ​		|----Hashtable:作为古老的实现类；线程安全的，效率低；**不能**存储null的key和value

     ​				|----Properties:常用来处理配置文件。key和value都是String类型

     HashMap的底层：数组+链表  （jdk7及之前）**数组+链表+红黑树 （jdk 8）**

   - **面试题：**

     1. HashMap的底层实现原理？
     2. HashMap 和 Hashtable的异同？
     3. CurrentHashMap 与 Hashtable的异同？（暂时不讲）

   - **map结构的理解**

     1. Map中的key:无序的、不可重复的，使用Set存储所有的key  ---> key所在的类要重写equals()和hashCode() （以HashMap为例）
     2. Map中的value:无序的、可重复的，使用Collection存储所有的value --->value所在的类要重写equals()
     3. 一个键值对：key-value构成了一个Entry对象。
     4. Map中的entry:无序的、不可重复的，使用Set存储所有的entry

   - **HashMap的底层实现原理？以jdk7为例说明：**

     ​		HashMap map = new HashMap():在实例化以后，底层创建了长度是**16**的一维数组Entry[] table。

     ​		...可能已经执行过多次put...

     ​		map.put(key1,value1):

     1. 首先，调用key1所在类的hashCode()计算key1哈希值，此哈希值经过某种算法计算以后，得到在Entry数组中的存放位置。

     ​		如果此位置上的数据为空，此时的key1-value1添加成功。 ---**-情况1**

     ​		如果此位置上的数据不为空，(意味着此位置上存在一个或多个数据(以链表形式存在)),比较key1和已经存在的一个或多个数据的**哈希值**：

     ​				如果key1的哈希值与已经存在的数据的**哈希值都不相同**，此时key1-value1添加成功。----**情况2**

     ​				如果key1的哈希值和已经存在的某一个数据(key2-value2)的哈希值相同，继续比较：调用key1所在类的**equals(key2)**方法，比较：

     ​						如果equals()返回false:此时key1-value1添加成功。----**情况3**

     ​						如果equals()返回true:**使用value1替换value2**。

     ​		补充：关于情况2和情况3：此时key1-value1和原来的数据以**链表**的方式存储。

     ​		在不断的添加过程中，会涉及到扩容问题，当超出临界值(且要存放的位置非空)时，扩容。默认的扩容方式：**扩容为原来容量的2倍**，**并将原有的数据复制过来**。

   - **jdk8 相较于jdk7在底层实现方面的不同：**

     - new HashMap():底层没有创建一个长度为16的数组
     - jdk 8底层的数组是：Node[],而非Entry[]
     - 首次调用put()方法时，底层创建长度为16的数组
     - jdk7底层结构只有：**数组+链表**。jdk8中底层结构：**数组+链表+红黑树**。
       1. 形成链表时，七上八下（jdk7:新的元素指向旧的元素。jdk8：旧的元素指向新的元素）
       2. 当数组的某一个索引位置上的元素以链表形式存在的数据个数 > 8 且当前数组的长度 > 64时，此时此索引位置上的所数据改为使用红黑树存储。
     - **DEFAULT_INITIAL_CAPACITY** : HashMap的默认容量，16
     -  **DEFAULT_LOAD_FACTOR**：HashMap的默认加载因子：0.75
     - threshold：**扩容的临界值，=容量*填充因子**：16 * 0.75 => 12
     - **TREEIFY_THRESHOLD**：Bucket中链表长度大于该默认值，转化为红黑树:8
     - **MIN_TREEIFY_CAPACITY**：桶中的Node被树化时最小的hash表容量:64

   - **LinkedHashMap的底层实现原理（了解）**

     源码中：

      static class Entry<K,V> extends HashMap.Node<K,V> {

     ​		Entry<K,V> before, after;//能够记录添加的元素的先后顺序

     ​		Entry(int hash, K key, V value, Node<K,V> next) {

     ​				super(hash, key, value, next);

     ​		}

     }

   - **Map中定义的方法：**

     1. 添加、删除、修改操作：
        1. Object put(Object key,Object value)：将指定key-value添加到(或修改)当前map对象中
        2. void putAll(Map m):将m中的所有key-value对存放到当前map中
        3. Object remove(Object key)：移除指定key的key-value对，并返回value
        4. void clear()：清空当前map中的所有数据
     2. 元素查询的操作：
        1. Object get(Object key)：获取指定key对应的value
        2. boolean containsKey(Object key)：是否包含指定的key
        3. boolean containsValue(Object value)：是否包含指定的value
        4. int size()：返回map中key-value对的个数
        5. boolean isEmpty()：判断当前map是否为空
        6. boolean equals(Object obj)：判断当前map和参数对象obj是否相等
     3. 元视图操作的方法：
        1. Set keySet()：返回所有key构成的Set集合
        2. Collection values()：返回所有value构成的Collection集合
        3.  Set entrySet()：返回所有key-value对构成的Set集合
     4. 总结：常用方法：
        1. 添加：put(Object key,Object value)
        2. 删除：remove(Object key)
        3. 修改：put(Object key,Object value)
        4. 查询：get(Object key)
        5. 长度：size()
        6. 遍历：keySet() / values() / entrySet()

   - TreeMap

     向TreeMap中添加key-value，要求key必须是由同一个类创建的对象

     因为要按照key进行排序：自然排序 、定制排序

8. **Collections工具类的使用**

   1. Collections:操作Collection、Map的工具类
   2. **面试题：Collection 和 Collections的区别？**
      - reverse(List)：反转 List 中元素的顺序
      - shuffle(List)：对 List 集合元素进行随机排序
      - sort(List)：根据元素的自然顺序对指定 List 集合元素按升序排序
      - sort(List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序
      - swap(List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换
      - Object max(Collection)：根据元素的自然顺序，返回给定集合中的最大元素
      - Object max(Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最大元素
      - Object min(Collection)
      - Object min(Collection，Comparator)
      - int frequency(Collection，Object)：返回指定集合中指定元素的出现次数
      - void copy(List dest,List src)：将src中的内容复制到dest中
      - boolean replaceAll(List list，Object oldVal，Object newVal)：使用新值替换 List 对象的所有旧值
      - **Collections 类中提供了多个 synchronizedXxx() 方法，该方法可使将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题**


##### 泛型

1. 
