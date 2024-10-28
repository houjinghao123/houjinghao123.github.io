+++
title = 'SpringSecurity学习'
date = 2024-10-24T23:30:35+08:00
categories = ["Spring Security"]
tags= ["SpringBoot","Spring Security"]
+++

# SpringSecurity学习

## 1、什么是SpringSecurity
**Spring Security** 是 Spring 家族中的一个安全管理框架。相比与另外一个安全框架**Shiro**，它提供了更丰富的功能，社区资源也比Shiro丰富。

​	一般来说中大型的项目都是使用**SpringSecurity** 来做安全框架。小项目有Shiro的比较多，因为相比与SpringSecurity，Shiro的上手更加的简单。

​	 一般Web应用的需要进行**认证**和**授权**。

​		**认证：验证当前访问系统的是不是本系统的用户，并且要确认具体是哪个用户**

​		**授权：经过认证后判断当前用户是否有权限进行某个操作**

​	而认证和授权也是SpringSecurity作为安全框架的核心功能。

- SpringSecurity的完成流程

SpringSecurity的原理其实就是一个过滤器链，内部包含了提供各种功能的过滤器。
![](https://i.postimg.cc/NFBkB6Y9/screenshot-53.png)

**UsernamePasswordAuthenticationFilter**:负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。

**ExceptionTranslationFilter**：处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException 。

**FilterSecurityInterceptor**：负责权限校验的过滤器。



## 2、SpringSecurity快速入门

### 2.1 准备工作
- 首先创建一个Maven项目并导入依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
```
- 然后创建SpringBoot的启动项

```java
@SpringBootApplication
public class SecurityApplication {

    public static void main(String[] args) {
        SpringApplication.run(SecurityApplication.class,args);
    }
}

```
- 创建一个Controller类

```java

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        return "hello";
    }
}

```
### 2.2引入SpringSecurity相关依赖

```xml
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
```
在引入该依赖后，我们在访问/hello接口时会自动跳转到security自带的登录界面。
登录的用户名默认是 user，登录的密码会在控制台输出。
![](https://i.postimg.cc/fyPn6RnP/screenshot-52.png)

## 3、认证

### 3.1 简单认证流程
![](https://i.postimg.cc/sDSGwQRb/screenshot-54.png)
Authentication接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。

AuthenticationManager接口：定义了认证Authentication的方法

UserDetailsService接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

UserDetails接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。

### 3.2自定义登录用户查询
从之前的分析我们可以知道，我们可以自定义一个UserDetailsService,让SpringSecurity使用我们的UserDetailsService。我们自己的UserDetailsService可以从数据库中查询用户名和密码。

- 我们先创建我们需要的用户信息表

```sql
CREATE TABLE `sys_user` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '用户名',
  `nick_name` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '昵称',
  `password` VARCHAR(64) NOT NULL DEFAULT 'NULL' COMMENT '密码',
  `status` CHAR(1) DEFAULT '0' COMMENT '账号状态（0正常 1停用）',
  `email` VARCHAR(64) DEFAULT NULL COMMENT '邮箱',
  `phonenumber` VARCHAR(32) DEFAULT NULL COMMENT '手机号',
  `sex` CHAR(1) DEFAULT NULL COMMENT '用户性别（0男，1女，2未知）',
  `avatar` VARCHAR(128) DEFAULT NULL COMMENT '头像',
  `user_type` CHAR(1) NOT NULL DEFAULT '1' COMMENT '用户类型（0管理员，1普通用户）',
  `create_by` BIGINT(20) DEFAULT NULL COMMENT '创建人的用户id',
  `create_time` DATETIME DEFAULT NULL COMMENT '创建时间',
  `update_by` BIGINT(20) DEFAULT NULL COMMENT '更新人',
  `update_time` DATETIME DEFAULT NULL COMMENT '更新时间',
  `del_flag` INT(11) DEFAULT '0' COMMENT '删除标志（0代表未删除，1代表已删除）',
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='用户表'
```

- 在刚才的初始项目中引入MybatisPuls和mysql驱动的依赖
```xml
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```
- 在资源目录下创建application.ymal文件导入下面的配置,记得修改自己的数据库密码
```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sg_security?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

- 定义Mapper接口
```java
public interface UserMapper extends BaseMapper<User> {
}
```
- 创建用户实体类
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@TableName(value = "sys_user")
public class User implements Serializable {
    private static final long serialVersionUID = -40356785423868312L;

