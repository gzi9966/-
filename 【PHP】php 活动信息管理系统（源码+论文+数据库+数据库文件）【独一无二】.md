---

> # 【PHP】php 活动信息管理系统（源码+论文+数据库+数据库文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
HELP·Events是一家活动管理公司，专门组织各种活动，包括在其礼堂举办的音乐会、会议和研讨会。随着公司规模的扩大，人工流程显然不足以应对日益复杂和规模不断扩大的业务。为了提高效率并为活动组织者和与会者提供无缝体验，HELP·Events决定开发一套全面的活动管理系统(EMS)。“

整个系统的设计分为多个模块，每个模块各自完成特定的功能，系统基于角色的权限管理，管理员、活动组织者和观众分别在各自的仪表盘内完成仅授权范围内的任务。

1. **用户管理模块**  
    - 涉及登录、注册、登出功能。
    - 根据用户角色（管理员、活动组织者、观众）跳转到不同仪表盘。
2. **管理员功能模块**  
    - 查看所有活动列表。
    - 管理系统中的活动数据。
3. **活动组织者功能模块**  
    - 创建活动并初始化座位。
    - 查看自己创建的活动详情。
4. **观众功能模块**  
    - 查看所有活动，预定门票或加入候补名单。
    - 查看自己已购买的门票，支持活动签到。
5. **数据库设计**  
    - 涉及 `users` 表、`events` 表、`seats` 表、`tickets` 表和 `waitlist` 表，合理配置外键约束。



