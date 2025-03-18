---

> # 【Java】Java swing 民宿管理系统 GUI（源码+可视化界面）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
本系统是一个基于Java Swing的民宿管理系统，包含登录、注册以及不同角色（房东、管理员、普通用户）的界面和功能。系统的核心需求如下：

#### 一、用户管理功能
1. **用户注册**：
    - 用户可以通过注册界面输入手机号、密码、昵称、身份证号，并选择身份（房东、管理员、普通用户）进行注册。
    - 注册时需要检查手机号是否已注册，若已注册则提示错误信息。
2. **用户登录**：
    - 用户可以通过登录界面输入手机号和密码进行登录。
    - 系统根据用户的身份（房东、管理员、普通用户）跳转到相应的功能界面。
    - 若登录失败，系统提示手机号或密码错误。



![](https://i-blog.csdnimg.cn/direct/7d1ca7e8ae434ec6acea76cab22c379a.png)

#### 二、房东管理功能
1. **用户管理**：
    - 房东可以查看系统中的用户列表，包括用户ID和用户名。
    - 房东可以添加、删除和修改用户信息。
2. **房间管理**：
    - 房东可以查看系统中的房间列表，包括房间ID和房间名。
    - 房东可以添加、删除和修改房间信息。
3. **动态管理**：
    - 房东可以查看系统中的动态列表，包括动态ID和内容。
    - 房东可以添加、删除和修改动态信息。
4. **订单管理**：
    - 房东可以查看系统中的订单列表，包括订单ID和订单详情。
    - 房东可以添加、删除和修改订单信息。
5. **日志管理**：
    - 房东可以查看系统中的日志列表，包括日志ID和日志详情。
    - 房东可以添加、删除和修改日志信息。
6. **统计信息**：
    - 房东可以查看统计信息，了解系统的使用情况和运营状况。
7. **管理员管理**：
    - 房东可以管理系统中的管理员，包括查看、添加、删除和修改管理员信息。

#### 三、管理员管理功能
1. **用户管理**：
    - 管理员可以查看系统中的用户列表，包括用户ID和用户名。
    - 管理员可以添加、删除和修改用户信息。
2. **房间管理**：
    - 管理员可以查看系统中的房间列表，包括房间ID和房间名。
    - 管理员可以添加、删除和修改房间信息。
3. **动态管理**：
    - 管理员可以查看系统中的动态列表，包括动态ID和内容。
    - 管理员可以添加、删除和修改动态信息。
4. **订单管理**：
    - 管理员可以查看系统中的订单列表，包括订单ID和订单详情。
    - 管理员可以添加、删除和修改订单信息。
5. **日志管理**：
    - 管理员可以查看系统中的日志列表，包括日志ID和日志详情。
    - 管理员可以添加、删除和修改日志信息。
6. **统计信息**：
    - 管理员可以查看统计信息，了解系统的使用情况和运营状况。

#### 四、普通用户功能
1. **查询房间**：
    - 普通用户可以查看房间列表，包括房间ID和房间名。
    - 普通用户可以预定房间，选择具体房间并进行预定操作。
2. **查看动态**：
    - 普通用户可以查看系统中的动态列表，包括动态ID和内容。
3. **个人资料管理**：
    - 普通用户可以查看和修改个人资料，包括用户名和其他个人信息。

![](https://i-blog.csdnimg.cn/direct/9a82617368a5492bbd6aff00e7889028.png)

4. **查看订单**：
    - 普通用户可以查看自己的订单列表，包括订单ID和订单详情。
5. **收藏管理**：
    - 普通用户可以查看收藏的房间列表，包括房间ID和房间名。

### 系统设计与实现
+ 系统采用Java Swing进行图形界面的设计与实现。
+ 系统采用多窗口设计，每个功能界面使用独立的JFrame实现。
+ 各个界面之间通过按钮和事件监听器进行切换和功能实现。
+ 使用`JTabbedPane`实现房东和管理员界面的多标签页功能，分别管理用户、房间、动态、订单、日志和统计信息。

### 角色和权限
+ **房东**：拥有最高权限，可以管理用户、房间、动态、订单、日志以及管理员。
+ **管理员**：拥有较高权限，可以管理用户、房间、动态、订单、日志和查看统计信息。
+ **普通用户**：拥有基础权限，可以查询房间、查看动态、管理个人资料、查看订单和收藏。

该系统旨在提供一个简洁、高效的民宿管理平台，帮助房东、管理员和普通用户高效地进行民宿运营和管理。

---

# 二、设计思路
### 设计思路
#### 主程序 (Main)
+ **初始化用户列表**：在主程序中定义一个静态的用户列表`users`来存储所有注册的用户。
+ **启动登录界面**：在`main`方法中使用`SwingUtilities.invokeLater`启动登录界面`LoginFrame`，确保界面在事件调度线程中创建。

#### 登录界面 (LoginFrame)
+ **布局与组件**：
    - 设置窗口标题、大小、默认关闭操作和位置。
    - 添加手机号和密码输入框，以及登录和注册按钮。
+ **登录逻辑**：
    - 获取输入的手机号和密码。
    - 遍历用户列表验证身份。
    - 根据用户角色（房东、管理员、普通用户）跳转到相应的界面，并关闭登录窗口。
    - 若验证失败，显示错误信息。
+ **注册逻辑**：
    - 打开注册界面`RegisterFrame`。

#### 注册界面 (RegisterFrame)
+ **布局与组件**：
    - 设置窗口标题、大小、默认关闭操作和位置。
    - 添加手机号、密码、昵称、身份证号输入框，以及身份选择框和注册按钮。
+ **注册逻辑**：
    - 获取用户输入的信息。
    - 检查手机号是否已注册。
    - 若未注册，将新用户添加到用户列表，并显示注册成功信息。
    - 关闭注册窗口。

#### 房东界面 (HostFrame)
+ **布局与组件**：
    - 设置窗口标题、大小、默认关闭操作和位置。
    - 使用`JTabbedPane`创建多个标签页，包括用户管理、房间管理、动态管理、订单管理、日志管理和统计信息。
+ **管理管理员功能**：
    - 添加一个按钮用于管理管理员，点击后打开`ManageAdminsFrame`。
+ **管理员管理界面 (ManageAdminsFrame)**：
    - 显示管理员列表。
    - 提供添加、删除、修改管理员的功能，通过按钮和对话框实现。

#### 管理员界面 (AdminFrame)
+ **布局与组件**：
    - 设置窗口标题、大小、默认关闭操作和位置。
    - 使用`JTabbedPane`创建多个标签页，包括用户管理、房间管理、动态管理、订单管理、日志管理和统计信息。
+ **用户管理功能**：
    - 显示用户列表。
    - 提供添加、删除、修改用户的功能，通过按钮和对话框实现。

#### 普通用户界面 (UserFrame)
+ **布局与组件**：
    - 设置窗口标题、大小、默认关闭操作和位置。
    - 使用`JTabbedPane`创建多个标签页，包括查询房间、查看动态、查看及修改个人资料、查看订单和收藏。
+ **房间预定功能**：
    - 显示房间列表。
    - 提供预定房间的功能，通过按钮和对话框实现。
+ **查看个人资料和修改功能**：
    - 提供查看和修改个人资料的功能，通过按钮和对话框实现。
+ **订单和收藏功能**：
    - 显示用户的订单和收藏列表。



![](https://i-blog.csdnimg.cn/direct/43f06b6734db4ce8bb65d0f877f59d49.png)

### 类定义
**User 类**

+ 存储用户的手机号、密码、昵称、身份证号和角色。

```java
public class User {
    private String phone;
    private String password;
    private String nickname;
    private String idCard;
    private String role;

    public User(String phone, String password, String nickname, String idCard, String role) {
        this.phone = phone;
        this.password = password;
        this.nickname = nickname;
        this.idCard = idCard;
        this.role = role;
    }

    public String getPhone() {
        return phone;
    }

    public String getPassword() {
        return password;
    }

    public String getNickname() {
        return nickname;
    }

    public String getIdCard() {
        return idCard;
    }

    public String getRole() {
        return role;
    }

    public void setNickname(String nickname) {
        this.nickname = nickname;
    }
}

```



**Admin 类**

+ 存储管理员的ID和名称。

```java
public class Admin {
    private String id;
    private String name;

    public Admin(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```



![](https://i-blog.csdnimg.cn/direct/24fc3340401e4c619ed36f705231d7a9.png)



**Room 类**

+ 存储房间的ID和名称。



```java
public class Room {
    private String id;
    private String name;

    public Room(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```









**Activity 类**

+ 存储动态的ID和内容。

```java
public class Activity {
    private String id;
    private String content;

    public Activity(String id, String content) {
        this.id = id;
        this.content = content;
    }

    public String getId() {
        return id;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}

```







![](https://i-blog.csdnimg.cn/direct/505c4b0ca01948e99fd7a77898dc0bbe.png)



**Order 类**

+ 存储订单的ID和详情。



```java
public class Order {
    private String id;
    private String details;

    public Order(String id, String details) {
        this.id = id;
        this.details = details;
    }

    public String getId() {
        return id;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }
}

```











**Log 类**

+ 存储日志的ID和详情。



```java
public class Log {
    private String id;
    private String details;

    public Log(String id, String details) {
        this.id = id;
        this.details = details;
    }

    public String getId() {
        return id;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }
}

```











---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/885d17cb5f98424eb4effcaceba1d49a.png)

![](https://i-blog.csdnimg.cn/direct/7545ab7881ca4b2ebdd3609825542aa0.png)

![](https://i-blog.csdnimg.cn/direct/cfb5a286a34043fbadd91344cf220389.png)

## 用户管理
![](https://i-blog.csdnimg.cn/direct/f9a84877efdb437bb2eafe0a4b9ace0f.png)  
![](https://i-blog.csdnimg.cn/direct/842a571bd55543a6afda966911fdaae9.png)

## 添加管理员
![](https://i-blog.csdnimg.cn/direct/f98b64b553ff43f19ef7d852271a142d.png)

![](https://i-blog.csdnimg.cn/direct/6a605ecf9bd54b5e8f6802f5fd1c8389.png)

## 删除管理员
![](https://i-blog.csdnimg.cn/direct/5043865581f54f25aadc6f702d625754.png)



## 修改管理员
![](https://i-blog.csdnimg.cn/direct/e3cc465dfb914f26b7cfb72650afd2fc.png)  
![](https://i-blog.csdnimg.cn/direct/dd084c6248254565879664d2b6d51468.png)

## 房间管理
![](https://i-blog.csdnimg.cn/direct/927ec7eede904532a67a656e23188d6b.png)  
![](https://i-blog.csdnimg.cn/direct/f051ad42415144289b66f8b37d0c1e8a.png)

## 订单管理
![](https://i-blog.csdnimg.cn/direct/65198f14d64a42bda2e149d188719dec.png)

## 日志管理
![](https://i-blog.csdnimg.cn/direct/1a9d880410654381b46cef23ebaf3606.png)



![](https://i-blog.csdnimg.cn/direct/dd102b1d207b45139ca69e2ada313a50.png)



---

