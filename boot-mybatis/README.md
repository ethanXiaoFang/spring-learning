## SpringBoot集成Mybatis实战

> mybatis是一款优秀的持久层框架，支持定制化SQL，存储过程和高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

### 起步

> SpringBoot可以通过`MyBatis-Spring-Boot-Starter`，快速集成Mybatis，只需在maven中引入依赖

```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>
```
`mybatis-spring-boot-starter`提供了以下功能：
- 自动检测现有数据源
- 创建并注册SQLSessionFactory的实例，该实例使用SqlSessionFactoryBean将该数据源作为输入
- 创建并注册在SqlSessionFactory中获取的SqlSessionTemplate实例
- 自动扫描Mapper并链接到SqlSessionTemplate，并将它们注册到Spring上下文中，这样它们就能在Bean中被注入
<!-- more -->
> 引入依赖之后，还需要在配置文件中添加JDBC基本的配置

```yaml
mybatis:
  type-aliases-package: org.boot.mybatis.model
  type-handlers-package: org.boot.mybatis.typehandler
  configuration:
    map-underscore-to-camel-case: true
    default-fetch-size: 100
    default-statement-timeout: 30
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    password: root
    username: root
    url: jdbc:mysql://localhost:3306/test
```

> 配置完成之后，编写数据访问层(DAO)，也就是`Mybatis`文档上写的`Mapper`，提供对`User`表的`CRUD`操作

```java
@Mapper
public interface UserMapper {
    /**
     * 根据ID查询用户
     *
     * @param id
     * @return
     */
    @Select("select * from user where id = #{id}")
    User findById(Integer id);

    /**
     * 添加一条用户数据
     *
     * @param user
     */
    @Insert("insert into user(name, password, state, address, email) values (#{name}, #{password}, #{state}, #{address}, #{email})")
    void insert(User user);

    /**
     * 更新用户数据
     *
     * @param user
     */
    @Update("update user set name=#{name},password=#{password},state=#{state},address=#{address},email=#{email} where id=#{id}")
    void update(User user);

    @Delete("delete from user where id = #{id}")
    void delete(Integer id);

    /**
     * 查询指定状态的用户列表
     *
     * @param state
     * @return
     */
    @Select("select * from user where id = #{state}")
    List<User> selectList(Integer state);
}

```