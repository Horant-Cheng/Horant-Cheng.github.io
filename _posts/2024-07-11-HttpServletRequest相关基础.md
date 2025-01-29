---
title: HttpServletRequest相关基础
date: 2024-07-11 14:30:45 +0800
categories: [SpringBoot]
tags: [springboot]     # TAG names should always be lowercase
---

### 什么是 `HttpServletRequest`？

`HttpServletRequest` 是 Java Servlet API 中的一个接口，定义在 `javax.servlet.http` 包中，用于表示客户端向服务器发送的 HTTP 请求。它提供了一系列方法，用于获取与请求相关的数据，包括请求参数、请求头、方法类型、会话信息等。

在 Java Web 开发中，`HttpServletRequest` 是 Servlet 处理客户端请求的核心对象。每当客户端（如浏览器）发起请求时，**Web 服务器会创建一个** `HttpServletRequest` 对象，表示该请求并将其传递给相应的 Servlet 或控制器。

------

### `HttpServletRequest` 的基本特性

1. **表示 HTTP 请求**：
   - 它是**对客户端 HTTP 请求的抽象封装，包括请求方法（GET、POST 等）、请求头、参数等**。
2. **作用域是单次请求**：
   - 它仅在一次请求的生命周期中有效，随着**请求的结束被销毁**。
3. **接口的实现**：
   - 由**服务器容器（如 Tomcat、Jetty 等）实现**，开发者不需要关心具体实现。

------

### 核心功能

#### 1. **获取请求参数**

用于从 URL、表单或请求体中提取参数：

- `getParameter(String name)`：获取单个参数值。
- `getParameterMap()`：返回包含所有参数的键值对映射。
- `getParameterNames()`：返回所有参数的名称。

示例：

```java
String username = request.getParameter("username");
Map<String, String[]> parameterMap = request.getParameterMap();
```

#### 2. **请求头信息**

用于读取请求中的头部信息，例如 User-Agent、Referer 等：

- `getHeader(String name)`：获取指定名称的请求头。
- `getHeaderNames()`：获取所有请求头名称。

示例：

```java
String userAgent = request.getHeader("User-Agent");
```

#### 3. **请求方法**

用于识别客户端使用的 HTTP 方法：

- `getMethod()`：返回请求方法（如 GET、POST）。

示例：

```java
String method = request.getMethod();  // 输出 "GET" 或 "POST"
```

#### 4. **获取请求路径和 URL 信息**

- `getRequestURI()`：获取请求的 URI。
- `getRequestURL()`：获取完整的 URL。
- `getContextPath()`：获取 Web 应用的上下文路径。
- `getQueryString()`：获取查询字符串。

示例：

```java
String uri = request.getRequestURI();
String query = request.getQueryString();
```

#### 5. **客户端信息**

用于获取请求发起者的信息：

- `getRemoteAddr()`：获取客户端 IP 地址。
- `getRemoteHost()`：获取客户端主机名。

示例：

```java
String ipAddress = request.getRemoteAddr();
```

#### 6. **请求体内容**

当使用 POST 方法提交表单或 JSON 数据时，可以读取请求体：

- `getInputStream()`：以字节流的形式读取请求体。
- `getReader()`：以字符流的形式读取请求体。

示例：

```java
BufferedReader reader = request.getReader();
StringBuilder body = new StringBuilder();
String line;
while ((line = reader.readLine()) != null) {
    body.append(line);
}
```

#### 7. **Session 管理**

通过 `HttpServletRequest` 可以获取当前会话：

- `getSession()`：获取或创建一个新的 `HttpSession`。
- `getSession(boolean create)`：如果 `create` 为 `false` 且会话不存在，则返回 `null`。

示例：

```java
HttpSession session = request.getSession();
session.setAttribute("user", "Alice");
```

#### 8. **请求属性**

- `setAttribute(String name, Object value)`：在请求范围内存储数据。
- `getAttribute(String name)`：获取请求范围内存储的数据。

示例：

```java
request.setAttribute("message", "Hello");
String message = (String) request.getAttribute("message");
```

#### 9. **文件上传**

在支持多部分请求的情况下，`HttpServletRequest` 也可以用于处理文件上传，通常结合第三方库（如 Apache Commons FileUpload）使用。

------

### 工作原理

1. **客户端发起请求**： 浏览器或其他客户端通过 URL 访问服务器，向服务器发送 HTTP 请求。
2. **服务器创建请求对象**： Web 服务器（如 Tomcat）解析 HTTP 请求，并创建一个 `HttpServletRequest` 对象，封装所有请求信息。
3. **请求对象传递给 Servlet**： Web 容器将 `HttpServletRequest` 传递给目标 Servlet 的 `doGet()` 或 `doPost()` 方法。
4. **Servlet 使用请求对象**： 在 Servlet 内部，通过 `HttpServletRequest` 获取请求参数、头部信息等，进行业务逻辑处理。

------

### 使用场景

1. **用户认证与会话管理**： 存储和验证用户登录信息，管理用户的登录状态。
2. **表单处理**： 处理用户提交的表单数据，提取参数进行验证或存储。
3. **文件上传**： 处理多部分请求，获取文件数据。
4. **RESTful API**： 在开发 RESTful 服务时，用于解析请求参数和请求体数据。

------

### 总结

`HttpServletRequest` 是 Java Web 开发的核心接口，它为处理客户端请求提供了丰富的方法，涵盖了从获取请求数据到管理会话的方方面面。理解 `HttpServletRequest` 的功能和使用方式是构建高效、健壮 Web 应用的基础。