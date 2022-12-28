# Java电商秒杀实战

## 项目环境搭建

### 配置redis

```java
private String host;
private int port;
private int timeout;//秒
private String password;
private int poolMaxTotal;
private int poolMaxIdle;
private int poolMaxWait;//秒
```

### 配置JedisPool

```java
@Bean
public JedisPool JedisPoolFactory() {
    JedisPoolConfig poolConfig = new JedisPoolConfig();
    poolConfig.setMaxIdle(redisConfig.getPoolMaxIdle());
    poolConfig.setMaxTotal(redisConfig.getPoolMaxTotal());
    poolConfig.setMaxWaitMillis(redisConfig.getPoolMaxWait() * 1000);
    JedisPool jp = new JedisPool(poolConfig, redisConfig.getHost(), redisConfig.getPort(),
            redisConfig.getTimeout()*1000, redisConfig.getPassword(), 0);
    return jp;
}
```

Linux下redis的相关命令

1. 安装
   - 解压tar -zvxf redis-4.0.2.tar.gz
   - 移动mv redis-4.0.2 **/usr/local/redis**
   - 编译 make   发现问题没有装gcc
     - yum install cpp
     - yum install binutils
     - yum install glibc
     - yum install glibc-kernheaders
     - yum install glibc-common
     - yum install glibc-devel
     - yum install gcc
     - yum install make
   - make install
   - 启动redis  
     - redis-cli
     - 密码 auth 123456
     - 退出exit
2. 

### 写RedisService

```java
package com.lfb.miaosha.redis;

import com.alibaba.fastjson.JSON;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Service;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

@Service
public class RedisService {
    @Autowired(required = false)
    JedisPool jedisPool;
    @Autowired(required = false)
    RedisConfig redisConfig;

    /**
     * 获取当个对象
     * */
    public <T> T get(KeyPrefix prefix,String key,  Class<T> clazz) {

        Jedis jedis = null;
        try{
            jedis = jedisPool.getResource();
            //生成真正的key
            String realKey = prefix.getProfix() + key;
            String str = jedis.get(realKey);
            T t = stringToBean(str, clazz);
            return t;
        }finally {
            returnToPool(jedis);
        }
    }
    /**
     * 设置对象
     * */
    public <T> boolean set(KeyPrefix prefix, String key,  T value) {

        Jedis jedis = null;
        try{
            jedis = jedisPool.getResource();
            String str = beanToString(value);
            if (str == null || str.length() <= 0){
                return false;
            }
            //生成真正的key
            String realKey = prefix.getProfix() + key;
            int seconds = prefix.expireSeconds();
            if (seconds <= 0){
                jedis.set(realKey,str);
            }else{
                jedis.setex(realKey,seconds, str);
            }
            return true;
        }finally {
            returnToPool(jedis);
        }
    }
    /**
     * 判断key是否存在
     * */
    public <T> boolean exists(KeyPrefix prefix, String key) {
        Jedis jedis = null;
        try{
            jedis = jedisPool.getResource();
            //生成真正的key
            String realKey = prefix.getProfix() + key;
            return jedis.exists(realKey);
        }finally {
            returnToPool(jedis);
        }
    }
    /**
     * 增加值
     * */
    public <T> Long incr(KeyPrefix prefix, String key) {
        Jedis jedis = null;
        try{
            jedis = jedisPool.getResource();
            //生成真正的key
            String realKey = prefix.getProfix() + key;
            return jedis.incr(realKey);
        }finally {
            returnToPool(jedis);
        }
    }
    /**
     * 减少值
     * */
    public <T> Long decr(KeyPrefix prefix, String key) {
        Jedis jedis = null;
        try{
            jedis = jedisPool.getResource();
            //生成真正的key
            String realKey = prefix.getProfix() + key;
            return jedis.decr(realKey);
        }finally {
            returnToPool(jedis);
        }
    }
    
    private <T> String beanToString(T value){
        if(value ==null){
            return null;
        }
        Class<?> clazz = value.getClass();
        if (clazz == int.class || clazz == Integer.class){
            return "" + value;
        }else if(clazz == String.class){
            return (String)value;
        }else if (clazz == long.class || clazz == Long.class){
            return "" + value;
        }else{
            return JSON.toJSONString(value);
        }
    }
    
    private <T> T stringToBean(String str, Class<T> clazz){
        if (str == null || str.length()<=0 ||clazz == null){
            return null;
        }
        if (clazz == int.class || clazz == Integer.class){
            return (T)Integer.valueOf(str);
        }else if(clazz == String.class){
            return (T) str;
        }else if (clazz == long.class || clazz == Long.class){
            return (T)Long.valueOf(str);
        }else{
            return JSON.toJavaObject(JSON.parseObject(str),clazz);
        }
    }

    private void returnToPool(Jedis jedis){
        if (jedis != null){
            jedis.close();
        }
    }
}
```

