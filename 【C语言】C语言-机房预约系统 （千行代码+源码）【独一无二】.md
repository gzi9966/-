---

> # C语言-机房预约系统 （千行代码+源码）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
12机房预约系统

管理员

机房信息：录入、修改机房的相关信息，如机器数目、是否空闲等  
机器信息：录入、维护机器信息，如机器类型、软硬件、是否可用等  
信息查询：空闲机器查询、预约情况查询等

学生  
查询：机房、机器信息查询  
预约：机器的预约、取消等  
学生信息修改：个人信息维护



您可以在编写过程中注意以下几点吗  
1.让控制台美观一些并且控制台能随着窗口的大小改变发生比例的改变  
2.输入密码时，密码用*显示  
3.分模块编写  
4.添加注释



> 管理员账号：   
用户名：admin 密码：admin123 
>
> 学生账号：  
用户名：student 密码：stud123
>

---



---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/eecdedfa5d4046d389cd873d3893b176.png)







## 1. 整体架构与模块化设计
该程序采用了**模块化**、**分层设计**的思想，主要将不同的功能抽取到独立的模块中，每个模块只关注自己的职责，具体如下：

+ **数据模块（data.c & data.h）**  
负责初始化和存储全局数据，包括用户、机房和机器的信息。例如：

```c
User g_users[MAX_USERS];
int g_userCount = 0;

Room g_rooms[MAX_ROOMS];
int g_roomCount = 0;

Machine g_machines[MAX_MACHINES];
int g_machineCount = 0;
```

通过 `initData()` 函数初始化默认数据，简化了硬编码数据的管理，也便于以后扩展。

+ **用户界面模块（ui.h 及其实现文件）**  
定义并实现了各种交互函数，如清屏、打印菜单、标题以及各种提示信息（成功、错误等）。例如：

```c
void clearScreen();
void printTitle();
void printError(const char *msg);
void printSuccess(const char *msg);
```

界面模块将所有输出的逻辑统一管理，使得修改界面风格非常方便。

+ **学生功能模块（student.c & student.h）**  
实现了学生用户的具体操作：

代码中每个功能单独封装为一个静态函数，避免命名冲突，并通过学生菜单 `studentMenu()` 进行调度。

    - 查询机房和机器信息；
    - 预定机器与取消预定；
    - 修改个人信息。
+ **管理员与登录模块（admin.h/admin.c & login.c/login.h）**  
虽然管理员模块的实现代码未全部展示，但从入口处可以看出管理员拥有专属的菜单和功能。`login.c` 处理用户登录逻辑，同时实现了密码输入的隐藏功能，提升了安全性。





## 2. 业务流程与功能逻辑
系统整体采用菜单驱动方式设计，用户通过主菜单选择登录身份，然后进入对应的子菜单进行操作。主要流程如下：

1. **程序入口（main.c）**  
    - 初始化数据（调用 `initData()`）。
    - 调整控制台窗口大小（`setConsoleSizeRatio(0.5)`）。
    - 显示主菜单，用户选择操作（0退出、1学生登录、2管理员登录）。
2. **用户登录**  
    - 通过 `login()` 函数验证用户身份。
    - 根据用户类型，调用 `studentMenu()` 或 `adminMenu()` 进入对应子系统。
    - 登录失败或身份不匹配，则通过 `printError()` 提示错误信息。
3. **学生操作流程**  
进入 `studentMenu()` 后，学生可以进行：
    - **查询机房、机器状态**（`queryRoomMachine()`）：遍历全局数据，实时显示各机房、各机器的状态。
    - **预定机器**（`reserveMachine()`）：用户输入机器ID后，检查是否可以预约，将机器状态置为不可用，并更新所属机房的使用数量及状态标识。
    - **取消预约**（`cancelReservation()`）：与预定逻辑相反，将机器状态恢复为可用，并同步更新机房状态。
    - **修改个人信息**（`modifyPersonalInfo()`）：通过 `fgets()` 获取新信息，并处理尾部换行字符，确保信息整洁。