    /**
     * 主键
     */
    @TableId
    private Long id;
    /**
     * 用户名
     */
    private String userName;
    /**
     * 昵称
     */
    private String nickName;
    /**
     * 密码
     */
    private String password;
    /**
     * 账号状态（0正常 1停用）
     */
    private String status;
    /**
     * 邮箱
     */
    private String email;
    /**
     * 手机号
     */
    private String phonenumber;
    /**
     * 用户性别（0男，1女，2未知）
     */
    private String sex;
    /**
     * 头像
     */
    private String avatar;
    /**
     * 用户类型（0管理员，1普通用户）
     */
    private String userType;
    /**
     * 创建人的用户id
     */
    private Long createBy;
    /**
     * 创建时间
     */
    private Date createTime;
    /**
     * 更新人
     */
    private Long updateBy;
    /**
     * 更新时间
     */
    private Date updateTime;
    /**
     * 删除标志（0代表未删除，1代表已删除）
     */
    private Integer delFlag;
}
```

- 在启动类中配置mapper扫描
```java
@SpringBootApplication
@MapperScans({@MapperScan("com.sg.mapper")})
public class App2
{
    public static void main( String[] args )
    {
        SpringApplication.run(App2.class);
    }
}

```

- 核心代码实现

创建一个类实现UserDetailsService接口，重写其中的方法。更加用户名从数据库中查询用户信息
```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //根据用户名查询用户信息
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(wrapper);
        //如果查询不到数据就通过抛出异常来给出提示
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或密码错误");
        }
        //TODO 根据用户查询权限信息 添加到LoginUser中

        //封装成UserDetails对象返回
        return new LoginUser(user);
    }
}
```
因为UserDetailsService方法的返回值是UserDetails类型，所以需要定义一个类，实现该接口，把用户信息封装在其中。

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class LoginUser implements UserDetails {
    private User user;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}

```

然后我们运行项目使用数据库中的用户名和密码登录就会发现无法登录，这是因为spring security自带的有密码加密
我们想要在这次测试中不使用密码就需要在数据库中的密码前面加上{noop}这个表示不进行密码加密，

![](https://i.postimg.cc/QdzLCtFx/screenshot-55.png)

但是在日后的开发中
我们的用户创建个人账户时肯定是需要加密的，这个加密方式的选择就需要在WebSecurityConfigurerAdapter中进行编写了。
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}
```
这里我们使用的是BCryptPasswordEncoder方法进行的加密吗，但是需要注意这里的加密的意思是把密码加密后与数据库中已经加密的密码进行比较
因此我们需要把加密后的密码存入数据库，我们可以在测试方法中调用BCryptPasswordEncoder来获取正常密码加密后的密码。
```java
@SpringBootTest
public class AppTest {
    @Test
    public void Test2(){
        BCryptPasswordEncoder bc=new BCryptPasswordEncoder();
        String encode = bc.encode("1234");
        System.out.println(encode);
        System.out.println(bc.matches("1234", "$2a$10$dtsT5D5X5YjHKXYCNYH.KeEIbgp.EOpOL6ILDVHAp82SHk1oRCjla"));
    }

}

```
在进行登录测试就可以正常登录了

### 3.3自定义登录接口
我们上面完成了自定义用户信息登录的测试，但是在前后端分离的项目中我们是需要一个登录的接口，具体的登录界面是由前端实现的所以我们需要自定义登录接口
首先就是在controller中定义一个相关接口

```java
@RestController
public class LoginController {

    @Autowired
    private LoginServcie loginServcie;

    @PostMapping("/user/login")
    public String login(@RequestBody User user){
        return loginServcie.login(user);
    }
}
```
```java
public interface LoginServcie {
    String login(User user);
}
```
```java
@Component
public class LoginServcieImpl implements LoginServcie {
    @Override
    public String login(User user) {
        return "登录成功";
    }
}
```
在定义完该接后我们还需要对securityConfig进行一些相关设置使/user/login接口不需要登录
```java
 */
@Configuration
public class securityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private AuthenticationSuccessHandler successHandler;

    //设置密码编码格式
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //启用默认的登录表单
        http.formLogin();
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();


    }
}
```
我们这里定义的登录接口是使用post方法并且需要携带参数所以就使用postman进行测试

- 这是我们正常请求登录时
![](https://i.postimg.cc/zvmHrKZm/screenshot-57.png)

- 但是请求其他接口时都需要进行登录验证

![](https://i.postimg.cc/43ShWjWx/screenshot-56.png)