### 写KeyPrefix接口对各个模块区分

### **内容总结**

springboot环境搭建

**result结果封装，通用缓存Key封装**，集成mybatis+Druid,集成redis+Jedis安装



## 实现登录功能

- 数据库设计
- 明文密码两次MD5处理
  1. 用户端：PASS = MD5（明文 + 固定Salt）**防止用户的明文密码在网络上传输**
  2. 服务端：PASS = MD5（用户输入 + 随机Salt）第二次将处理过的密码存入数据库
- JSR303参数检验+全局异常处理器
- 分布式Session

### 前端设计

1. bootstrap设计界面
2. jquery-validation做表单验证
3. layer.js做弹框
4. MD5.js

### 异常的拦截

拦截绑定异常，输出错误信息

### 分布式Session

分布式多台服务器，用户的session处理？存在的问题，用户的第一个请求落在第一台服务器中，第二个请求落在第二台服务器上，则用户的session信息全部丢失

处理：session同步，一台服务器上的session信息同步到另一台服务器上，性能有问题，实现复杂

**用户登录成功后，服务器生成一个类似于sessionid的东西（token）标识用户，写到cookie中传递给客户端，客户端在随后的访问中都在cookie中上传这个token，服务端拿到token取到用户对应的session信息**

**用第三方缓存redis管理session**

1. UUIDUtil生成token
2. 将token存在redis中
3. 生成cookie
4. 设置cookie失效时间

**容器中的session有效时间=用户最后登录时间+session的过期时间**——**实现session的延期**

实现重新生成token

问题：springmvc的controller方法里可以带很多参数，**使用mvc框架里的addArgumentResolvers函数给controller里的方法赋值**

### 内容总结

**数据库设计，两次明文密码两次MD5处理，JSR303参数检验，全局异常处理器，分布式session**

http网络上明文传输，输入明文密码，在网络上传输，数据包可能被截取掉，第二次MD5防止数据库被盗了



## 实现秒杀功能

用户浏览商品页，点击商品进入商品详情页，点击秒杀，秒杀成功进入订单详情页

### 数据库设计

商品表，订单表，秒杀商品表，秒杀订单表

### 商品列表页

商品列表页，商品详情页，订单详情页

![image-20211011233803014](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211011233803014.png)

### 商品详情页

![image-20211011233831288](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211011233831288.png)

### 订单详情页

![image-20211011233952915](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211011233952915.png)

### 实现秒杀功能

设置秒杀商品的开始和结束时间

根据**商品ID**提交到**MiaoshaController**

- **判断用户是否存在**，不存在返回login
- **判断商品库存**，没有库存，返回商品秒杀失败信息
- **判断是否秒杀到商品**，根据userId和goodsID判断miaoshaorder中是否存在订单,存在返回不能重复秒杀信息
- **减库存，下订单，写入秒杀订单****（原子操作-事务）**
  - 调用MiaoshaService中的miaosha函数，返回生成的订单信息（OrderService）（根据userId和goodsID）
    - 根据商品ID减少商品库存（GoodsService）
    - 生成新的订单（OrderService）

### 内容总结

**数据库设计，商品列表页，商品详情页，订单详情页**

## 秒杀测压-Jmeter压力测试

### Jmeter入门

1. https://jmeter.apache.org/
2. 文档https://jmeter.apache.org/usermanual/index.html
3. 并发时QPS值，tps值
4. 聚合报告
   - throughput吞吐量
5. **添加线程组**
6. **添加监听器   聚合报告**
7. **线程组右键  添加Sampler   HTTP请求**

### 自定义变量模拟多用户

1. 测试计划    添加配置元件    CSV Data Set Config
2. 引用变量${}
3. **测试获取用户信息**

### JMeter命令行使用

1. 在Windows上录好jmx
2. 命令行：sh jmeter.sh -n -t XXX.jmx -l result.jtl
3. 将result.jtl导入Jmeter

