---
title: Spring Boot Bean注册到容器的方式
date: 2024-10-28 14:30:45 +0800
categories: [SpringBoot, Bean]
tags: [springboot, bean]     # TAG names should always be lowercase
---

### 1. 使用xml文件进行配置

使用xml文件配置bean，并定义bean的依赖关系

```java

<beans 省略....>
    <bean id="myService" class="com.example.MyService"/>
</beans>


```

### 2. 使用@Component 及其衍生类

使用注解如`@Service` `@Component` `Controller` `@Repository`

```java
@Component
public class MyComponent {
    // 业务逻辑
}
```

### 3. 使用@Configuration 以及 @Bean

使用@Configurantion 声明配置类，并用 @Bean 注解定义Bean

```java
@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        return new MyBean(myDependency());
    }

    @Bean
    public MyDependency myDependency() {
        return new MyDependency();
    }
}
```

### 4. 使用@import

使用@import 可以将普通类导入到配置容器中，并将其自动注册为Bean

```java
@Configuration
@Import(MyComponent.class)
public class MainConfig {
    // 当前配置类逻辑
}

public class MyComponent {
    // 普通类逻辑
}
```

