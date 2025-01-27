---
title: Object和StringUtil以及其相关方法区分
date: 2025-01-11 14:30:45 +0800
categories: [SpringBoot]
tags: [springboot]     # TAG names should always be lowercase
---

------

### **1. `ObjectUtil` 和 `StringUtil` 的区别**

- **`ObjectUtil`**：主要用于对任意类型的对象进行操作或检查，包括 `null` 检查。
- **`StringUtil`**：专门用于字符串操作或检查，包括判断字符串是否为空、是否为特定格式等。

------

### **2. `isNotNull` 和 `isNotEmpty` 的区别**

#### **`isNotNull`**

- **方法定义**：检查对象是否不为 `null`。

- 常见用法：

  - 用于检查对象是否存在。

  - **适用范围**：可以用于任意对象类型（`Object`）。

  - 示例

    ：

    ```java
    Object obj = "Hello";
    if (ObjectUtil.isNotNull(obj)) {
        // 执行逻辑，obj 不为 null
    }
    ```

#### **`isNotEmpty`**

- **方法定义**：检查集合或字符串是否不为空（集合有元素，字符串长度大于 0 且不为 `null`）。

- 常见用法：

  - 对集合（如 `List`、`Set`）和字符串进行检查。

  - **适用范围**：字符串、集合类。

  - 示例：

    ```java
    String str = "Hello";
    if (StringUtil.isNotEmpty(str)) {
        // 执行逻辑，str 不为空且不为 null
    }
    ```

------

### **3. `isNotBlank` 与 `isNotEmpty` 的区别**

`isNotBlank` 是 `StringUtil` 提供的另一种方法，用于检查字符串是否**既不为空也不全是空格**。区别如下：

#### **`isNotBlank`**

- **方法定义**：检查字符串是否不为 `null` 且不全是空格。

- 常见用法：

  - 确保字符串有实际内容（非空白字符）。

  - **适用范围**：字符串。

  - 示例：

    ```java
    String str = "  ";
    if (StringUtil.isNotBlank(str)) {
        // 不会进入此逻辑，因为 str 是空格
    }
    ```

#### **`isNotEmpty`**

- **方法定义**：检查字符串是否不为 `null` 且长度大于 0。

- 区别：

  - `isNotEmpty` 不关心字符串是否全是空格，只要长度大于 0 就返回 `true`。

  - 示例

    ：

    ```java
    String str = "  ";
    if (StringUtil.isNotEmpty(str)) {
        // 会进入此逻辑，因为 str 的长度大于 0
    }
    ```

------

### **4. 什么时候用？**

| **检查目标**         | **使用方法**                | **原因**                                   |
| -------------------- | --------------------------- | ------------------------------------------ |
| **对象是否存在**     | `ObjectUtil.isNotNull`      | 用于任何类型的对象，判断是否为 `null`。    |
| **字符串是否非空**   | `StringUtil.isNotEmpty`     | 检查字符串是否有内容，但不关心是否是空格。 |
| **字符串是否非空白** | `StringUtil.isNotBlank`     | 确保字符串既非空，也不是仅由空格组成。     |
| **集合是否有内容**   | `CollectionUtil.isNotEmpty` | 检查集合是否不为 `null` 且有元素。         |

------

### **5. 代码中的适用场景**

#### **对于 `ObjectUtil.isNotNull`**

用于检查对象（如 `id`）是否存在，因为 `id` 是一个通用对象，可能是 `null`。适用于需要直接判断对象的逻辑。

#### **对于 `StringUtil.isNotBlank`**

用于检查字符串字段（如 `userRole`、`userAccount` 等）是否既非空字符串也不全是空格。这在字符串字段查询中很重要，避免构造无意义的查询条件。

#### **是否选择 `isNotEmpty`**

- 如果允许字符串包含空格但不为空，可以用 `isNotEmpty`。
- 在大多数情况下，`isNotBlank` 更常用，因为它能过滤掉全是空格的无效输入。

------

总结：

- **`ObjectUtil.isNotNull`** 适用于任何类型的非空检查，主要用于**非字符串对象**。
- **`StringUtil.isNotEmpty`** 适用于简单字符串是否有内容的判断（包括空格）。
- **`StringUtil.isNotBlank`** 是**更严格**的字符串检查，确保内容不是空白字符。