- 将项目生成jar包https://www.cnblogs.com/hai-feng/p/12302073.html
- 上传到服务器
- nohup java -jar miaosha4.jar &
- tail -f nohup.out 

#### 步骤：

1. 生成1000个用户
2. 测试秒杀功能
3. 发现并发的瓶颈主要在于数据库的访问（分库，分表）**大并发核心在数据库的访问（抗压力）**
   - 解决方法，添加缓存

### Redis压测工具Redis-benchmark

1. redis-benchmark -h 127.0.0.1 -p 6379 -c 100 -n 100000**100个并发连接, 100000个请求**
2. redis-benchmark -h 127.0.0.1 -p 6379 -q -d 100       **存取大小为100字节的数据包**
3. redis-benchmark -t set,lpush -q -n 100000  **十万个请求只测试set和lpush命令**

### spirngboot 打war包放到Tomcat

1. 修改pom.xml文件将默认的jar方式改为war：

2. 在pom.xml中添加依赖，将scope状态修改为provided

   ```xml
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-tomcat</artifactId>
   <scope>provided</scope>
   </dependency>
   ```

3. **继承SpringBootServletInitializer实现 WebApplicationInitializer重写configure方法：**

4. 菜单 Build -->> build artifacts -->> all artifacts -->> Build

5. 此时项目已经成功打包成war包，位置在项目结构的target下，以.war结尾的就是项目

6. 直接将war包放到服务器tomcat下的webapps中

### 内容总结：

## 页面优化技术

### 页面缓存+url缓存+对象缓存

1. 页面缓存粒度最大，对象缓存粒度最小
2. **取缓存       手动渲染魔板thymeleafViewResolver      结果输出**
3. 减少数据从服务端的下载
4. **页面缓存**，多用户访问界面相同
5. **点击不同商品展现不同页面为URL缓存**
6. **对象缓存**：
   1. **用户的缓存信息**
   2. 一个service引用的时候一定要引用别人的service,  别人的service里有可能调用缓存
   3. **数据发生更新一定也要把缓存更新掉，先修改数据库，再修改缓存**

### 页面静态化，前后端分离

1. 传统jsp动态，thymeleaf动态，**静态化，纯html**通过**js,ajax请求服务端**拿到**数据渲染页面**，可以将**HTML文件缓存在客户端**，**页面数据**不需要重复下载，只需要**下载动态数据**，从本地缓存获取页面，**极大减少数据流量**
2. **常用技术AngularJS ,vue.js**————本项目用**Jqury模拟获取动态数据**
3. 优点：**利用浏览器的缓存** 

### 静态资源优化

1. **图片 css   js压缩，减少流量**
2. **多个JS/CSS组合，减少连接数**显著提高页面加载速度
3. CDN就近访问

### CDN优化

