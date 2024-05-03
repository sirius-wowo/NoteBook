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

实例化bean的三种方式 → 构造方法(常用)

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