![](https://i-blog.csdnimg.cn/direct/42bf38e5b390485ba14d3b096a28b17d.png)

```c
for(int i = 0; i < g_machineCount; i++){
    if(g_machines[i].machineId == mid && g_machines[i].isAvailable == 1){
        g_machines[i].isAvailable = 0;
        // 更新所属机房使用数，检查是否已满
        for(int r = 0; r < g_roomCount; r++){
            if(g_rooms[r].roomId == g_machines[i].roomId){
                g_rooms[r].usedMachines++;
                if(g_rooms[r].usedMachines == g_rooms[r].totalMachines)
                    g_rooms[r].isFree = 0;
                break;
            }
        }
        printSuccess("预定成功！");
        pressAnyKeyToContinue();
        return;
    }
}
```

4. **管理员操作流程**  
管理员登录后进入 `adminMenu()`，虽然代码未完全展示，但整体逻辑与学生模块类似，通过菜单选择具体管理操作，体现了系统灵活的身份分级设计。

---

## 3. 数据同步与状态管理
在预定和取消预约操作中，不仅需要更新单台机器的状态，还要同步机房整体的状态。比如：

+ 当一台机器被预定后，其所属机房的 `usedMachines` 增加，同时判断 `usedMachines` 与 `totalMachines` 的关系以更新 `isFree` 状态。
+ 取消预约操作时，确保相应的数据恢复原状。

设计使得数据状态保持一致，模拟了真实环境下机房资源的动态变化，体现出程序对多层数据状态的管理能力。



## 4. 安全性与用户体验的优化
### 4.1 密码输入隐藏功能
在 `login.c` 中实现的 `inputPassword()` 函数考虑到了密码输入的安全问题：

+ **跨平台兼容**：使用 `#ifdef _WIN32` 分支分别处理 Windows 和 Linux 下的密码隐藏策略。
+ **字符替换与删除操作**：在输入过程中，用“*”替代实际字符，同时支持退格键删除操作，提升用户体验。

代码片段：

```c
while((ch = getch()) != '\r' && ch != '\n' && ch != EOF && i < maxLen - 1){
    if(ch == 8 || ch == 127){ // 处理退格
        if(i > 0) {
            i--;
            printf("\b \b");
        }
    } else {
        pwd[i++] = (char)ch;
        printf("*");
    }
}
```

### 4.2 界面清晰与反馈及时
+ **清屏与菜单刷新**：在每个操作前调用 `clearScreen()` 确保界面干净，提升可读性。
+ **统一提示反馈**：如 `printSuccess()` 和 `printError()` 分别用于成功和错误提示，使用户能一目了然地了解操作结果。
+ **控制台大小调整**：调用 `setConsoleSizeRatio(0.5)` 增加了程序显示在不同设备上的兼容性。



![](https://i-blog.csdnimg.cn/direct/6502722945d74de58d50c1e48057bc1f.png)





+ **模块化分层设计**  
各个功能模块相互独立、职责明确，既降低了系统复杂度，也为后续维护和扩展提供了便利。
+ **完善的数据同步机制**  
在预定和取消预定操作中，对机器和机房状态的同步更新，确保了数据的一致性和业务逻辑的正确性。
+ **友好的用户交互体验**  
清晰的菜单设计、直观的界面输出以及及时的操作反馈，让用户操作更简单高效。
+ **安全性考虑**  
密码输入的隐藏处理以及跨平台支持，使程序在安全性和兼容性上均表现出色。









---

# 三、可视化分析
主页：  
![](https://i-blog.csdnimg.cn/direct/04123ffa98e44852b184f26927e99be1.png)







学生端：  
![](https://i-blog.csdnimg.cn/direct/97f16fa559d94ff39734e6d36c3e84d8.png)  
![](https://i-blog.csdnimg.cn/direct/ffcbbb459fec4834988c320d70549561.png)  
![](https://i-blog.csdnimg.cn/direct/488e996d34134b09b0806cd07e931f5a.png)

![](https://i-blog.csdnimg.cn/direct/336790a5cbde4e349e95a73b818d2ae2.png)

![](https://i-blog.csdnimg.cn/direct/c347c614f43c4958be993af8070211b5.png)

![](https://i-blog.csdnimg.cn/direct/e0b7a49c29c8482eb948470fa5889aac.png)



![](https://i-blog.csdnimg.cn/direct/631b2d20469d40cfbc6a7c9ac6f9ab66.png)



3）管理员  
![](https://i-blog.csdnimg.cn/direct/16aa7aeb0f2e4cd2bc666aefa7d44898.png)  
![](https://i-blog.csdnimg.cn/direct/74b0696130524964bad77139b4bae70a.png)



![](https://i-blog.csdnimg.cn/direct/25393f623a9a4aeda06d44a4cc6da68d.png)

![](https://i-blog.csdnimg.cn/direct/71f94a5de121485b8808f693146671ba.png)  
![](https://i-blog.csdnimg.cn/direct/a35e8dcbbc8f4c2b9d9f908b6f588894.png)

![](https://i-blog.csdnimg.cn/direct/b5746ca2d40d4aacaf599cd57cfeb74e.png)



![](https://i-blog.csdnimg.cn/direct/412bc51e7d1046d48d0a7faed03f6772.png)



---![](https://i-blog.csdnimg.cn/direct/31887a37e1e844968c660648bbdcd197.png)

