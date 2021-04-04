### 是什么

指在程序运行期间，将某段代码动态的切入到指定方法的指定位置进行运行的编程方式，称为面向切面编程

### 专业术语

- 横切关注点
  - 方法开始	通知方法
  - 方法结束    通知方法
  - 方法异常    通知方法
  - 方法返回    通知方法

- 切面类    通知方法所在的类
- 连接点    每一个方法的每一个位置都是一个连接点
- 切入点    真正需要使用的连接点叫切入点
- 切入点表达式    选出连接点成为切入点

### 怎么用

- 首先写一个目标类

  - ```java
    public class MyCaluca implements Caloulator {
        @Override
        public int add(int x, int y) {
            return x + y;
        }
    
        @Override
        public int sub(int x, int y) {
            return x - y;
        }
    }
    ```

    

- 写一个切面类

  - ```java
    public class CalucaUtils {
        
        public static void logStart() {
            System.out.println("start...");
        }
        
        public static void logEnd() {
            System.out.println("end...");
        }
    }
    
    ```

    

- 写配置

  - 将目标类和切面类加入到IoC容器中，为这两个类加上注解Service和Component，Aspect

  - ```java
    @Service
    public class MyCaluca implements Caloulator {
        @Override
        public int add(int x, int y) {
            return x + y;
        }
    
        @Override
        public int sub(int x, int y) {
            return x - y;
        }
    }
    
    @Aspect
    @Component
    public class CalucaUtils {
    
        public static void logStart() {
            System.out.println("start...");
        }
    
        public static void logEnd() {
            System.out.println("end...");
        }
    }
    ```

    

  - 设置自动扫描

    - ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                  http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
      
          <context:component-scan base-package="org.imp"></context:component-scan>
          <context:component-scan base-package="org.Utils"></context:component-scan>
      </beans>
      ```

      

- 告诉Spring切面类的通知方法什么时候执行
  - @Before  在目标方法之前运行
  - @After在目标方法最终结束运行
  - @AfterReturn  在目标方法正常返回之后运行
  - @AfterThrowing  在目标方法抛出异常运行
  - @Around

- 最后一步，在配置文件中开启基于注解的aop，加入下面这一句话，和命名空间

  - ```xml
         xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/s
                                    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
    ```

    

  

- 创建一个测试类

  ```java
  public class test01 {
  
      ApplicationContext aop = new ClassPathXmlApplicationContext("aop.xml");
  
  
      @Test
      public void test01() {
          Caloulator bean = aop.getBean(Caloulator.class);
          bean.add(1, 2);
      }
  
  }
  
  //结果
  start...
  end...
  ```

  

- aop的细节...

  - AOP的底层是动态代理，容器中保存的对象是被代理类的代理对象

  - 没有接口也能生成代理对象

  - 切入点表达式的写法

    - execution(访问权限符 返回值类型 方法全类名（参数表）)
    - .. 可以匹配任意多个参数  execution(public int org.imp.MyCaluca.add(..))
    - .. 可以匹配任意多层路径  execution(public int org..MyCaluca.add(..))
    - public 可写可不写
    - 最模糊的写法：execution(* *(..))
    - 可以用逻辑符  && || ！

  - 通知方法的执行顺序

    - 正常执行
      - @Before（前置通知）
      - @After（后置通知）
      - @AfterReturning
    - 异常执行
      - @Before（前置通知）
      - @After（后置通知）
      - @AfterThrowing

  - 在通知方法时，获取详细信息

    - 获取方法参数：JoinPoint joinPoint.getArgs()

    - 获取方法名：joinPoint.getSignature.getName()

    - 获取返回值：

      ```java
      /**
       * The name of the argument in the advice signature to bind the returned value to
       */
      String returning() default "";
      ```

      

    - 例子：

      - ```java
         @Before("execution(public int org.imp.MyCaluca.add(int, int))")
            public  void logStart(JoinPoint joinPoint) {
                System.out.println("方法 " + joinPoint.getSignature().getName() +" start...参数是" + Arrays.asList(joinPoint.getArgs()) + "...");
            }
        
            @AfterReturning(value = "execution(public int org.imp.MyCaluca.add(int, int))", returning = "result")
            public void logReturn(JoinPoint joinPoint, Object result) {
                System.out.println("return..." + "结果是" + result);
            }
        ```

        