CDN的全称是Content Delivery Network，即[内容分发网络](https://baike.baidu.com/item/内容分发网络/4034265)。CDN是构建在现有网络基础之上的**智能虚拟网络**，**依靠部署在各地的边缘服务器**，通过**中心平台的负载均衡、内容分发、调度**等功能模块，使**用户就近获取所需内容**，**降低网络拥塞，提高用户访问响应速度和命中率**。CDN的关键技术主要有**内容存储和分发技术**

### nginx

Nginx是一款[轻量级](https://baike.baidu.com/item/轻量级/10002835)的[Web](https://baike.baidu.com/item/Web/150564) 服务器/[反向代理](https://baike.baidu.com/item/反向代理/7793488)服务器及[电子邮件](https://baike.baidu.com/item/电子邮件/111106)（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，[并发](https://baike.baidu.com/item/并发/11024806)能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、[京东](https://baike.baidu.com/item/京东/210931)、[新浪](https://baike.baidu.com/item/新浪/125692)、[网易](https://baike.baidu.com/item/网易/185754)、[腾讯](https://baike.baidu.com/item/腾讯/112204)、[淘宝](https://baike.baidu.com/item/淘宝/145661)等

### 问题：

- **多次下订单导致商品库存小于0**
  - 解决：**更新数据库时判断库存是否大于0，大于才可以更新商品信息，然后下订单**

- **一个用户同时发出两个请求，显示都没有秒杀到，就可以同时减库存，下订单，导致一个用户秒杀到两种商品**
  - 解决：通过数据库解决，利用数据库的**唯一索引**，防止用户插入重复记录，做秒杀的时候，**提交表单之前会生成一个验证码**，在miaosha_order表上建一个唯一索引user_id和goods_id，保证商品不会超卖
  - **生成订单的时候，将订单写到缓存中，不需要查数据库，只需要查缓存，优化性能**
- **解决超卖：**
  - **数据库表加唯一索引，防止用户重复购买**
  - **SQL加库存数量判断：防止库存变成负数**

### 内容总结：

**并发大瓶颈在数据库的访问，解决方法加缓存。**

缓存有很多，用户角度：浏览器端发起请求，在浏览器端做页面静态化，将页面缓存在用户的浏览器端，请求到达网站之前，在网络中部署CDN节点，让请求首先访问CDN，通过CDN到网站的话，网站可以再做一次缓存，比如nginx也可以加缓存，如果Nginx也没有缓存，应用程序里的页面缓存，细粒度一点就是对象缓存，最后一层才是数据库，通过层层访问数据库缓存，逐步削减到达数据库的请求数量，这样才能保障网站在大并发情况下，扛得住压力，不同粒度，不同层面缓存减轻数据库的压力，采用缓存后肯定会产生一些数据的不一致，在整个过程中做一个平衡，在满足数据一致性的前提下，做一个缓存。



## 秒杀接口优化

**思路：减少数据库访问**

1. **系统初始化，将商品库存数量加载到redis**
2. 收到请求，**redis预减库存，库存不足，直接返回**，否则进入3
3. 请求入队，立即返回排队中
4. **请求出队，生成订单，减少库存**
5. **客户端轮询，是否秒杀成功**

### redis预减库存减少数据库访问

### 内存标记减少redis访问

- **当请求下单数大于库存数**，库存减少为0后，就不在需要访问数据库

### 请求先入队缓存，异步下单，增强用户体验

1. 同步下单改为异步下单

### RabbitMQ队列缓存，异步下单，增强用户体验

#### RabbitMQ安装与spring集成

- 安装erlang,大并发（是一门语言）

  ![image-20211013225414777](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211013225414777.png)

- 安装rabbitmq流行的开源消息队列系统

  1. xz -d rabbitmq-server-generic-unix-3.8.6.tar.xz

  2. tar xf rabbitmq-server-generic-unix-3.8.6.tar

  3. 安装依赖

     - yum install python3 -y
     - yum install xmlto -y
     - yum install python3-simplejson -y

  4. mv rabbitmq_server-3.8.6/ /user/local/rabbitmq

  5.  cd /usr/local/rabbitmq

  6.  cd sbin/

  7. 设置rabbitmq服务**开机自启动**

     systemctl enable rabbitmq-server

     **关闭**rabbitmq服务

     systemctl stop rabbitmq-server

     **重启**rabbitmq服务

     systemctl restart rabbitmq-server

  8. 查看进程ps -ef | grep rabbitmq

- 集成

  1. 添加依赖spring-boot-starter-amqp
  2. 创建消息接受者MQReceiver(四种交换机Exchange模式)https://blog.csdn.net/qq_39240270/article/details/94202815
     1. 直连交换机：Direct exchange
     2. 扇形交换机：Fanout exchange
     3. 主体交换机：Topic exchange
     4. 首部交换机：Headers exchange
  3. 创建消息发送者MQsender
  4. 使用rabbitmq消息队列，测试了四种交换模式

### Nginx水平扩展

### 压测

### 小插曲

1. **分库分表中间件mycat**

## 安全优化

### 秒杀接口地址隐藏

**秒杀真正开始之前，获取不到秒杀地址**

1. 秒杀开始之前，先去请求接口获取秒杀地址
   - 接口改造，带上PathVariable参数
   - **添加生成地址的接口**
   - 秒杀收到请求，先验证PathVariable

### 数学公式验证码

**功能：防止机器人，将请求分散开**

1. 生成图形验证码接口 
2. 获取秒杀路径的时候，验证验证码
3. ScriptEngine使用

### 接口限流防刷

1. **限制某一个接口被某一用户在一分钟内访问多少次**
2. **放缓存里做，次数写到缓存里，给数据加一个有效期，访问一次，数字加1，超过限制次数**，返回失败
3. **封装通用访问次数判断**
4. **写一个拦截器，用注解封装**

- ```
  ThreadLocal//与当前线程绑定
  ```

## 服务器优化

### Tomcat优化

1. 内存优化
2. 并发优化
   1. 影响性能参数
      - maxConnections(NIO,IO多路复用模型)，最大10000，默认8192；APR模式缩减到1024
      - acceptCount，新来的请求来不及处理放队列，默认值100
      - maxThreads默认值200工作线程数
      - minSpareThreads最小空闲数，空闲线程数
3. APR优化（tomcat使用apr连接器）https://cloud.tencent.com/developer/article/1702345

### Nginx优化[nginx](http://nginx.org/en/)配置文档有优先级

- 并发优化

  - 1.工作线程数和并发连接数
    worker_rlimit_nofile 20480; #每个进程打开的最大的文件数=worker_connections*2是安全的，受限于操作系统/etc/security/limits.conf
    vi /etc/security/limits.conf

    * hard nofile 204800
    * soft nofile 204800
    * soft core unlimited
    * soft stack 204800

    **worker_processes 4; #cpu，如果nginx单独在一台机器上**
    **worker_processes auto;**
    **events {**
        **worker_connections 10240;#每一个进程打开的最大连接数，包含了nginx与客户端和nginx与upstream之间的连接**
        **multi_accept on; #可以一次建立多个连接**
        **use epoll;**
    **}**

- KeepAlive长连接

  - 客户端与nginx（默认是打开的）：
    keepalive_timeout  60s; #长连接的超时时间
    keepalive_requests 100; #100个请求之后就关闭连接，可以调大
    keepalive_disable msie6; #ie6禁用

  - Nginx与upstream server：

    反向代理服务器集群

    upstream server_pool{
            server localhost:8080 weight=1 max_fails=2 fail_timeout=30s;
            keepalive 300;  #300个长连接，最大的空闲的长连接数
    }

  - 同时要在location中设置：
    location /  {
                proxy_http_version 1.1;协议版本
    	proxy_set_header Upgrade $http_upgrade;
    	proxy_set_header Connection "upgrade";
    }

  - 

- 压缩优化

  - gzip on;开启
    gzip_http_version 1.1;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
    gzip_proxied any;
    gzip_types text/plain text/css application/javascript application/x-javascript application/json application/xml application/vnd.ms-fontobject application/x-font-ttf application/svg+xml application/x-icon;
    gzip_vary on; #Vary: Accept-Encoding
    gzip_static on; #如果有压缩好的 直接使用

- 配置缓存

  ![image-20211016081525489](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016081525489.png)

  ​		静态文件加载

  ​		![image-20211016081615148](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016081615148.png)

  ​		清除缓存

  ​		![image-20211016081733761](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016081733761.png)

- 两个监控工具

  - Nginx_status：并发统计
  - Ngxtop::请求统计

### LVS四层负载均衡（对应计算机网络里的七层和四层模型）IP层

1. ![image-20211016083558679](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016083558679.png)
2. **VS/NAT修改报文头信息**
3. **VS/TUNE,IP隧道**
4. **VS/DR:效率高，必须在同一网段**
5. **负载均衡8种调度算法**https://blog.csdn.net/weixin_42006882/article/details/106254538?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.no_search_link&spm=1001.2101.3001.4242
   1. 轮询、
   2. 加权轮询、
   3. 最小连接、
   4. **加权最小连接、**（使用最多0
   5. 局部最小连接、
   6. 带复制的局部最小连接、
   7. 目标地址散列、
   8. 原地址散列
6. ![image-20211016084833647](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016084833647.png)

### LVS+Keepalived高可用https://www.jianshu.com/p/9fe2c140fa52

1. ![image-20211016090843708](C:\Users\17391\AppData\Roaming\Typora\typora-user-images\image-20211016090843708.png)
2. **DS轮询**一个域名对应多个IP地址



## 经过一周的努力时间，2021年10月16日终于把这个项目做完了，好开心，哈哈哈哈

奖励自己一袋奶喝，嘿嘿嘿嘿嘿嘿

# 项目总结

秒杀：**瞬间产生大量的请求，（高并发）**

秒杀的相关核心技术（**高并发业务场景**）：**缓存技术**，**异步化**，**添加缓存，请求异步化**

前端：thymeleaf,bootstrap,jQuery

后端：SpringBoot，JSR303，Mybatis

中间件：RabbitMQ,Redis,Drudi(连接池，监控数据库)

秒杀;**分布式会话，商品列表页，详情页，订单详情页，系统测压，缓存优化，消息队列，接口安全**

**学习到：**如何利用缓存，如何使用异步

## 常见问题总结





