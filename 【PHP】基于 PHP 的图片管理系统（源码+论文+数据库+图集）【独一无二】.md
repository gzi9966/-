---

> # 【PHP】基于 PHP 的图片管理系统（源码+论文+数据库+图集）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
本系统是一个基于 PHP + MySQL 开发的 **图片管理系统**，主要功能包括 **用户注册/登录、图片上传、图片展示、图片搜索、图片删除**，并使用进行前端美化。系统适用于个人或小型团队管理和存储图片，提升图片管理的便捷性和安全性。

（1）安装配置项目运行所需的环境；  
（2） 采用软件工程的方法对题目要求进行需求分析和功能设计；  
（3）根据系统功能需求设计所需的数据库，数据库设计要符合范式要求；  
（4）项目资源文件要统一规范存放，类名、方法名等规范命名，合理添加注释，体现良好的职业素养；  
（5）项目前端可使用BootStrap等主流前端框架进行页面布局，页面布局整体美观大方，无错位等现象；  
（6）项目后端采用PHP+MySQL进行开发；  
（7）在项目中合理使用GET、POST等数据交互方式；  
（8）在项目中至少使用Session或Cookie中的一种来保存用户的会话状态；  
（9）在项目中围绕数据库的增、删、查、改进行操作，在数据显示、数据搜索、数据分页、数据修改、数据删除五种操作中，至少在项目中体现3种。  
（10）在项目中设计图像或者文件的上传操作，要求上传的文件按照年月自动分目录存放，对上传文件的大小、类型要进行验证，并重命名。图片操作（缩放、水印）和验证码功能二者包含其一。

---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/c02b39699e5d474aa725da992f1713a3.png)



---

#### **2. 系统功能模块**
##### **2.1 用户管理**
+ **用户注册**
    - 允许用户注册账号，提供 **用户名、邮箱、密码** 进行注册。
    - **密码加密存储**（使用 `password_hash()` 进行 Bcrypt 加密）。
    - 注册成功后自动跳转到系统首页。
+ **用户登录**
    - 用户使用 **用户名 + 密码** 进行身份验证。
    - **密码验证** 采用 `password_verify()`，防止明文存储密码。
    - 登录成功后，使用 **Session 记录用户状态**，避免重复登录。
+ **用户退出**
    - 用户点击 **退出按钮**，系统清除 Session，并跳转到登录页面。

---

##### **2.2 图片管理**
+ **图片上传**
    - 允许用户上传图片，并填写 **描述信息** 方便管理。
    - **支持的图片格式**：JPEG、PNG、GIF。
    - **图片大小限制**：最大 2MB。
    - **存储路径**：  
        * 图片按 **年月自动分目录存放**（`uploads/YYYY/MM/`）。
        * 图片文件 **重命名**，防止文件名冲突（使用 `uniqid()` 生成唯一名称）。
    - 图片信息存入数据库（**文件名、路径、描述、上传时间**）。
+ **图片展示**
    - 首页展示用户上传的所有图片（**按时间倒序排序**）。
    - 使用 **Bootstrap 进行卡片布局**，图片以缩略图形式展示。
    - 每张图片下方显示描述信息，并提供 **删除按钮** 进行管理。
+ **图片搜索**
    - 用户可以输入 **关键字** 搜索 **文件名或描述** 相关的图片。
    - 采用 **SQL **`LIKE`** 语句** 实现模糊查询，支持部分匹配。
+ **图片删除**
    - 用户点击 **删除按钮**，可以删除图片。
    - 删除时：
        1. 先检查当前用户是否有权限删除该图片（**防止越权操作**）。
        2. **删除数据库记录**。
        3. **删除本地文件**，确保存储空间不会占用过多资源。

---

