---

> # 【C语言】C语言 食堂自动化管理系统（源码+数据文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
编写一份食堂自动化管理系统，实现学生就餐卡管理和菜品管理以及消费记录统计等相关功能。所有数据包括学生就餐卡信息，菜品信息等都要利用文件系统保存，以备系统下次运行时使用。通过此课题，熟练掌握文件、数组、指针的各种操作，以及一些基本算法的应用。

（1）学生就餐卡和菜品数据是由多条数据记录构成，其信息包括：学生就餐卡：卡号，姓名，余额，状态（正常和挂失。）食堂菜品数据：编号 名称 金额 消费次数   
（2）数据保存形式：所有就餐卡信息，菜品信息数据要以文本或二进制文件保存。  
（3）需要实现的功能代码要能提供以下两个基本模块。

管理员模块：管理员以密码登录系统，可以完成以下功能  
A． 注册学生就餐卡  
B．就餐卡的充值挂失模块  
C．食堂菜单管理功能，包括对菜单的增删修改查等功能。  
D．统计功能：统计菜品销售情况（销售排行），以及学生卡的使用情况（选做）  
学生模块：以就餐卡号登录系统（说明：卡处于挂失，则拒绝登录。）  
可以查询卡中余额。  
可以查询本卡的消费记录（选做）  
可以浏览菜品并按照金额购买

（4）界面功能要求

1. 采用友好的字符界面，实现一个功能控制菜单。
2. 每次操作都从该菜单选择，利用循环结构使得一次运行程序可进行多次操作。  
选做要求：

（1）数据完整性保证。例如：就餐卡号和菜单编号不得重复。  
（2）学生每次消费可以自动生成消费记录（卡号，购买时间，购买菜品编号，购买金额）。  
该消费记录表永久保存，用于事后查询和统计。具体请学生自己构思、设计。



