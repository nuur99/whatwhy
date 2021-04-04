### Spring IoC

让我们从零开始先建立一个Spring项目吧，第一步先新建一个Maven项目，新建之后我们要导入Spring所需要的依赖

```xml
 <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>
```

我们把代码写入pom.xml就行了，接下来我们要新建一个SpringConfiguation文件，这里面是SpringIoC主要的工作空间，注意要把新建的文件放在resourse文件下，然后我们新建三个文件夹，分别是dao，service，servlet，然后在三个文件夹下新建三个类，分别是BookDao，BookService，BookServlet，接下来，我们通过注解来分别创建Dao，Service，Controller，显然我们只要在创建的三个类上分别为它们加上@Repository，@Service，@Controller，然后还有重要的一步，在Spring配置文件中设置自动扫描的属性

```xml
 <context:component-scan base-package="dao"></context:component-scan>
    <context:component-scan base-package="service"></context:component-scan>
    <context:component-scan base-package="servlet"></context:component-scan>
```

这样，Spring就会把设置注解的类，加入容器中管理了，那我们来测试以下看看吧

```java
public class Test01 {

    ApplicationContext ioc = new ClassPathXmlApplicationContext("ioc.xml");
    @Test
    public void test() {
        BookDao bookDao = (BookDao)ioc.getBean("bookDao");
        System.out.println(bookDao);
    }
}
```