![](https://i-blog.csdnimg.cn/direct/8b4d8d5f21784c90a160b14e2bef6513.png)

#### **3. 数据库设计**
本系统主要涉及两张数据库表：

##### **3.1 用户表（**`users`**）**
| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | INT(AUTO_INCREMENT) | 用户ID，主键 |
| username | VARCHAR(50) | 用户名，唯一 |
| email | VARCHAR(100) | 用户邮箱 |
| password | VARCHAR(255) | 用户密码（加密存储） |


##### **3.2 图片表（**`images`**）**
| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | INT(AUTO_INCREMENT) | 图片ID，主键 |
| user_id | INT | 上传该图片的用户ID |
| filename | VARCHAR(255) | 图片文件名 |
| filepath | VARCHAR(255) | 图片存储路径 |
| upload_time | TIMESTAMP | 上传时间 |
| description | TEXT | 图片描述信息 |


---

#### **4. 交互流程**
1. **用户注册**  
    - 填写 **用户名、邮箱、密码** → 提交表单 → 注册成功 → 自动跳转到首页。
2. **用户登录**  
    - 输入 **用户名+密码** → 服务器验证 → 登录成功 → 跳转到首页。
3. **上传图片**  
    - 选择图片 → 输入描述信息 → 提交 → 图片存入服务器 → 展示在首页。
4. **搜索图片**  
    - 在搜索框输入关键字 → 显示匹配的图片。
5. **删除图片**  
    - 用户点击删除 → 服务器检查权限 → 删除数据库记录 → 删除本地图片。
6. **退出登录**  
    - 用户点击退出 → 清除 Session → 跳转回登录页面。

---

#### **5. 主要技术点**
+ **后端**：
    - **PHP + MySQL** 进行数据存储和交互。
    - **Session 进行用户登录状态管理**。
    - **密码加密存储**（`password_hash()`）。
    - **文件上传处理**（`move_uploaded_file()`）。
    - **SQL 查询**（数据插入、查询、删除）。
+ **前端**：
    - **表单验证** 确保输入安全性。
    - **响应式设计**，适配 PC 和移动端。







本系统采用 **PHP + MySQL** 开发，具备 **用户注册、登录、图片上传、搜索、删除** 等核心功能，界面简洁美观，文件管理高效，能够满足基本的图片管理需求，并预留了后续扩展的可能性。  



---

# 三、关键模块代码分析
## **1. 用户管理模块**
### **1.1 用户注册 (**`register.php`**)**
用户注册功能允许新用户创建账户，以便登录并使用系统。首先，系统接收用户输入的用户名、邮箱和密码，并进行格式验证。为了确保账户的唯一性，系统会查询数据库检查用户名或邮箱是否已存在。如果已存在，则提示用户更换信息。若信息有效，系统使用 password_hash() 进行密码加密，增强安全性。随后，将用户信息存入数据库，并提示注册成功。完成注册后，用户可以使用注册的账户进行登录。  
![](https://i-blog.csdnimg.cn/direct/8b4d8d5f21784c90a160b14e2bef6513.png)



![](https://i-blog.csdnimg.cn/direct/8eed100eb9cb4588b8f63f6d27164ce7.png)

#### **代码**
```php
<?php
include 'config.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = trim($_POST['username']);
    $email = trim($_POST['email']);
    $password = trim($_POST['password']);

    // 验证用户名或邮箱是否已存在
    $stmt = $conn->prepare("SELECT id FROM users WHERE username = ? OR email = ?");
    $stmt->bind_param("ss", $username, $email);
    $stmt->execute();
    $stmt->store_result();
    
    if ($stmt->num_rows > 0) {
        echo "用户名或邮箱已被注册";
    } else {
        // 密码加密
        $hashed_password = password_hash($password, PASSWORD_BCRYPT);
        $stmt = $conn->prepare("INSERT INTO users (username, email, password) VALUES (?, ?, ?)");
        $stmt->bind_param("sss", $username, $email, $hashed_password);
        if ($stmt->execute()) {
            echo "注册成功，请<a href='login.php'>登录</a>";
        } else {
            echo "注册失败";
        }
    }
    $stmt->close();
}
?>
```

#### **分析**
+ 通过 `$_POST` 获取用户输入的用户名、邮箱、密码。
+ 采用 **预处理 SQL 语句** (`$stmt->bind_param()`) 防止 **SQL 注入**。
+ 使用 `password_hash()` 进行 **Bcrypt 加密**，保证密码安全。
+ 先查询数据库检查 **用户名或邮箱是否已注册**，避免重复用户。
+ 注册成功后，将用户信息插入数据库，并提示跳转至 **登录页**。

---

### **1.2 用户登录 (**`login.php`**)**
用户登录功能用于验证用户身份并建立会话。用户输入用户名和密码后，系统会查询数据库，查找匹配的用户名，并获取加密存储的密码。随后，系统使用 password_verify() 进行密码校验，确保用户输入的密码与数据库中的哈希密码一致。如果验证成功，系统会使用 Session 存储用户 ID 和用户名，以维护登录状态，并跳转至首页。否则，系统会返回错误信息，提示用户名或密码错误，防止未授权用户访问。

![](https://i-blog.csdnimg.cn/direct/ec0fde4fbf224db09dc6c69fa3ca754e.png)  
![](https://i-blog.csdnimg.cn/direct/fc201ac33138412384e233a0a3f3aca2.png)

#### **代码**
```php
<?php
session_start();
include 'config.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = trim($_POST['username']);
    $password = trim($_POST['password']);

    $stmt = $conn->prepare("SELECT id, password FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $stmt->store_result();
    $stmt->bind_result($user_id, $hashed_password);
    
    if ($stmt->fetch() && password_verify($password, $hashed_password)) {
        $_SESSION['user_id'] = $user_id;
        $_SESSION['username'] = $username;
        header("Location: index.php");
    } else {
        echo "用户名或密码错误";
    }
    $stmt->close();
}
?>
```

#### **分析**
+ **Session 维护用户状态**，成功登录后存储 `user_id` 和 `username`。
+ 使用 `password_verify()` 校验 **加密存储的密码**，避免明文存储密码。
+ 采用 **预处理查询**，防止 SQL 注入。
+ 登录成功后，跳转到 `index.php` 主页。

---

## **2. 图片管理模块**
### **2.1 图片上传 (**`upload.php`**)**
图片上传功能允许用户将本地图片上传至服务器，并添加描述信息。在上传过程中，系统会先验证文件的格式是否为 JPEG、PNG 或 GIF，并检查文件大小是否超过 2MB。如果文件符合要求，系统会按照 当前年月 创建存储目录（如 uploads/2025/01/），确保文件管理有序。随后，系统生成唯一文件名，防止文件名冲突，并将图片存入服务器目录。同时，文件路径、描述信息和上传时间存入数据库，以便后续管理和展示。

![](https://i-blog.csdnimg.cn/direct/61c9c260d64b44bfab2007bea42df32b.png)  
![](https://i-blog.csdnimg.cn/direct/ce777961da4342928a4ceb7dc8abd5b9.png)

#### **代码**
```php
<?php
session_start();
include 'config.php';

if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_FILES["image"])) {
    $user_id = $_SESSION['user_id'];
    $description = trim($_POST['description']);
    $file = $_FILES["image"];
    
    // 验证文件类型和大小
    $allowed_types = ['image/jpeg', 'image/png', 'image/gif'];
    if (!in_array($file["type"], $allowed_types) || $file["size"] > 2 * 1024 * 1024) {
        die("文件类型不支持或大小超限");
    }

    // 按年月存放图片
    $upload_dir = "uploads/" . date("Y") . "/" . date("m") . "/";
    if (!file_exists($upload_dir)) {
        mkdir($upload_dir, 0777, true);
    }

    // 生成唯一文件名
    $file_ext = pathinfo($file["name"], PATHINFO_EXTENSION);
    $filename = uniqid() . "." . $file_ext;
    $filepath = $upload_dir . $filename;

    if (move_uploaded_file($file["tmp_name"], $filepath)) {
        $stmt = $conn->prepare("INSERT INTO images (user_id, filename, filepath, description, upload_time) VALUES (?, ?, ?, ?, NOW())");
        $stmt->bind_param("isss", $user_id, $filename, $filepath, $description);
        $stmt->execute();
        echo "图片上传成功";
    } else {
        echo "图片上传失败";
    }
}
?>
```

#### **分析**
+ 采用 `$_FILES` 获取上传的图片，并 **验证格式（JPEG/PNG/GIF）和大小（最大2MB）**。
+ 图片 **按年月创建存储目录**，提高存储管理效率。
+ **生成唯一文件名**（`uniqid()`），避免文件名冲突。
+ 使用 `move_uploaded_file()` 安全存储图片，并插入数据库记录。

---

### **2.2 图片展示 (**`index.php`**)**
图片展示功能用于在首页显示所有已上传的图片。系统从数据库中获取所有图片的存储路径、描述信息和上传时间，并按照时间倒序排列，确保最新图片优先展示。前端界面采用 Bootstrap 进行 卡片式布局，每张图片配有描述信息，并可点击查看大图。同时，每张图片下方提供 删除按钮，供用户管理自己的图片。为了防止 XSS 攻击，系统会对用户输入的描述信息进行 htmlspecialchars() 处理，确保展示内容安全。

![](https://i-blog.csdnimg.cn/direct/31eeb2214e2b452b9d62d5907e1683e8.png)

#### **代码**
```php
<?php
session_start();
include 'config.php';

$stmt = $conn->prepare("SELECT id, filename, filepath, description FROM images ORDER BY upload_time DESC");
$stmt->execute();
$result = $stmt->get_result();
?>
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-5">
        <h2>图片库</h2>
        <div class="row">
            <?php while ($row = $result->fetch_assoc()) { ?>
                <div class="col-md-4">
                    <div class="card">
                        <img src="<?= $row['filepath'] ?>" class="card-img-top" alt="图片">
                        <div class="card-body">
                            <p><?= htmlspecialchars($row['description']) ?></p>
                            <a href="delete.php?id=<?= $row['id'] ?>" class="btn btn-danger">删除</a>
                        </div>
                    </div>
                </div>
            <?php } ?>
        </div>
    </div>
</body>
</html>

```

#### **分析**
+ 采用 **Bootstrap** 进行卡片式布局，图片按 **时间倒序** 展示。
+ 使用 `htmlspecialchars()` 处理描述信息，防止 **XSS 攻击**。
+ 图片显示后，提供 **删除按钮** 进行管理。

---

![](https://i-blog.csdnimg.cn/direct/8af604744fb348e89ef600f1e0034d19.png)

### **2.3 图片删除 (**`delete.php`**)**
![](https://i-blog.csdnimg.cn/direct/501b38603af54a3186e0e0adddd9061e.png)



图片删除功能允许用户删除自己上传的图片，确保数据可管理。系统会根据用户提交的图片 ID 查询数据库，并检查该图片是否属于当前登录用户。如果用户有权限删除，系统会先使用 unlink() 删除服务器存储的图片文件，随后在数据库中删除对应的记录，确保数据一致性。若用户尝试删除他人图片，系统会返回“无权删除”提示信息，防止越权操作。

#### **代码**
```php
<?php
session_start();
include 'config.php';

if (isset($_GET['id'])) {
    $image_id = intval($_GET['id']);
    $stmt = $conn->prepare("SELECT filepath FROM images WHERE id = ? AND user_id = ?");
    $stmt->bind_param("ii", $image_id, $_SESSION['user_id']);
    $stmt->execute();
    $stmt->bind_result($filepath);
    
    if ($stmt->fetch()) {
        unlink($filepath);
        $delete_stmt = $conn->prepare("DELETE FROM images WHERE id = ?");
        $delete_stmt->bind_param("i", $image_id);
        $delete_stmt->execute();
        echo "图片删除成功";
    } else {
        echo "无权删除";
    }
}
?>
```

#### **分析**
+ 先 **检查用户权限**，确保用户只能删除自己的图片。
+ 使用 `unlink()` **删除本地文件**，然后 **删除数据库记录**，避免垃圾数据。

---

### **2.3 图片搜索 (**`search.php`**)**
![](https://i-blog.csdnimg.cn/direct/ba59fc1864534d25aa058a8b938831cd.png)  
![](https://i-blog.csdnimg.cn/direct/f447e19f364d45599429a088bdb5a79c.png)

