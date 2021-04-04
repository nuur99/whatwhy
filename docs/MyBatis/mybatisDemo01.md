### 导包

根据官方文档下载jar包，然后导入项目就行了

### 新建一个数据库，并创建一个表

```sql
CREATE TABLE `t_cat` (
  `cat_id` int(11) DEFAULT NULL,
  `cat_name` varchar(20) DEFAULT NULL,
  `cat_age` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into t_cat values(1, "tom", 5);
insert into t_cat values(2, "jerry", 6);
insert into t_cat values(3, "jack", 7);
```

这样我们的数据库就有数据了

#### 创建一个对应的类

```java
package com.mybatis.beans;

public class Cat {
	private Integer cat_id;
	private String cat_name;
	private Integer cat_age;
	public Integer getCat_id() {
		return cat_id;
	}
	public void setCat_id(Integer cat_id) {
		this.cat_id = cat_id;
	}
	public String getCat_name() {
		return cat_name;
	}
	public void setCat_name(String cat_name) {
		this.cat_name = cat_name;
	}
	public Integer getCat_age() {
		return cat_age;
	}
	public void setCat_age(Integer cat_age) {
		this.cat_age = cat_age;
	}
	@Override
	public String toString() {
		return "Cat [cat_id=" + cat_id + ", cat_name=" + cat_name + ", cat_age=" + cat_age + "]";
	}
	
}
```

#### 写一个CatDao接口

```java
package com.mybatis.dao;

import com.mybatis.beans.Cat;

public interface CatDao {
	
	/**
	 * 查询方法
	 * @param id
	 * @return
	 */
	Cat QueryCatById(Integer id);
	/**
	 * 删除方法
	 * @param id
	 * @return
	 */
	int DeleteCatById(Integer id);
	/**、
	 * 插入方法
	 * @param cat
	 * @return
	 */
	int InsertCat(Cat cat);
	/**
	 * 更新方法
	 * @param cat
	 * @return
	 */
	int UpdateCat(Cat cat);

}

```

接下来就要写mybatis的配置文件了，我们要先写一个全局的配置文件

### 全局配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<properties resource="database.properties"/>
<settings>
		<setting name="logImpl" value="LOG4J" />
	</settings>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
   
  </mappers>
</configuration>
```

```xml
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf-8
user=root
password=root
```

注意连接数据库要导入相关jar包

### 接下来编写CatDao的映射sql语句

我们要新建一个xml文件，名字也叫CatDao,

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.dao.CatDao">
  <select id="QueryCatById" resultType="com.mybatis.beans.Cat">
    select * from t_cat where cat_id = #{id}
  </select>
</mapper>
```

最后一步，在mybatis的全局配置文件配置这一个CatDao

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<properties resource="database.properties"/>
<settings>
		<setting name="logImpl" value="LOG4J" />
	</settings>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="com/mybatis/dao/CatDao.xml"/>
  </mappers>
</configuration>
```

### 测试

新建一个测试类CatDaoTest

```java
public class CatDaoTest {

	@Test
	public void testQuery() throws IOException {
		String resource = "mybatis_config.xml";
		SqlSession openSession = null;
		try {
			InputStream inputStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			openSession = sqlSessionFactory.openSession();
			CatDao session = openSession.getMapper(CatDao.class);
			Cat cat = session.QueryCatById(1);
			System.out.println(cat);
		}finally{
			openSession.close();
		}
		
	}
}
//输出
Cat [cat_id=1, cat_name=tom, cat_age=5]
```

接下来把其他三个方法补全

```java
package com.mybatis.test;

import java.io.IOException;
import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import com.mybatis.beans.Cat;
import com.mybatis.dao.CatDao;

public class CatDaoTest {

	@Test
	public void testQuery() throws IOException {
		String resource = "mybatis_config.xml";
		SqlSession openSession = null;
		try {
			InputStream inputStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			openSession = sqlSessionFactory.openSession(true);
			CatDao session = openSession.getMapper(CatDao.class);
			Cat cat = session.QueryCatById(1);
			System.out.println(cat);
		}finally{
			openSession.close();
		}
		
	}
	@Test
	public void testDelete() throws IOException {
		String resource = "mybatis_config.xml";
		SqlSession openSession = null;
		try {
			InputStream inputStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			openSession = sqlSessionFactory.openSession(true);
			CatDao session = openSession.getMapper(CatDao.class);
			int delete = session.DeleteCatById(1);
			System.out.println(delete);
		}finally{
			openSession.close();
		}
		
	}
	@Test
	public void testInsert() throws IOException {
		String resource = "mybatis_config.xml";
		SqlSession openSession = null;
		try {
			InputStream inputStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			openSession = sqlSessionFactory.openSession(true);
			CatDao session = openSession.getMapper(CatDao.class);
			Cat cat = new Cat();
			cat.setCat_name("tom1");
			cat.setCat_age(3);
			int insert = session.InsertCat(cat);
			System.out.println(insert);
		}finally{
			openSession.close();
		}
		
	}
	@Test
	public void testUpdate() throws IOException {
		String resource = "mybatis_config.xml";
		SqlSession openSession = null;
		try {
			InputStream inputStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			openSession = sqlSessionFactory.openSession(true);
			CatDao session = openSession.getMapper(CatDao.class);
			Cat cat = new Cat();
			cat.setCat_id(2);
			cat.setCat_name("tom1");
			cat.setCat_age(3);
			int update = session.UpdateCat(cat);
			System.out.println(update);
		}finally{
			openSession.close();
		}
	}
}

```

