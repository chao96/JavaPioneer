
# Shiro

## 一、Shiro简介

1.Shiro是Java的一个安全框架

2.主要功能：身份认证、授权、加密、会话管理

3.**基本功能简介如下：**

![](F:\learn\docs\Shiro\img\1基本功能简介.png)

+ **Authentication：身份认证；**验证用户是否拥有相应身份。
+ **Authorization：权限验证；**验证某个已认证的用户是否拥有某个权限。
+ **Session Manager：会话管理；**用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中。
+ **Cryptography：加密；**保护数据安全，如密码加密存到数据库。
+ **Web Support：Web支持；**集成Web环境。
+ **Caching：缓存；**用户登录后，其角色、权限等信息不必每次去查，提高效率。
+ **Concurrency：Shiro支持多线程应用的并发验证；**如在一个线程中开启另一个线程，可以把权限自动传播过去
+ **Testing：测试支持；**
+ **Run As：允许一个用户假装为另一个用户身份进行访问**。
+ **Remember Me：记住我；**如一次登录后，下次不用再登录。

## 二、Shiro架构

**1.Shiro架构（外部来看）**

![image-20200226102130755](C:\Users\xuchao\AppData\Roaming\Typora\typora-user-images\image-20200226102130755.png)

+ **Subject：应用代码直接交互的对象；**Shiro对外API的核心是Subject，Subject代表了当前用户，与Subject的所有交互都会委托给SecurityManager；Subject是一个门面，SecurityManager是实际的执行者。
+ **SecurityManager：安全管理器；**所有与安全有关的操作都会与SecurityManager交互，它是Shiro的核心，负责与Shiro的其他组件进行交互。
+ **Realm：Shiro从Realm获取安全数据（如用户、角色、权限）。**SecurityManager要验证用户身份，需要从Realm获取相应的用户信息验证。

**2.Shiro架构（内部来看）**

![](F:\learn\docs\Shiro\img\3Shiro架构（内部）.png)

+ **Subject：任何可以与应用交互的用户**。
+ **SecurityManager：Shiro的核心；**所有具体的交互都通过SecurityManager控制，管理着所有Subject，负责进行认证、授权、会话及缓存的管理。相当于SpringMVC中的DispatcherServlet；
+ **Authenticator：负责Subject认证；**可以自定义实现，什么情况下算用户认证通过。
+ **Authorizer：控制用户权限；**
+ **Realm：可以认为是安全实体数据源；**由用户提供，一般在应用中都需要实现自己的Realm；
+ **SessionManager：管理Session生命周期的组件**。
+ **CacheManager：缓存控制器。**缓存用户信息。
+ **Cryptography：密码模块。**加密解密操作

## 三、SpringBoot + Shiro实现用户认证

### 1.引入依赖包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.sprintboot</groupId>
    <artifactId>shiro</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>shiro</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!--数据库连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.19</version>
        </dependency>
        <!--thymeleaf和shiro-->
        <dependency>
            <groupId>com.github.theborakompanioni</groupId>
            <artifactId>thymeleaf-extras-shiro</artifactId>
            <version>2.0.0</version>
        </dependency>
        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.2</version>
        </dependency>

        <!--Shiro和spring整合依赖-->
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.4.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- mysql -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.15</version>
            <scope>runtime</scope>
        </dependency>

        <!--支持jsp页面跳转-->
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <version>9.0.12</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.web</groupId>
            <artifactId>javax.servlet.jsp.jstl</artifactId>
            <version>1.2.1</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 2.配置文件

```java

#配置端口号8080，默认就是8080
server.port=8080

#thymeleaf模板配置
spring.thymeleaf.suffix=.html
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.mode=HTML5
#页面缓存，默认配置true
spring.thymeleaf.cache=false

#配置静态资源路径
#spring.resources.static-locations=classpath:/METAINF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/,classpath:/templates/
spring.resources.static-locations=classpath:/static/,classpath:/templates/

#配置mysql
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/shiro?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
#配置mybatis扫描包
mybatis.type-aliases-package=com.springboot.shiro.entity
```

### 3.Shiro的核心API

subject：用户主体（把操作交给SecurityManager）
SecurityManager：安全管理器（关联Realm）
Realm：Shiro连接数据的桥梁

#### 1) shiro配置类

```java
/**
 * shiro配置类
 */
@Configuration
public class shiroConfig {
    /**
     * 创建ShiroFilterFactoryBean
     *
     * @param securityManager
     * @return
     */
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        // 设置shiro内置过滤器
        /**
         * shiro内置过滤器，可以实现权限相关的拦截器
         * 常用的过滤器：
         * anon：无需认证（登录）可以访问
         * authc：必须认证才可以登录访问
         * user：如果我们使用了rememberMe的功能可以直接访问
         * perms：必须得到资源权限才可以访问
         * role：必须得到角色权限才可以访问
         */
        Map<String,String> filterMap = new LinkedHashMap<String,String>();

        // 方法一：拦截指定页面认证
        /*
        filterMap.put("/user/add","authc");
        filterMap.put("/user/update","authc");
        */
        // 方法二：指定几个页面无需认证，其余全部拦截
        filterMap.put("/index","anon");
        filterMap.put("/user/login","anon");

        //添加授权过滤
        //注意：当授权拦截后，shiro会自动跳转到未授权页面
        filterMap.put("/user/add","perms[user:add]");
        filterMap.put("/user/update","perms[user:update]");
        filterMap.put("/**","authc");

        //拦截后跳转到登录页面认证
        shiroFilterFactoryBean.setLoginUrl("/user/tologin");
        //设置未授权提示页面
        shiroFilterFactoryBean.setUnauthorizedUrl("/user/unAuth");
        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterMap);
        return shiroFilterFactoryBean;
    }

    /**
     * 创建SecurityManager
     * 4.3、使用shiro内置过滤器实现页面拦截
     * 这是后比如说我们有两个用户的页面，一个添加，一个修改
     * 创建两个页面，进行测试
     * 创建test跳转这两个页面
     *
     * @param userRealm
     * @return
     */
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //关联realm
        securityManager.setRealm(userRealm);
        return securityManager;
    }

    /**
     * 创建realm
     */
    @Bean(name = "userRealm")
    public UserRealm getRealm() {
        return new UserRealm();
    }
}
```

#### 2) 自定义realm类

```java
/**
 * 自定义realm类
 *
 * @author xuchao
 * @since 2020/3/3 13:32
 */
public class UserRealm extends AuthorizingRealm {

    @Autowired
    private IUserService userService;

    /**
     * 执行授权逻辑
     *
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行授权逻辑");

        //给资源进行授权
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //添加资源的授权字符串
        //注意：字符串要和过滤器中保持一致
        //info.addStringPermission("user:add");
        //到数据库查询当前登录用户的授权字符串
        //获取当前用户的ID
        Subject subject = SecurityUtils.getSubject();
        //注意这个getPrincipal（）；是从下们的认证方法获取的，所以我们需要改下面的方法
        User user = (User) subject.getPrincipal();
        User user1 = userService.findById(user.getId());
        //在info里面添加上我们的授权字符串
        info.addStringPermission(user1.getPerms());
        return info;
    }

    /**
     * 执行认证逻辑
     *
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行认证逻辑");
        //判断用户名
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;

        User user = userService.findByName(token.getUsername());

        if (!token.getUsername().equals(user.getUsername())) {
            //说明用户名不存在！
            return null;
        }
        //判断密码
        return new SimpleAuthenticationInfo(user, user.getPassword(), "");
    }
}
```



