### 导包

- commons-logging-1.1.1.jar
- spring-aop-4.0.0.RELEASE.jar
- spring-beans-4.0.0.RELEASE.jar
- spring-context-4.0.0.RELEASE.jar
- spring-core-4.0.0.RELEASE.jar
- spring-expression-4.0.0.RELEASE.jar
- spring-web-4.0.0.RELEASE.jar
- spring-webmvc-4.0.0.RELEASE.jar

在tomcat里面，第一个包如果不导的话，会出错误，SpringMVC是一定需要这个包的，

### 配置

在WEB-INF目录下面的web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">

  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  
  <servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:conf.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

里面配置SpringMVC的视图分发器，非常重要，还需要一个spring的配置文件，这个配置文件放在类路径下面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<context:component-scan base-package="com.tom"></context:component-scan>

	<bean class="org.springframework.web.servlet.view.UrlBasedViewResolver">
		<property name="prefix" value="/WEB-INF/pages/" />
		<property name="suffix" value=".jsp" />
		 <property name="viewClass" value="org.springframework.web.servlet.view.InternalResourceView"/>  
	</bean>
</beans>
```

里面用来设置自动扫描包，这样就可以用注解， 特别注意的是，如果是自己手动添加上面的命名空间，一定注意格式，

### 写控制器类

在com.tom.controller的包下新建一个HelloControler.java

```java
package com.tom.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloControler {

	@RequestMapping("/hello")
	public String helloController() {
		return "success";
	}
}
```

这个类有两个注解，一个是Controller，表示这个类是一个控制器类，下面的那个RequestMappring，表明这个控制器来回应哪个浏览器请求，下面的那个方法返回的字符串就是返回的页面的地址

在WEB-INF下面新建一个pages文件夹，里面放一个success.jsp，这样整个项目在Tomcat上运行就可以了