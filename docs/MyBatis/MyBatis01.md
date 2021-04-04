### 是什么

这里我们可以看以下官网给出的解释：

***MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis        免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis        可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old        Java Objects，普通老式 Java 对象）为数据库中的记录。***

### 怎么用

官方文档描述，每个基于MyBatis的应用都是以一个SqlSessionFactory 为核心，它的实例乐意通过SqlSessionFactoryBuilder 获得，而这个又可以通过xml文件获得，所以让我们先从一个xml文件写起

​      

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<!-- 通过这个配置文件完成mybatis与数据库的连接 -->
<configuration>
	<!-- 引入 database.properties 文件-->
	<properties resource="database.properties"/>
	
	<!-- 配置mybatis的log实现为LOG4J -->
	<settings>
		<setting name="logImpl" value="LOG4J" />
	</settings>
	
	<!--类型别名-->
	<typeAliases>
	 	<package name="cn.smbms.pojo"/>
	</typeAliases>
	
	<environments default="development">
		<environment id="development">
			<!--配置事务管理，采用JDBC的事务管理  -->
			<transactionManager type="JDBC"></transactionManager>
			<!-- POOLED:mybatis自带的数据源，JNDI:基于tomcat的数据源 -->
			<dataSource type="POOLED">
				<property name="driver" value="${driver}"/>
				<property name="url" value="${url}"/>
				<property name="username" value="${user}"/>
				<property name="password" value="${password}"/>
			</dataSource>
		</environment>
	</environments>
	
	<!-- 将mapper文件加入到配置文件中 -->
	<mappers>
		<mapper resource="cn/smbms/dao/bill/BillMapper.xml"/>
		<mapper resource="cn/smbms/dao/provider/ProviderMapper.xml"/>
		<!-- 在此处添加新的mapper映射文件 -->
		
	</mappers>



</configuration>

```

​        有了这个配置文件，我们就可以连接数据库了，在我们真正开始之前，先做一些准备工作，我们需要定义一个实体类，实体类会映射到数据库的一个表中

```java
public class Provider {
	private Integer id;   //id
	private String proCode; //供应商编码
	private String proName; //供应商名称
	private String proDesc; //供应商描述
	private String proContact; //供应商联系人
	private String proPhone; //供应商电话
	private String proAddress; //供应商地址
	private String proFax; //供应商传真
	private Integer createdBy; //创建者
	private Date creationDate; //创建时间
	private Integer modifyBy; //更新者
	private Date modifyDate;//更新时间
    
    //getter,setter方法
}
```

现在我们写完实体类了，我们需要写一个接口，里面描述了操作实体类的方法

```java
public interface ProviderMapper {
	/**
		 * 查询供应商表记录数
		 * @return
		 */
		public int count();
		/**
		 * 查询供应商列表
		 * @return
		 */
		public List<Provider> getProviderList();
		/**
		 * 根据供应商名称查询供应商列表(模糊查询)
		 * @param proName
		 * @return
		 */
		public List<Provider> getProviderListByProName(String proName);
		
		/**
		 * 增加供应商
		 * @param provider
		 * @return
		 */
		public int add(Provider provider);
		
		/**
		 * 修改供应商信息
		 * @param provider
		 * return
		 */
		public int modify(Provider provider);
		
		/**
		 * 根据供应商ID删除供应商信息
		 * @param delId
		 * return
		 */
		public int deleteProviderById(@Param("id")Integer delId);
}
```

接下来就是MyBatis的特性了，我们要建立一个xml文件，然后为每一个方法映射所需要的sql语句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="cn.smbms.dao.provider.ProviderMapper">

	<!-- 查询供应商表记录数 -->
	<select id="count" resultType="int">
		select count(1) as count from smbms_provider
	</select>
	
	<!-- 查询供应商列表 -->
	<select id="getProviderList" resultType="cn.smbms.pojo.Provider">
		select * from smbms_provider
	</select>
	<!-- 根据供应商名称查询供应商列表(模糊查询) -->
	<select id="getProviderListByProName" resultType="cn.smbms.pojo.Provider" parameterType="String">
		select * from smbms_provider where proName like CONCAT ('%',#{proName},'%')
	</select>
	<!--增加供应商-->
	<insert id="add" parameterType="Provider">
	insert into smbms_provider (proCode,proName,proDesc,proContact,proPhone,
	proAddress,proFax,createdBy,creationDate)
	values (#{proCode},#{proName},#{proDesc},#{proContact},#{proPhone},#{proAddress},#{proFax},#{createdBy},#{creationDate})
	</insert>
	<!--修改供应商信息-->
	<update id="modify" parameterType="Provider">
	update smbms_provider set proCode=#{proCode},proName=#{proName},proDesc=#{proDesc},
	proContact=#{proContact},proPhone=#{proPhone},proAddress=#{proAddress},proFax=#{proFax},modifyBy=#{modifyBy},modifyDate=#{modifyDate}
	where id = #{id}
	</update>
	<!--根据供应商ID删除供应商信息-->
	<delete id="deleteProviderById" parameterType="Integer">
	delete from smbms_provider where id = #{id}
	</delete>
	
</mapper>

```

我们写完一个映射文件，就要去总的配置文件去注册，在<mappers></mappers>下面注册，这样我们准备工作就完成了，接下来我们要实验 一下增加的方法

```java
public class MyBatisUtil {
	private static SqlSessionFactory factory;
	
	static{//在静态代码块下，factory只会被创建一次
		System.out.println("static factory===============");
		try {
			InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
			factory = new SqlSessionFactoryBuilder().build(is);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} 
	}
	
	public static SqlSession createSqlSession(){
		return factory.openSession(false);//true 为自动提交事务
	}
	
	public static void closeSqlSession(SqlSession sqlSession){
		if(null != sqlSession) 
			sqlSession.close();
	}
}
```



```Java
@Test
	public void testAdd(){
		logger.debug("testAdd !===================");
		SqlSession sqlSession = null;
		int count = 0;
		try {
			sqlSession = MyBatisUtil.createSqlSession();
			Provider provider = new Provider();
			provider.setProCode("BJ_GYS888");
			provider.setProName("黄牛集团");
			provider.setProContact("李想");
			provider.setProAddress("拉不拉几呀");
			provider.setProPhone("8888888888");
			provider.setCreatedBy(1);
			provider.setCreationDate(new Date());
			provider.setProFax("010-588876565");
			provider.setProDesc("酒");
			count = sqlSession.getMapper(ProviderMapper.class).add(provider);
			//模拟异常，进行回滚
			//int i = 2/0;
			sqlSession.commit();
		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
			sqlSession.rollback();
			count = 0;
		}finally{
			MyBatisUtil.closeSqlSession(sqlSession);
		}
		logger.debug("testAdd count: " + count);
	}
```

到此，大致的过程就完成了！