![](https://i-blog.csdnimg.cn/direct/444146c1e15d49589c43bd12b0b643f5.png)



---

# 二、设计思路
## 1. 系统概述
本系统为食堂自动化管理系统，主要涉及两个角色：  

+ **管理员模块**：负责管理学生就餐卡、食堂菜品（增、删、改、查）以及统计菜品销售情况。  
+ **学生模块**：学生通过就餐卡登录后可以查询余额、浏览菜品、进行菜品购买以及查询自己的消费记录。

所有数据（学生就餐卡、菜品、消费记录）均存储于文本文件中，以保证程序退出后数据的持久性。



## 2. 数据结构设计
系统采用结构体来表示学生、菜品和消费记录，定义了三个主要数据结构：

+ **学生结构体 Student**  
存放就餐卡号、学生姓名、卡内余额以及卡片状态（正常/挂失）。

```c
typedef struct {
    char cardNumber[20];  // 就餐卡号（唯一标识）
    char name[50];        // 学生姓名
    double balance;       // 卡内余额
    int status;           // 状态：0 正常，1 挂失
} Student;
```

+ **菜品结构体 Dish**  
保存菜品编号、名称、价格和销售次数。

```c
typedef struct {
    char id[20];          // 菜品编号（唯一标识）
    char name[50];        // 菜品名称
    double price;         // 菜品价格
    int sales;            // 销售次数
} Dish;
```

+ **消费记录结构体 Record**  
用于记录每次消费的就餐卡号、购买菜品编号、消费金额及时间记录。

```c
typedef struct {
    char cardNumber[20];  // 消费卡号
    char dishId[20];      // 菜品编号
    double price;         // 消费金额
    char timeStr[30];     // 购买时间，格式：YYYY-MM-DD_HH:MM:SS
} Record;
```

同时全局定义数组存储这些数据，并用计数变量记录当前数量，这样在内存中可以进行查找和操作。



![](https://i-blog.csdnimg.cn/direct/83f59d14d0144e4fac573377b7934c3a.png)

## 3. 文件操作模块
为了保证数据持久化，每种数据都对应一个文本文件，分别为：

+ 学生就餐卡 —— `student_cards.txt`
+ 菜品信息   —— `dishes.txt`
+ 消费记录   —— `records.txt`

加载函数（例如 `loadStudents()`、`loadDishes()`、`loadRecords()`）在程序启动时读取文件内容到全局数组；保存函数（例如 `saveStudents()`、`saveDishes()`、`saveRecords()`）在数据发生修改后写回文件。  
例如，加载学生数据的代码块如下：

```c
void loadStudents(void)
{
    FILE *fp = fopen(STUDENT_FILE, "r");
    if (fp == NULL)
    {
        studentCount = 0;
        return;
    }
    studentCount = 0;
    while (fscanf(fp, "%s %s %lf %d", 
                  students[studentCount].cardNumber,
                  students[studentCount].name,
                  &students[studentCount].balance,
                  &students[studentCount].status) == 4)
    {
        studentCount++;
        if (studentCount >= MAX_STUDENTS)
            break;
    }
    fclose(fp);
}
```

---

## 4. 辅助函数设计
为了提高代码的模块化和可复用性，还设计了一些辅助函数，例如：

+ `clearInputBuffer()`：用于清除输入缓冲区，避免由于换行符等残留字符导致输入错误。
+ `getCurrentTimeString()`：获取当前系统时间，并将时间格式化为`YYYY-MM-DD_HH:MM:SS`字符串，便于记录消费时间。



## 5. 管理员模块设计
管理员模块需要输入密码验证后才能进入，常用的功能有：

1. **注册学生就餐卡**  
检查卡号是否重复，若不重复则新建卡片并初始化余额、状态（正常）。

```c
void registerStudent(void)
{
     //
    // 代码太多了，只讲讲思路吧！！！
    //
    // 输入卡号、姓名和初始余额
    // 调用 findStudentIndexByCard() 检查卡号唯一性...
    // 将数据存入 students 数组，并保存到文件
}
```

2. **就餐卡充值/挂失**  
根据操作类型，能够对指定卡号进行充值或挂失，充值时增加余额，挂失时设置状态为挂失。

```c
void rechargeOrReport(void)
{
    //
    // 代码太多了，只讲讲思路吧！！！
    //
    
    // 根据用户选择判断充值或挂失操作
    // 对输入的卡号进行查找，若存在则进行相应处理，再保存数据
}
```

3. **菜品管理**  
包括菜品增加、删除、修改和查询功能：

```c
void addDish(void)
{
    // 检查菜品编号，输入名称、价格后存储菜品信息
}
```

    - **增加菜品**：要求菜品编号不重复，销售次数初始化为0。
    - **删除菜品**：根据菜品编号查找并删除，采用“用最后一项覆盖删除项”的方法。
    - **修改菜品**：修改指定菜品的名称和价格。
    - **查询菜品**：遍历并打印当前所有菜品信息。
4. **统计功能**  
统计菜品销售情况，并按照销售次数从高到低排序后打印销售排行榜。

```c
void statistics(void)
{
    //
    // 代码太多了，只讲讲思路吧！！！
    //
    
    // 拷贝菜品数据到临时数组，使用冒泡排序算法对销售次数降序排序
    // 打印排行数据
}
```

管理员模块的主界面函数 `adminMenu()` 集成了以上所有操作，用户在验证管理员密码后可选择对应的功能。



![](https://i-blog.csdnimg.cn/direct/5c92f72cfd5d410ba744e297cc5d6356.png)

## 6. 学生模块设计
学生模块主要通过就餐卡号登录，允许的操作包括：

1. **查询余额**  
每个学生可以查询自己卡中余额，直接读取全局数组中对应学生的数据即可。

```c
void studentQueryBalance(int studentIndex)
{
    //
    // 代码太多了，只讲讲思路吧！！！
    //
    
    printf("当前余额为：%.2lf\n", students[studentIndex].balance);
}
```

2. **浏览菜品**  
遍历全局菜品数组，将菜品编号、名称、价格和销售次数显示，方便学生选择。

```c
void studentBrowseDishes(void)
{
    //
    // 代码太多了，只讲讲思路吧！！！
    //
    // 打印所有菜品详细信息
}
```

3. **购买菜品**  
根据学生输入的菜品编号，检查菜品是否存在及余额是否足够，若满足条件则：

```c
void studentPurchase(int studentIndex)
{
    // 查询菜品，检查余额，扣费，增加销售次数并记录消费时间
}
```

    - 扣除学生余额
    - 增加对应菜品的销售次数
    - 生成消费记录（记录时间，通过 `getCurrentTimeString()` 获取当前时间）
    - 保存相关数据到文本文件
4. **查询消费记录**  
通过卡号遍历消费记录数组，筛选出属于当前就餐卡的记录并打印出来。

```c
void studentQueryRecords(const char *cardNumber)
{
    //
    // 代码太多了，只讲讲思路吧！！！
    //
    // 遍历 records 数组，匹配卡号后打印消费记录
}
```

学生模块的主界面函数 `studentMenu()` 封装了上述操作，使得学生在登录后可以方便地通过菜单选项进行操作。



## 7. 主函数流程
主函数 `main()` 作为程序入口主要完成以下工作：

1. **数据加载**  
调用 `loadStudents()`、`loadDishes()`、`loadRecords()` 从文本文件加载数据到内存。
2. **主菜单显示**  
用户可选择进入管理员模块或学生模块，或退出系统：
    - 当选择学生模块时，根据输入的就餐卡号查找对应学生，并判断卡是否处于挂失状态。
    - 根据用户操作进入相应的子模块。
3. **保存数据**  
在程序退出前，再次调用保存函数确保所有数据更新写入文件。

```c
int main(void)
{
    // 加载数据
    loadStudents();
    loadDishes();
    loadRecords();
    
    // 主菜单循环
    do {
        // 显示菜单、获取用户选择
        // 根据选择调用 adminMenu() 或 studentMenu()
    } while(choice != 0);
    
    // 退出前保存各数据文件
    saveStudents();
    saveDishes();
    saveRecords();
    
    return 0;
}
```

![](https://i-blog.csdnimg.cn/direct/c9a7809f9a7a4f79b34ad89aa7b80f27.png)



## 8. 总结
该系统通过**模块化设计**将不同功能进行分离，采用结构体和全局数组存储实体数据，并利用文本文件实现数据的持久化，使得数据在程序退出后仍能保留。管理员模块和学生模块的划分清晰，通过辅助函数（如输入缓冲区清理、当前时间获取）提高了代码的健壮性。同时，代码中通过查找函数（如 `findStudentIndexByCard()` 和 `findDishIndexById()`）实现了数据项的快速定位，从而支持各项操作的快速响应。



---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/a522d0377f1547b085452a12ea3fa556.png)

![](https://i-blog.csdnimg.cn/direct/bfedba472e714d66bba4c4e4b87375aa.png)

![](https://i-blog.csdnimg.cn/direct/fd5c2f494ef34d5d96aac38c27d2513e.png)  
![](https://i-blog.csdnimg.cn/direct/8d6b90b7c85c41a5b1095b71836f41d7.png)  
![](https://i-blog.csdnimg.cn/direct/f04025dfdc9349129cfa9c75aea65c3b.png)  
![](https://i-blog.csdnimg.cn/direct/98ffbd478f6d4a9989504310c1995972.png)

![](https://i-blog.csdnimg.cn/direct/e83d1712307d431a8b40bfafbcf0c8dd.png)  
![](https://i-blog.csdnimg.cn/direct/c47969058778441fa7158ee09f66c310.png)  
![](https://i-blog.csdnimg.cn/direct/67bd99eee043419db6410f2ba0d91816.png)  
![](https://i-blog.csdnimg.cn/direct/78a5da98c25e4c9d9e5e6369c4d73709.png)



![](https://i-blog.csdnimg.cn/direct/1c54fe91822f4c3e86cc6f230bc613fb.png)



---

