---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
一、停车场管理系统的设计与实现  
1设计要求  
1.1问题描述（任务）  
设有一个可以停放n辆汽车的狭长停车场，它只有一个大门可以供车辆进出。车辆按到达停车场时间的早晚依次从停车场最里面向大门口处停放（最先到达的第一辆车放在停车场的最里面）。如果停车场已放满n辆车，则后来的车辆只能在停车场大门外的便道上等待，一旦停车场内有车开走，则排以便道上的第一辆车就进入停车场。停车场内如有某辆车要开走，在它之后进入停车场的车都必须先退出停车场为它让路，待其开出停车场后，这些辆再依原来的次序进场。每辆车在离开停车场时，都应根据它在停车场内停留的时间长短交费。如果停留在便道上的车未进停车场时，允许其离去，不收停车费，并且仍然保持在便道上等待的车辆的次序。

1.2基本功能60~70分  
要求程序输出每辆车到达后的停车位置（停车场或便道上），以及某辆车离开停车场应交纳的费用和它在停车场内停留的时间。

1.3建议拓展的功能20分  
①系统功能以菜单方式显示  
②车辆编号为字符型  
③设置屏幕字体颜色  
④单位时间收费值可以修改

![](https://i-blog.csdnimg.cn/direct/4d661b9f4df749f9a6c0ede0597fde36.png)



---

# 二、设计思路
## 1. 总体设计思路
本系统模拟车辆进入停车场和在便道等待的管理情况，主要思想包括：

+ **数据结构选择**  
    - 用栈（Stack）来模拟停车场。停车场遵循先进后出（LIFO）原则，当车离开时，需要将后进车辆先弹出，才能找到目标车辆，然后再将中间车辆重新入栈。
    - 用循环队列（Queue）来模拟便道的等待队列。便道按照先进先出（FIFO）管理，等待车辆可以依次进入停车场。
+ **基本操作**  
    - **车辆到达**：当车辆到达系统时，若停车场未满，则直接进入停车场；否则，车辆进入便道等待。
    - **车辆离开**：车辆在离开停车场时，需要先从停车场中找到目标车辆，计算费用，并将临时存放的非目标车辆依次恢复入栈。同时，便道队列中的第一辆车会进入停车场。
    - **收费设置**：系统支持修改每单位时间的收费标准。
+ **菜单驱动**  
系统通过文本菜单与用户交互，用户可选择车辆到达、车辆离开、修改收费值及退出系统。



## 2. 数据结构设计
### 2.1 定义车结构体（Car）
车的基本信息包括车牌号、到达时间和离开时间。  
代码如下：

```c
typedef struct {
    char id[10];      // 车牌号
    int arrive_time;  // 到达时间
    int leave_time;   // 离开时间
} Car;
```

### 2.2 定义停车场结构体（Stack）
使用栈结构模拟停车场，数组保存车信息，`top` 表示栈顶指针。  
代码如下：

```c
#define MAX_CAR 10  // 定义停车场最大车位数

typedef struct {
    Car cars[MAX_CAR];
    int top;
} Stack;
```

![](https://i-blog.csdnimg.cn/direct/04e4bd90df914f1d849f41a06c663f5a.png)

### 2.3 定义便道结构体（Queue）
采用循环队列模拟便道。数组存放等待车辆，`front`、`rear` 分别为队头和队尾指针。  
代码如下：

```c
#define MAX_WAIT 10 // 定义便道最大等待车位数

typedef struct {
    Car cars[MAX_WAIT];
    int front;
    int rear;
} Queue;
```

---

## 3. 模块说明与代码
### 3.1 初始化模块
#### 3.1.1 初始化栈
初始化时将 `top` 设为 -1，表示栈为空。

```c
void initStack(Stack *s) {
    s->top = -1;
}
```

#### 3.1.2 初始化队列
初始化队列时，将 `front` 和 `rear` 都设置为 0。

```c
void initQueue(Queue *q) {
    q->front = q->rear = 0;
}
```

---

### 3.2 判满/判空操作
#### 3.2.1 栈的判满与判空
+ **栈满判断**：当 `top` 达到 `MAX_CAR - 1` 时，表示停车场已满  
+ **栈空判断**：`top` 的值为 -1 时表示停车场为空

```c
int isStackFull(Stack *s) {
    return s->top == MAX_CAR - 1;
}

int isStackEmpty(Stack *s) {
    return s->top == -1;
}
```

#### 3.2.2 队列的判满与判空
+ **队列满判断**：利用循环队列，当 `(rear + 1) % MAX_WAIT == front` 时，队列满  
+ **队列空判断**：当 `rear == front` 时，队列为空

```c
int isQueueFull(Queue *q) {
    return (q->rear + 1) % MAX_WAIT == q->front;
}

int isQueueEmpty(Queue *q) {
    return q->rear == q->front;
}
```

![](https://i-blog.csdnimg.cn/direct/926b34948e5d467e8929a5da13c3b275.png)

---

### 3.3 基本数据操作
#### 3.3.1 栈操作：入栈与出栈
+ **入栈**：在栈不满时，将车辆数据存入数组并更新 `top`  
+ **出栈**：在栈不空时，从栈顶弹出车辆数据，并将 `top` 减 1

```c
void pushStack(Stack *s, Car car) {
    if (isStackFull(s)) {
        printf("停车场已满\n");
        return;
    }
    s->cars[++s->top] = car;
}

Car popStack(Stack *s) {
    if (isStackEmpty(s)) {
        printf("停车场为空\n");
        Car empty_car = {"", -1, -1};
        return empty_car;
    }
    return s->cars[s->top--];
}
```

#### 3.3.2 队列操作：入队与出队
+ **入队**：在队列未满时，将车辆添加到 `rear` 处，并更新队尾  
+ **出队**：在队列非空时，从 `front` 处取出车辆，并更新队头

```c
void enqueue(Queue *q, Car car) {
    if (isQueueFull(q)) {
        printf("便道已满\n");
        return;
    }
    q->cars[q->rear] = car;
    q->rear = (q->rear + 1) % MAX_WAIT;
}// 此处省略20行代码
    }
    Car car = q->cars[q->front];
    q->front = (q->front + 1) % MAX_WAIT;
    return car;
}
```

---

![](https://i-blog.csdnimg.cn/direct/91d93fee0e654e3ebcde893d9e32ab41.png)

### 3.4 车辆到达处理模块
当车辆到达时，首先构造车辆记录（包括车牌、到达时间及初始化离开时间），然后判断停车场是否有空位：

+ 如果停车场满，则将车辆加入便道队列；
+ 否则直接入栈到停车场。

```c
void carArrival(Stack *parking, Queue *waiting, char *id, int time) {
    Car car;
    strcpy(car.id, id);     // 设置车牌号
    car.arrive_time = time; // 设置到达时间
    car.leave_time = -1;    // 初始化离开时间

    if (isStackFull(parking)) {
        enqueue(waiting, car); // 停车场满，将车加入便道
        printf("车牌号%s的车停在便道上\n", id);
    } else {
        pushStack(parking, car); // 停车场未满，将车推入停车场
        printf("车牌号%s的车停在停车场内\n", id);
    }
}
```

---

### 3.5 车辆离开处理模块
当车辆离开时，需要从停车场中找到目标车辆，因此需要使用一个临时栈辅助：

1. 从停车场栈中依次弹出车辆，直到找到目标车辆。
2. 找到车辆后设置离开时间，并计算停车持续时间及费用。
3. 将临时栈中暂时移出的车辆重新推入停车场，以保持原有顺序。
4. 如果便道有等待车辆，则将便道队首的车辆进入停车场，并更新其到达时间为当前离开的时间。

```c
void carDeparture(Stack *parking, Queue *waiting, char *id, int time, int fee_per_unit) {
    Stack temp_stack;
    initStack(&temp_stack); // 初始化临时栈
    Car car;
    int found = 0;

    // 从停车场中寻找要离开的车
    while (!isStackEmpty(parking)) {
        car = popStack(parking);
        if (strcmp(car.id, id) == 0) {
            found = 1;
            car.leave_time = time; // 设置离开时间
            break;
        // 此处省略20行代码
            printf("车牌号%s的车从便道进入停车场\n", next_car.id);
        }
    }
}
```

---

### 3.6 菜单与主函数
#### 3.6.1 显示菜单
菜单函数将显示系统支持的操作选项，包括车辆到达、离开、修改收费值以及退出系统。

```c
void displayMenu() {
    printf("\n停车场管理系统\n");
    printf("1. 车辆到达\n");
    printf("2. 车辆离开\n");
    printf("3. 修改收费值\n");
    printf("4. 退出系统\n");
    printf("请选择操作: ");
}
```

#### 3.6.2 主函数
在 `main()` 函数中，首先初始化停车场和便道数据结构，然后进入菜单循环，根据用户选择调用相应模块，最后程序退出。

```c
int main() {
    Stack parking;
    Queue waiting;
    initStack(&parking); // 初始化停车场栈
    initQueue(&waiting); // 初始化便道队列

    int fee_per_unit = 2;  // 每单位时间收费值
    int choice;
    char id[10];
    int time;

    // 主循环：显示菜单并处理用户选择
    while (1) {
        displayMenu();
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("请输入车牌号: ");
                scanf("%s", id); // 输入车牌号
                printf("请输入到达时间: ");
                // 此处省略20行代码
            default:
                printf("无效的选择，请重新输入\n");
        }
    }

    return 0;
}
```

![](https://i-blog.csdnimg.cn/direct/41d7b774f48d4842a117684c9960da6d.png)

---

---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/ed5bbf66bd9f41baaf34952fbc1d8ebe.png)  
![](https://i-blog.csdnimg.cn/direct/5b35b683810c46ef89a8b6a6496b4f22.png)

![](https://i-blog.csdnimg.cn/direct/3e6b700e83024733903a53e9333b683d.png)  
![](https://i-blog.csdnimg.cn/direct/500fbf48bf804294ae7a98ce89219701.png)

---

