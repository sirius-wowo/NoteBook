#### bean的别名

 bean中存在一个name属性，所有的别名都写在这个属性中，多个别名之间 使用逗号 分号 或者 空格 来进行分割，例如如下代码:
 ```xml
 <bean id = "bookDaoInst" name = "bookDaoAlias bookDaoAliasss" class="com.gov.spstu.dao.impl.BookDaoImpl"/>
 ```
 效果上，bean id 和 name 效果上是一样的。但引用的时候，仍建议使用bean id
 
 
获取bean无论是通过id还是name获取，如果无法获取到，将抛出异常NoSuchBeanDefinitionException
``` java
NoSuchBeanDefinitionException: No bean named 'bookServiceImpl' available
```

#### bean的作用范围配置
通过bean的属性标签`scope`来进行bean的作用范围定义，主要有`singleton`(单例)和`prototype`(非单例)两个枚举
 ```xml
 <bean id = "bookDaoInst" name = "bookDaoAlias bookDaoAliasss" class="com.gov.spstu.dao.impl.BookDaoImpl" scope="prototype"/>
 ```
 
- 为什么bean默认是单例的呢？
- 适合交给容器进行管理的bean (可以反复用的)
    - 表现层对象
    - 业务层对象
    - 数据层对象
    - 工具对象
- 不适合交给容器进行管理的bean (有状态的，内部要记录成员状态属性值)
    - 封装实体的域对象

bean实例化
bean本质上就是对象，创建bean使用构造方法完成

实例化bean的三种方式 
##### 方式一. 构造方法(常用)

- 提供可访问的构造方法

    ```java
    public class BookDaoImpl implements BookDao {
        // 如果无参构造方法不存在，则会报错
        public BookDaoImpl(){
            System.out.println("book dao constructor is running ...");
        }
    
        public void save() {
            System.out.println("book dao save ...");
        }
    }
    ```
- 配置
    ```xml
    <bean id = "bookdao" class="com.gov.spstubeaninstance.dao.impl.BookDaoImpl" />
    ```

##### 方式二. 使用静态工厂实例化(了解即可)
- 静态工厂
    ```java
    public static OrderDao getOrderDao(){
        return new OrderDaoImpl();

    }
    ```
- 配置
    ```xml
    <!--  方式二. 使用静态工厂实例化bean  -->
    <bean id = "orderDao" class = "com.gov.spstubeaninstance.factory.OrderDaoFactory" factory-method="getOrderDao"/>
    ```
    
    
##### 方式三. 使用实例工厂实例化(了解即可)
原版毫无意义 不写了 他妈的 懒了, 但是通过FactoryBean改版的必须要掌握，具体如下：
- FactoryBean
```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    @Override
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }

    @Override
    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
```

- 配置
```xml
    <!--  方式四. 使用FactoryBean实例化bean  -->
    <bean id = "userDaoByBean" class = "com.gov.spstubeaninstance.factory.UserDaoFactoryBean"/>
```

 
 bean的生命周期
- 声明周期 从创建到消亡的过程
- bean生命周期 bean从创建到销毁的整体过程
- bean生命周期控制，在bean创建后到销毁前做一些事情

生命周期是通过bean的init-method属性 和 destroy-method属性来进行控制，指明处理的方法，例如:
- 代码中补充生命周期控制函数
```java
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }

    /** 表示bean初始化对应的操作 */
    public void init(){
        System.out.println("init ...");
    }

    /** 表示bean销毁前对应的操作 */
    public void destroy(){
        System.out.println("destroy ...");

    }
}
```
- xml中配置声明周期控制方法
```xml
<bean id = "bookDao" class = "com.gov.spstubeanlifecycle.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy"/>
```
但上述方法比较暴力。
 
推荐使用接口来控制声明周期，具体如下: 
```java
public class BookserviceImpl implements BookService, InitializingBean, DisposableBean {
    private BookDao bookDao;

    public void setBookDao(BookDao bookDao) {
        System.out.println("set property ...");
        this.bookDao = bookDao;
    }

    public void save()  {
        System.out.println("book service save ...");
        bookDao.save();
    }


    public void destroy() throws Exception {
        System.out.println("book service destroy ...");
    }
    public void afterPropertiesSet() throws Exception {
        System.out.println("book service init ...");
    }
}
```