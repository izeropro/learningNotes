# IOC（控制反转）
控制反转：是一种设计思想，而不是一个具体的技术实现。原来的对象是由使用者来控制，有了Spring之后，可以把对象交给Spring来管理
DI（依赖注入）：把对应的属性值注入到具体的对象中。@Autowired,@populateBean完成属性值的注入
容器：存储对象，使用map结构来存储，在Spring中一般存在三级缓存，singletonObject存放完整的bean对象，整个bean的生命周期，从创建到使用再到销毁的过程全部由容器来管理。


# AOP（面向切面）
AOP(Aspect-Oriented Programming:面向切面编程)  
    能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

总：AOP 是IOC的一个扩展功能，主要应用于事务管理和日志打印。通过动态代理来实现  
分：Bean的创建过程中有一个步骤可以对bean进行扩展实现，aop本身就是一个扩展功能，所以在BeanPostProcessor的后置处理方法中来进行实现  
   1：代理对象的创建过程（advice、切面、切点）  
   2：通过jdk或cglib的方式来生成代理对象  
   3：在执行方法调用的时候，会调用到生成的字节码文件中，直接会找到DynamicAdvisoreInterceptor类中的intercept方法，从此方法开始执行  
  4：根据之前定义好的通知来生成拦截器链  
  5：从拦截器中一次获取每一个通知开始进行执行，在执行过程中为了方便找到下一个通知是哪个会有一个CglibMethodIncation的对象找的时候是从-1的位置开始查找并且执行的。

# JDK 动态代理和 CGLib 动态代理的区别
1. JDK动态代理利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler 来处理，而 cglib 动态代理是利用 asm 开源包，对代理对象类的 class 文件加载进来，通过修改其字节码生成子类来处理。
2. JDK动态代理只能对实现了接口的类生成代理，而不能针对类。CGLIB 是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法（继承）

# Spring 在选择用 JDK 还是 CGLiB 的依据：
(1）当 Bean 实现接口时，Spring 就会用 JDK 的动态代理
(2）当 Bean 没有实现接口时，Spring 使用 CGlib 是实现
(3）可以强制使用 CGlib（在 spring 配置中加入< aop : aspectj - autoproxy proxy - target - class =" true "/>)

# Spring循环依赖问题
1. 三级缓存机制
   - 一级缓存：正式对象
   - 二级缓存：半成品对象
   - 三级缓存：工厂
  
【参考】
[Spring循环依赖及解决方法](https://blog.csdn.net/cristianoxm/article/details/113246104)


# Spring Boot 事务管理
1. @EnableTransactionManagement注解来开启注解事务
   - @EnableTransactionManagemen ->TransactionManagementConfigurationSelector->ProxyTransactionManagementConfiguration->TransactionInterceptor（Advice属性）
2. TransactionInterceptor拦截器 @Transactional 注解
   - Spring 会调用TransactionInterceptor在目标方法执行前后进行拦截
   - ThreadLocal 获取数据库连接
【参考】
[@Transactional原理](https://www.jianshu.com/p/b33c4ff803e0)

# @Transactional注解
## 一、Transactional注解作用
1. 标识方法需要被代理
2. 携带事务管理需要的一些属性信息
    - value 和 transactionManager 属性：两个是一样的意思。当配置了多个事务管理器时，可以使用该属性指定选择哪个事务管理器
    - **propagation属性**：事务的传播行为
       1. REQUIRED（默认）：支持使用当前事务，如果当前事务不存在，创建一个新事务。
       2. SUPPORTS：支持使用当前事务，如果当前事务不存在，则不使用事务。
       3. MANDATORY：中文翻译为强制，支持使用当前事务，如果当前事务不存在，则抛出Exception。
       4. REQUIRES_NEW：创建一个新事务，如果当前事务存在，把当前事务挂起。
       5. NOT_SUPPORTED：无事务执行，如果当前事务存在，把当前事务挂起。
       6. NEVER：无事务执行，如果当前有事务则抛出Exception。
       7. NESTED：嵌套事务，如果当前事务存在，那么在嵌套的事务中执行。如果当前事务不存在，则表现跟REQUIRED一样。
    - **isolation 属性**：事务的隔离级别
       1. Isolation.DEFAULT（默认）
       2. Isolation.READ_UNCOMMITTED
       3. Isolation.READ_COMMITTED
       4. Isolation.REPEATABLE_READ
       5. Isolation.SERIALIZABLE
    - timeout 属性：事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。
    - readOnly 属性：指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 read-only 为 true。
    - rollbackFor 属性：用于指定能够触发事务回滚的异常类型，可以指定多个异常类型。
    - noRollbackFor 属性：抛出指定的异常类型，不回滚事务，也可以指定多个异常类型

## 二、Transactional注解实现流程
![Transactional注解实现流程](images/Transactional%E6%B3%A8%E8%A7%A3%E5%AE%9E%E7%8E%B0%E6%B5%81%E7%A8%8B.png)

【参考】
[Spring的@Transactional如何实现的](https://www.modb.pro/db/145349)

# Spring事务传播机制
## 一、pring事务失效的几种场景以及原因
1. 抛出编译异常 ，事务只能捕捉运行时异常和error  
    **解决方案**： 配置rollbackFor eg. 配置@Transactional(rollbackFor = Exception.class)
2. 选用了错误的事务传播机制 例如 @Transactional(propagation = Propagation.NOT_SUPPORTED)
    有事务挂起 ，无事务就以非事务的方式执行。
3. **业务自身捕捉到异常[try catch finally] 而不是通过抛出异常交给类处理**  
    因为spring事务只有捕捉到了业务抛出去的异常，才能进行后续的处理，如果业务自己捕获了异常，则事务无法感知。  
    **解决方案**：将异常原样抛出 尽量不要在业务方法中使用try...catch来捕获你的异常，防止影响了事务。
4. **跨了线程的事务**  
    原因：如果事务方法内，开启了新线程去执行其他事务方法也是不受当前事务方法控制的。因为不同线程拥有的**threadlocal** 不一样。  
    所以：当你需要明确开启新线程，请分开处理。
5. @Transactional放在非public方法上。  
    原因：AbstractFallbackTransactionAttributeSource类调用computeTransactionAttribute()时，过滤了非public方法上事务配置信息（相当于没有配置无事务运行机制）。

## 二、Spring事务传播机制
1. 什么是传播事务：
    事务是数据库的一种特性,而Spring只是封装这个特性，方便我们使用，最终的执行实际上都是在数据库中完成，但是对于数据库来说，事务是单一的，没有那么多业务场景,但是对于Spring来说,会面对各种各样的业务需求，所以需要有一套可以从代码层面去控制事务来满足我们的场景需求，所以也就有了传播机制。

    **事务传播机制**是通过@Transactional实现得，使用了ThreadLocal实现得，保证事务方法拿到的数据库连接是同一个呢？答案就是使用了ThreadLocal。
    我们平时开发不可能所有数据库操作都在一个方法里，之前说过保证事务的很重要的一点是始终用一个连接connection，所以被调用方法里有操作数据库时也需要用这个主方法中创建的connection。因为不太可能把所有调用方法都加一个connection入参，所以比较优雅的方法是把连接放在ThreadLocal里，其他方法要操作数据库时就从ThreadLocal里get即可。
【参考】
[spring事务失效的几种场景以及原因](https://www.cnblogs.com/lzbbbb/p/16511827.html)