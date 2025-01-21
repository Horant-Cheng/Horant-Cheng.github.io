---
title: SpringBoot默认可同时处理的最大连接数是多少？
date: 2024-12-24 14:30:45 +0800
categories: [SpringBoot, Bean]
tags: [springboot, bean]     # TAG names should always be lowercase
---

###  定义

通过Spring 容器初始化、组装和管理的java对象都可以成为Spring Bean。Bean可以在Spring 容器中通过被定义以及通过依赖注入与其他Bean进行相互依赖。

Bean可以看作Spring中的一个对象，其生命周期（创建，初始化，使用，销毁）完全由Spring容器进行管理。



### Spring Bean 的生命周期

1. 实例化：在Spring容器启动时，根据注解或者配置文件，会首先实例化Bean
2. 依赖注入：在实例化后，Spring 会通过构造器，setter方法或注解将其他Bean的依赖注入进来
3. 初始化：如果实现了InitializingBean 方法或使用@PostConstruct，在依赖注入完成后会调用相应的初始化方法
4. 销毁：如果实现了DisposableBean方法或使用@PreDestroy 注解，在Spring容器时调用销毁方法。





### 定义Spring Bean的方式

#### XML 配置

早期的 Spring 应用通常通过 XML 文件定义 Bean，使用 `<bean>` 标签来指定类、构造器参数和依赖关系。

#### 基于注解的配置
使用 `@Component`、`@Service`、`@Repository`、`@Controller` 等注解可以将类标记为 Spring Bean，Spring 会自动扫描这些类并将其注册为 Bean。

#### Java 配置类
通过 `@Configuration` 和 `@Bean` 注解，可以在 Java 类中手动定义 Bean。相比 XML 配置，这种方式更加简洁和类型安全。
