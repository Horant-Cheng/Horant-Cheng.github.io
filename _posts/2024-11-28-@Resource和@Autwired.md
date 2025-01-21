---
title: @Resource和@Autwired
date: 2024-11-28 14:30:45 +0800
categories: [SpringBoot, Annotations]
tags: [springboot]     # TAG names should always be lowercase
---

`@Resource` 是 Java 提供的一个注解（位于 `javax.annotation` 包中），用于依赖注入。它的功能与 Spring 的 `@Autowired` 注解类似，但有一些区别。以下是 `@Resource` 的详细说明和使用场景：

---

### 1. **`@Resource` 的作用**
`@Resource` 注解用于将 Spring 容器中的 Bean 注入到目标字段、方法或构造函数中。它可以用来注入以下内容：
- 其他 Spring Bean。
- 资源（如数据源、JNDI 资源等）。

---

### 2. **`@Resource` 的使用方式**
`@Resource` 可以标注在字段、方法或构造函数上。以下是常见的使用方式：

#### 字段注入
```java
@Service
public class MyService {

    @Resource
    private DataSource dataSource;
}
```

#### 方法注入
```java
@Service
public class MyService {

    private DataSource dataSource;

    @Resource
    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```

#### 构造函数注入
```java
@Service
public class MyService {

    private final DataSource dataSource;

    @Resource
    public MyService(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```

---

### 3. **`@Resource` 的注入规则**
`@Resource` 的注入规则与 `@Autowired` 不同，具体如下：

#### 默认按名称注入
- 如果没有指定名称，`@Resource` 默认按字段名或方法名查找 Bean。
- 例如，以下代码会查找名为 `dataSource` 的 Bean：
  ```java
  @Resource
  private DataSource dataSource;
  ```

#### 指定名称注入
- 可以通过 `name` 属性指定要注入的 Bean 名称。例如：
  ```java
  @Resource(name = "myDataSource")
  private DataSource dataSource;
  ```

#### 按类型注入
- 如果未找到指定名称的 Bean，`@Resource` 会尝试按类型注入。
- 例如，以下代码会查找类型为 `DataSource` 的 Bean：
  ```java
  @Resource
  private DataSource dataSource;
  ```

---

### 4. **`@Resource` 与 `@Autowired` 的区别**
| 特性                  | `@Resource`                         | `@Autowired`                         |
| --------------------- | ----------------------------------- | ------------------------------------ |
| **来源**              | Java 标准注解（`javax.annotation`） | Spring 注解                          |
| **默认注入规则**      | 按名称注入                          | 按类型注入                           |
| **支持 `required`**   | 不支持                              | 支持（`@Autowired(required=false)`） |
| **支持构造函数注入**  | 支持                                | 支持                                 |
| **支持 `@Qualifier`** | 不支持                              | 支持                                 |

---

### 5. **使用场景**
- **按名称注入**：
  - 如果你希望按名称注入 Bean，可以使用 `@Resource`。
  - 例如：
    ```java
    @Resource(name = "myDataSource")
    private DataSource dataSource;
    ```

- **与 JNDI 资源集成**：
  
  - `@Resource` 最初设计用于 JNDI 资源注入，因此在需要注入 JNDI 资源时，`@Resource` 是更好的选择。
  
- **兼容性**：
  
  - 如果项目需要在**非 Spring 环境中运行（如 Java EE 容器）**，`@Resource` 是更好的选择，因为它是 Java 标准注解。

---

### 6. **示例代码**
使用 `@Resource` 注入 `DataSource` Bean：

#### 配置类
```java
@Configuration
public class AppConfig {

    @Bean(name = "myDataSource")
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("user");
        dataSource.setPassword("password");
        return dataSource;
    }
}
```

#### 服务类
```java
@Service
public class MyService {

    @Resource(name = "myDataSource")
    private DataSource dataSource;

    public void queryData() {
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        List<Map<String, Object>> result = jdbcTemplate.queryForList("SELECT * FROM my_table");
        System.out.println(result);
    }
}
```

---

### 总结
`@Resource` 用处范围更广，且为java标准注释，可以按名称以及类型等进行注释，而`@Autowired`只能按类型注入