---

> # 模拟物联网智能家居控制系统（源码）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
题目描述：开发一个模拟的物联网智能家居控制系统，该系统能够控制家中的各种智能设备（如灯光、窗帘、空调等）。使用C语言编写程序，模拟设备间的通信和控制逻辑。系统应支持通过命令行界面或简单的图形用户界面（GUI，可使用ASCII字符实现）进行设备控制，并允许用户设置定时任务和场景模式（如离家模式、回家模式）。  
技术要求：掌握C语言中的函数、循环、条件语句等基本语法，理解多线程或事件驱动编程的基本概念（尽管在初学者水平可能不实现多线程，但可以通过轮询模拟），以及如何使用结构体和数组管理设备信息。



![](https://i-blog.csdnimg.cn/direct/4cf828e1b36148d9a67f8c79338159bc.png)

---

# 二、设计思路
## 1. 总体设计思路
本系统模拟了一个简单的智能家居控制系统，使用 C 语言实现，并通过命令行菜单与用户交互。整个设计主要分为以下几个部分：

+ **设备管理**：包含设备的添加、移除、状态查看以及单个设备控制（开/关）。
+ **定时任务管理**：支持为设备设置定时任务，在设定的时间自动执行开或关操作。
+ **场景模式**：提供预定义的场景（如离家模式、回家模式）和自定义场景，让用户可以批量控制多个设备。
+ **调试与帮助**：调试模式用于显示当前系统中所有设备和定时任务的信息，帮助用户了解系统的内部状态；同时也提供帮助信息和日志记录功能，便于追踪操作。



## 2. 数据结构设计
系统中定义了两种主要的数据结构：

### 2.1 设备结构体 `Device`
+ **属性：**
    - `name`：设备名称（字符串）。
    - `status`：设备状态（0 代表关闭，1 代表开启）。

```c
typedef struct {
    char name[20];
    int status; // 0 = 关闭, 1 = 开启
} Device;
```

### 2.2 定时任务结构体 `TimerTask`
+ **属性：**
    - `deviceIndex`：待操作设备在数组中的索引。
    - `action`：任务执行的操作（0 表示关闭，1 表示开启）。
    - `triggerTime`：任务触发的具体时间（使用 time_t 存储当前时间加上用户输入的延时秒数）。

```c
typedef struct {
    int deviceIndex;
    int action; // 0 = 关闭, 1 = 开启
    time_t triggerTime;
} TimerTask;
```

同时，系统定义了全局数组 `devices[]` 和 `tasks[]` 分别存储设备和定时任务，以及相应的计数变量 `deviceCount` 和 `taskCount` 控制数量。



## 3. 功能模块实现
### 3.1 设备管理模块
+ `initializeDevices()`  
初始化设备列表，默认添加三个设备（如“灯光”、“窗帘”、“空调”），并设置初始状态为关闭。
+ `listDevices()`  
遍历设备数组并打印每个设备的名称及当前状态，同时显示设备总数。
+ `addDevice()`  
检查设备数量是否达到上限，如果未达到则接受用户输入设备名称，将新设备添加到数组中，并初始化为关闭状态。
+ `removeDevice()`  
用户选择要删除的设备，通过将后面的设备依次向前移动覆盖删除位置，减少设备数量。
+ `controlDevice()`  
允许用户选择一个设备，并选择执行“开启”或“关闭”操作，对应修改设备的状态值。

### 3.2 定时任务管理模块
+ `addTimerTask()`  
实现添加定时任务的功能：
    - 首先让用户选择目标设备。
    - 然后指定操作（开启或关闭）。
    - 最后用户输入一个延迟时间（秒），程序将当前时间与延迟时间相加，得到任务触发时间，并存入任务数组中。
+ `executeTasks()`  
定时任务的执行：
    - 获取当前系统时间，与每个任务的 `triggerTime` 进行比较。
    - 若任务到达执行时间，则执行对应设备的状态修改，并打印出任务执行信息。
    - 执行完成后使用数组移除策略将任务删除（通过移动后续任务填补空间，并调整任务计数和索引）。

### 3.3 场景模式模块
+ `runSceneMode()`  
提供三种场景模式：
    - 离家模式：将所有设备全部关闭。
    - 回家模式：将所有设备全部开启。
    - 自定义场景：允许用户依次选择多个设备分别进行开或关操作，直到输入“0”结束。

### 3.4 调试及帮助模块
+ `debugMode()`  
显示当前所有设备状态与定时任务的信息（包括剩余时间），便于调试和了解当前系统内部状态。
+ `printHelp()`  
输出系统的操作说明以及各个功能模块的具体使用方法，方便新用户查看帮助。  
![](https://i-blog.csdnimg.cn/direct/6de1a58989f74e30831414e84a65c5e4.png)

### 3.5 日志记录模块
+ `logOperation()`  
每当用户进行一次操作后，系统会将操作的描述写入日志文件 `operations.log` 中，同时记录当前的时间戳。这个功能有助于在系统运行过程中追踪用户的操作记录。

```c
void logOperation(const char *operation) {
    FILE *logFile = fopen("operations.log", "a");
    if (logFile == NULL) {
        printf("无法打开日志文件！\n");
        return;
    }
    time_t now = time(NULL);
    fprintf(logFile, "[%s] %s\n", ctime(&now), operation);
    fclose(logFile);
}
```

---

## 4. 主程序流程
在 `main()` 函数中，整个系统采用无限循环的方式显示主菜单，允许用户选择不同的操作。根据用户的输入，系统调用相应的函数实现对应功能，并在每次操作后调用 `logOperation()` 记录日志。用户选择“退出”选项后，主循环结束、程序退出。

```c
int main() {
    initializeDevices();
    while (1) {
        printf("\n智能家居控制系统\n");
        printf("1. 查看设备列表\n");
        printf("2. 添加新设备\n");
        printf("3. 移除设备\n");
        printf("4. 控制设备\n");
        printf("5. 设置定时任务\n");
        printf("6. 执行定时任务\n");
        printf("7. 场景模式\n");
        printf("8. 调试模式\n");
        printf("9. 帮助\n");
        printf("10. 退出\n");
        printf("请输入你的选择: ");

        int choice;
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                listDevices();
                logOperation("查看设备列表");
                break;
            case 2:
                addDevice();
                logOperation("添加新设备");
                break;
            case 3:
                removeDevice();
                logOperation("移除设备");
                break;
            case 4:
                controlDevice();
                logOperation("控制设备");
                break;
            case 5:
                addTimerTask();
                logOperation("设置定时任务");
                break;
            case 6:
                executeTasks();
                logOperation("执行定时任务");
                break;
            case 7:
                runSceneMode();
                logOperation("运行场景模式");
                break;
            case 8:
                debugMode();
                logOperation("进入调试模式");
                break;
            case 9:
                printHelp();
                logOperation("查看帮助信息");
                break;
            case 10:
                printf("退出系统，再见！\n");
                logOperation("退出系统");
                return 0;
            default:
                printf("无效的选择！\n");
                logOperation("无效选择");
        }
    }
    return 0;
}
```

---

本系统通过模块化设计，将智能家居的各项功能（设备管理、定时任务、场景模式、调试模式等）分离，各自负责不同的功能点，并通过一个统一的菜单进行交互。日志记录功能使得每次操作都有据可查；而定时任务的实现则利用时间戳比较来驱动设备状态更改。整个设计思路简单明了，易于扩展和维护。

![](https://i-blog.csdnimg.cn/direct/847ef1d198c143209e974e6dda0de4cd.png)













---

# 三、结果分析
![](https://i-blog.csdnimg.cn/direct/65723e37f5fb44dab1046c882c4c1266.png)



![](https://i-blog.csdnimg.cn/direct/3b6777291e3e43cc9cea8d0c5acaae3a.png)

![](https://i-blog.csdnimg.cn/direct/45c9ef2c4a974c99a5a31fd20bd7831f.png)  
![](https://i-blog.csdnimg.cn/direct/99b5a3a8b246454c832a5a2c3c597430.png)

![](https://i-blog.csdnimg.cn/direct/07d4cf39c23f486196d45235799f6fdf.png)  
![](https://i-blog.csdnimg.cn/direct/06655bf0c31e4e8ca3b93812e7feca6f.png)

![](https://i-blog.csdnimg.cn/direct/b6ba814e48544bad80fbb8cb816885a8.png)

![](https://i-blog.csdnimg.cn/direct/05f8f8ad30be4efb9572b5928291548b.png)



---![](https://i-blog.csdnimg.cn/direct/d8feed4c993c46b58a0ab4b0b9a0769e.png)