![](https://i-blog.csdnimg.cn/direct/3381739db6dc4135aa8345d45f5b0dc6.png)



---

# 二、设计思路
### **1. 登录模块 (**`login.php`**)**
+ **功能**: 验证用户身份并根据角色跳转到对应的仪表盘。
+ **设计**:
    - 通过用户表查询验证邮箱和密码。
    - 根据角色跳转到管理员、活动组织者或观众的仪表盘。
+ **代码:**

```php
<?php
require 'db.php';

session_start();

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $email = $_POST['email'];
    $password = $_POST['password'];

    $stmt = $conn->prepare("SELECT * FROM users WHERE email = ?");
    $stmt->execute([$email]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if ($user && password_verify($password, $user['password'])) {
        $_SESSION['user_id'] = $user['id'];
        $_SESSION['role'] = $user['role'];

        if ($user['role'] === 'admin') {
            header("Location: admin_dashboard.php");
        } elseif ($user['role'] === 'organizer') {
            header("Location: organizer_dashboard.php");
        } elseif ($user['role'] === 'attendee') {
            header("Location: attendee_dashboard.php");
        }
        exit;
    } else {
        $error = "登录失败，邮箱或密码错误。";
    }
}
?>
```

---

### **2. 注册模块 (**`register.php`**)**
+ **功能**: 提供给新用户注册账户，防止重复邮箱注册。
+ **代码说明**:
    - 验证邮箱是否已经注册过，若未注册则插入新用户。
    - 实现简单的表单验证和用户分角色创建。
+ **代码:**

```php
<?php
require 'db.php';

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $username = $_POST['username'];
    $email = $_POST['email'];
    $password = password_hash($_POST['password'], PASSWORD_BCRYPT);
    $role = $_POST['role'];

    // 检查邮箱是否已被注册
    $stmt = $conn->prepare("SELECT * FROM users WHERE email = ?");
    $stmt->execute([$email]);
    if ($stmt->rowCount() > 0) {
        $error = "该邮箱已经注册，请直接登录。";
    } else {
        // 插入新用户
        $stmt = $conn->prepare("INSERT INTO users (username, email, password, role) VALUES (?, ?, ?, ?)");
        $stmt->execute([$username, $email, $password, $role]);
        header("Location: login.php");
        exit;
    }
}
?>
```

![](https://i-blog.csdnimg.cn/direct/ca86d88891534a5ba36ef3ee399a88a9.png)

---

### **3. 管理员仪表盘 (**`admin_dashboard.php`**)**
+ **功能**: 提供给管理员查看所有活动的功能。
+ **代码说明**:
    - 查询 `events` 表中所有活动并显示。
+ **代码:**

```php
<?php
require 'db.php';
session_start();

if ($_SESSION['role'] !== 'admin') {
    header("Location: login.php");
    exit;
}

$stmt = $conn->prepare("SELECT * FROM events");
$stmt->execute();
$events = $stmt->fetchAll(PDO::FETCH_ASSOC);

?>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>管理员仪表盘</title>
</head>
<body>
    <h2>欢迎, 管理员!</h2>
    <table>
        <thead>
        <tr>
            <th>ID</th>
            <th>标题</th>
            <th>日期</th>
            <th>组织者</th>
        </tr>
        </thead>
        <tbody>
        <?php foreach ($events as $event): ?>
            <tr>
                <td><?= htmlspecialchars($event['id']) ?></td>
                <td><?= htmlspecialchars($event['title']) ?></td>
                <td><?= htmlspecialchars($event['date']) ?></td>
                <td><?= htmlspecialchars($event['organizer_id']) ?></td>
            </tr>
        <?php endforeach; ?>
        </tbody>
    </table>
</body>
</html>

```

---

![](https://i-blog.csdnimg.cn/direct/f635b33960e04a5da8e4f6445a7898f0.png)

### **4. 活动组织者仪表盘 (**`organizer_dashboard.php`**)**
+ **功能**: 
    - 显示活动组织者创建的所有活动。
    - 提供“创建新活动”的入口。

```php
<?php
require 'db.php';
session_start();

if ($_SESSION['role'] !== 'organizer') {
    header("Location: login.php");
    exit;
}

$stmt = $conn->prepare("SELECT * FROM events WHERE organizer_id = ?");
$stmt->execute([$_SESSION['user_id']]);
$my_events = $stmt->fetchAll(PDO::FETCH_ASSOC);

?>
<h2>我创建的活动</h2>
<a href="create_event.php">创建新活动</a>
<table>
    <?php foreach ($my_events as $event): ?>
        <tr>
            <td><?= $event['title'] ?></td>
        </tr>
    <?php endforeach; ?>
</table>

```

---

### **5. 观众仪表盘 (**`attendee_dashboard.php`**)**
+ **功能**: 查看活动信息，预定门票、查看自己的门票及二维码签到。

```php
<h3>所有活动</h3>
<?php foreach ($events as $event): ?>
    <tr>
        <td><?= htmlspecialchars($event['title']) ?></td>
        <td><a href="book_ticket.php?event_id=<?= $event['id'] ?>">预定门票</a></td>
    </tr>
<?php endforeach; ?>
```

---

### **数据库模块**
数据库分为以下表格：

1. `users`: 用户信息，包括管理员、活动组织者和观众。
2. `events`: 每个活动信息，包括主办方。
3. `seats`: 活动座位信息。
4. `tickets`: 门票详情，关联活动和座位。
5. `waitlist`: 活动候补名单。

![](https://i-blog.csdnimg.cn/direct/0d88e2a1ffc145dbae64dce5193f64a8.png)



---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/04e1f7185903479c8e33d0f1600aa7d0.png)



![](https://i-blog.csdnimg.cn/direct/2c991a2c041c41ce9a38fae5db8d3845.png)



![](https://i-blog.csdnimg.cn/direct/0c00d6f1184a45aba216b753242aa107.png)



![](https://i-blog.csdnimg.cn/direct/baba78860c8a4190a01cf4d372440286.png)  
![](https://i-blog.csdnimg.cn/direct/edcd92b6cd5240e99a026e26658a6c41.png)  
![](https://i-blog.csdnimg.cn/direct/5d0a35bdc8de44f6bc2fe4160b50ff50.png)  
![](https://i-blog.csdnimg.cn/direct/bf797076319b44de97bd7419f762d33b.png)  
![](https://i-blog.csdnimg.cn/direct/0858e77f087c4c439e3ef74145f01905.png)  
![](https://i-blog.csdnimg.cn/direct/00b0811d81894ef0a85562ad2031dd16.png)  
![](https://i-blog.csdnimg.cn/direct/e2ef20591f8d447197143a2ae2a5b86e.png)  
![](https://i-blog.csdnimg.cn/direct/c7de6b4ce2254eca98b87962a10732c7.png)

