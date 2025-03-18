

---

> # 【C语言】C语言 好声音比赛管理系统（含源码+数据文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
设计一个用于管理“好声音”比赛的选手数据的系统，主要功能包括输入选手数据、评委打分、成绩排序、数据查询、追加选手数据以及将数据保存到文件中。系统通过结构体 `Contestant` 来存储选手的基本信息，包括选手编号、姓名、评委打分、以及平均分。程序的主要操作流程如下：首先，用户可以通过菜单选择不同的功能。例如，用户可以输入选手的基本数据，接着为每位选手的比赛表现打分（每个选手有5个评委评分），系统自动计算每位选手的平均分。接下来，用户可以根据选手的平均分对成绩进行排序，从高到低排列。系统还提供查询功能，用户可以通过输入选手编号来查询特定选手的信息。如果需要，可以添加新的选手数据，或者将现有数据保存到本地文件 `contestants.txt` 中，确保数据的持久化。系统通过循环菜单的方式不断运行，直到用户选择退出。整体设计简洁，功能全面，能够满足比赛管理的基本需求，适用于小型比赛的管理与数据存储。



![](https://i-blog.csdnimg.cn/direct/2f595eb1cbcf47d28f421aed57b0bf34.png)

---

# 二、设计思路
## 1. 数据结构定义模块
### 设计思路
程序首先定义了一个结构体 `Contestant`，用来存储每个选手的信息。该结构体包含：

+ 选手编号（`id`）
+ 选手姓名（`name`）
+ 评委打分数组（`scores`，共 5 个评委的分数）
+ 选手平均分（`avgScore`）

这样设计能够完整记录每位选手的基本信息及后续评委打分的结果，方便后续计算和处理。

### 关键代码
```c
// 定义选手数据结构
typedef struct {
    int id;
    char name[50];
    float scores[5]; // 假设有5个评委
    float avgScore;
} Contestant;
```

---

## 2. 全局变量及函数声明模块
### 设计思路
程序使用一个全局的 `Contestant` 数组来存储所有选手数据，并利用全局变量 `count` 来记录选手数量。同时，提前声明各个功能模块的函数，便于后续实现和调用。

### 关键代码
```c
Contestant contestants[100];
```

```c
// 函数声明
void inputContestantData(Contestant contestants[], int *count);
void judgeScoring(Contestant contestants[], int count);
void sortScores(Contestant contestants[], int count);
void searchData(Contestant contestants[], int count);
void addContestantData(Contestant contestants[], int *count);
void saveDataToFile(Contestant contestants[], int count);
void displayMenu();
```

---

## 3. 菜单显示模块
### 设计思路
`displayMenu()` 函数用于显示系统主菜单，提示用户可执行的各项操作。该模块将菜单内容打印出来，使用户能够直观了解操作选项。

### 关键代码
```c
void displayMenu() {
    printf("\n===== 好声音比赛管理系统 =====\n");
    printf("1. 输入选手数据\n");
    printf("2. 评委打分\n");
    printf("3. 成绩排序（按平均分）\n");
    printf("4. 数据查询\n");
    printf("5. 追加选手数据\n");
    printf("6. 写入数据文件\n");
    printf("7. 退出系统\n");
}
```

---

## 4. 选手数据输入模块
### 设计思路
`inputContestantData()` 函数用于录入选手的基本信息，包括选手编号和姓名，并初始化平均分为 0。函数通过参数传入当前选手的数组与数量，输入完成后更新选手数量。

### 关键代码
![](https://i-blog.csdnimg.cn/direct/6c22f5df343348afb81149a086f373ee.png)

```c
void inputContestantData(Contestant contestants[], int *count) {
    printf("输入选手数据：\n");
    printf("请输入选手编号：");
    scanf("%d", &contestants[*count].id);
    printf("请输入选手姓名：");
    scanf("%s", contestants[*count].name);
    contestants[*count].avgScore = 0; // 初始化平均分
    (*count)++;
}
```

---

## 5. 评委打分模块
### 设计思路
`judgeScoring()` 函数实现评委为每个选手打分，每个选手由 5 个评委打分，然后计算平均分。函数遍历所有已录入的选手，对每位选手依次输入 5 个分数，累加后求均值并存入选手的 `avgScore` 字段。

### 关键代码
```c
void judgeScoring(Contestant contestants[], int count) {
    for (int i = 0; i < count; i++) {
        printf("为选手 %s 打分：\n", contestants[i].name);
        float total = 0;
        for (int j = 0; j < 5; j++) {
            printf("请输入评委 %d 的分数：", j + 1);
            scanf("%f", &contestants[i].scores[j]);
            total += contestants[i].scores[j];
        }
        contestants[i].avgScore = total / 5;
    }
}
```

---

## 6. 成绩排序模块
![](https://i-blog.csdnimg.cn/direct/7ace1cbc52e442eab5a242da54bad9c7.png)

### 设计思路
`sortScores()` 函数利用冒泡排序算法，根据选手的平均分对选手数组进行排序，排序方式为按平均分从高到低排列。排序后，程序输出“成绩已排序”的提示信息，方便用户查看结果。

### 关键代码
```c
void sortScores(Contestant contestants[], int count) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (contestants[j].avgScore < contestants[j + 1].avgScore) {
                Contestant temp = contestants[j];
                contestants[j] = contestants[j + 1];
                contestants[j + 1] = temp;
            }
        }
    }
    printf("成绩已排序。\n");
}
```

---

## 7. 数据查询模块
### 设计思路
`searchData()` 函数允许用户输入一个选手的编号，然后在选手数据中查找匹配的选手信息，并输出该选手的编号、姓名和平均分。如果没有找到对应的选手，则输出提示信息。

### 关键代码
```c
void searchData(Contestant contestants[], int count) {
    int id;
    printf("请输入要查询的选手编号：");
    scanf("%d", &id);
    for (int i = 0; i < count; i++) {
        if (contestants[i].id == id) {
            printf("编号: %d, 姓名: %s, 平均分: %.2f\n", contestants[i].id, contestants[i].name, contestants[i].avgScore);
            return;
        }
    }
    printf("未找到编号为 %d 的选手。\n", id);
}
```

---

## 8. 追加选手数据模块
### 设计思路
`addContestantData()` 函数实现对已有数据的追加，即调用前面录入数据的函数 `inputContestantData()`，从而使用户可以在原有基础上继续增加选手数据。

### 关键代码
```c
void addContestantData(Contestant contestants[], int *count) {
    inputContestantData(contestants, count);
}
```

---

## 9. 数据保存模块
### 设计思路
`saveDataToFile()` 函数用于将当前选手数据保存到文件中。函数打开名为 "contestants.txt" 的文件（写入模式），遍历选手数组，将每个选手的编号、姓名和平均分输出到文件中，然后关闭文件。这样，即使程序退出，也可以通过文件恢复数据。

### 关键代码
```c
void saveDataToFile(Contestant contestants[], int count) {
    FILE *file = fopen("contestants.txt", "w");
    if (file == NULL) {
        printf("无法打开文件！\n");
        return;
    }
    for (int i = 0; i < count; i++) {
        fprintf(file, "编号: %d, 姓名: %s, 平均分: %.2f\n", contestants[i].id, contestants[i].name, contestants[i].avgScore);
    }
    fclose(file);
    printf("数据已保存到文件。\n");
}
```

![](https://i-blog.csdnimg.cn/direct/3c398a0b8385482fb770d5bd046ca45f.png)

---

## 10. 主函数与整体流程控制模块
### 设计思路
主函数中首先初始化存储选手数据的数组和选手数量变量，然后进入一个 do–while 循环展示菜单，根据用户输入的选项依次调用上面各个功能模块：

+ 输入选手数据
+ 评委打分
+ 成绩排序
+ 数据查询
+ 追加选手数据
+ 写入数据文件  
同时，当用户选择退出时，退出循环并结束程序。

### 关键代码
```c
int main() {
    Contestant contestants[100];
    int count = 0;
    int choice;

    do {
        displayMenu();
        printf("请输入您的选择：");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                inputContestantData(contestants, &count);
                break;
            case 2:
                judgeScoring(contestants, count);
                break;
            case 3:
                sortScores(contestants, count);
                break;
            case 4:
                searchData(contestants, count);
                break;
            case 5:
                addContestantData(contestants, &count);
                break;
            case 6:
                saveDataToFile(contestants, count);
                break;
            case 7:
                printf("退出系统。\n");
                break;
            default:
                printf("无效选项，请重新选择。\n");
        }
    } while (choice != 7);

    return 0;
}
```

![](https://i-blog.csdnimg.cn/direct/991ab6df28e8427599b3a605ed0028fc.png)



本程序采用模块化设计，主要模块包括：

1. **数据结构定义模块**：定义 `Contestant` 结构体存储选手基本信息。
2. **文件读写模块**：通过 `loadData`（本例中未单独提取加载函数）和 `saveDataToFile` 实现数据文件持久化保存。
3. **数据录入与追加模块**：实现选手数据的输入与追加，更新选手数量。
4. **评委打分模块**：实现对每个选手的打分，并计算平均分。
5. **成绩排序模块**：采用冒泡排序算法按平均分从高到低排序选手。
6. **数据查询模块**：通过输入选手编号查询并输出选手信息。
7. **主函数模块**：提供菜单驱动的用户交互，调用相应功能模块完成管理任务。

---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/9d0d2c2d81054867bae0330d9a482cbf.png)



![](https://i-blog.csdnimg.cn/direct/a7a4002ea94a40619acef1af43e1c2fc.png)



![](https://i-blog.csdnimg.cn/direct/e08847ff40384906bd4912cf83bbc218.png)



![](https://i-blog.csdnimg.cn/direct/9a120226a6744e478ca06376acbcc015.png)  
![](https://i-blog.csdnimg.cn/direct/19d7791cff184038bb8926c2e05cd324.png)







---

