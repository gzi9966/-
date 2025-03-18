---

> # 系C语言 实践课题选题系统（源码+报告+数据文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
 设计一个实践课题选题系统，完成学生选题功能。教师可以添加、管理、发布自己的课题题目，并查看选择题目的学生情况。学生登陆后，可以查看教师题目 ，并进行选题。  
实践课题题目包括：题目编号、题目名称、人员数（由几个人来完成）、指导教师等信息。  
学生信息包括：学号、姓名、性别、年龄、班级、专业等信息。  
（1）添加功能：程序能够添加学生的记录和实践课题题目记录，提供选择界面供用户选择所要添加的类别。添加记录时，要求学号和编号要唯一。如果添加了重复记录，则提示数据添加重复并取消添加。  
（2）查询功能：可根据学号、姓名、编号、名称等信息对已添加的学生和实践课题题目进行查询，如果未找到，给出相应的提示信息，如果找到，则显示相应的记录信息。  
（3）显示功能：可显示当前系统中所有学生的信息和实践课题题目信息，每条记录占据一行。  
（4）编辑功能：可根据查询结果对相应的记录进行修改，修改时注意学号的唯一性。  
（5）删除功能：主要实现对已添加的学生和实践课题题目记录进行删除。如果当前系统中没有相应的记录，则提示“记录为空！”并返回操作。  
（6）统计功能：能根据多种参数进行统计。能按实践课题题目名称统计出学生选择该题目的人员的信息。  
（7）保存功能：可将当前系统中各类记录存入文件中，存入方式任意。  
（8）读取功能：可将保存在文件中的信息读入到当前系统中，供用户进行使用。



![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)



---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/3d418fbe0a4d41f187f1da966a3120dd.png)

为实现高效的数据管理和操作，系统采用结构体数组的形式管理学生和课题信息：

+ 学生结构体（Student）：包括学号、姓名、性别、年龄、班级、专业以及已选课题编号等字段。学号作为唯一标识，用于检索和操作学生信息。
+ 课题结构体（Topic）：包括课题编号、课题名称、预计人数和指导教师等字段。课题编号作为唯一标识，用于查询和分配课题。  
两个结构体均以全局数组形式存储，设置了学生和课题的最大数量上限（MAX_STUDENTS 和 MAX_TOPICS）。通过全局变量记录当前学生和课题的数量，便于动态管理数据。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STUDENTS 100
#define MAX_TOPICS   100

// 学生结构体
typedef struct {
    char id[20];             // 学号 (唯一)
    char name[20];           // 姓名
    char sex[10];            // 性别
    int  age;                // 年龄
    char className[20];      // 班级
    char major[20];          // 专业
    char chosenTopicId[20];  // 已选课题编号(可为空字符串)
} Student;

// 课题结构体
typedef struct {
    char id[20];      // 课题编号 (唯一)
    char name[50];    // 课题名称
    int  personCount; // 人员数
    char teacher[20]; // 指导教师
} Topic;

// 全局数组存储
Student g_students[MAX_STUDENTS];
Topic   g_topics[MAX_TOPICS];

// 当前学生和课题的数量
int g_studentCount = 0;
int g_topicCount   = 0;
```

每个功能模块均通过一个独立的函数实现，确保代码结构的清晰和可维护性。主要功能的实现逻辑如下：

+ 添加功能：检查数据唯一性（学号或课题编号）后，将新数据添加至对应数组，并更新计数器。
+ 查询功能：支持按学号、姓名（学生）或编号、名称（课题）进行查询，并展示匹配结果。
+ 显示功能：依次遍历学生和课题数组，输出所有记录的信息，方便用户查看。
+ 编辑功能：通过唯一标识找到对应数据，更新其字段值并重新保存。
+ 删除功能：通过唯一标识找到记录，并将最后一条记录覆盖到删除位置，减少数据移动操作的开销。
+ 统计功能：根据课题名称找到对应课题编号，并输出选择该课题的所有学生信息。
+ 文件操作：通过文件读写功能实现数据的持久化存储，将学生和课题信息分别保存到两个文件中，支持系统重启后数据恢复。
+ 选题功能：学生输入学号后，系统验证其身份，并允许其从可选课题中选择一个，将课题编号保存到学生记录中。

```c
int main(void) {
    int choice;
    do {
        printf("\n================= 实践课题选题管理系统 =================\n");
        printf("1. 添加记录 (学生 / 课题)\n");
        printf("2. 查询记录 (学生 / 课题)\n");
        printf("3. 显示所有记录 (学生 & 课题)\n");
        printf("4. 编辑记录 (学生 / 课题)\n");
        printf("5. 删除记录 (学生 / 课题)\n");
        printf("6. 统计 (按课题名称查看选课学生)\n");
        printf("7. 保存记录到文件\n");
        printf("8. 从文件读取记录\n");
        printf("9. 学生选题\n");
        printf("0. 退出系统\n");
        printf("=======================================================\n");
        printf("请输入你的选择: ");
        scanf("%d", &choice);
        getchar();

        switch(choice) {
            case 1: addRecord(); break;
            case 2: searchRecord(); break;
            case 3: displayRecords(); break;
            case 4: editRecord(); break;
            case 5: deleteRecord(); break;
            case 6: statisticsInfo(); break;
            case 7: saveToFile(); break;
            case 8: readFromFile(); break;
            case 9: chooseTopic(); break;
            case 0: printf("已退出系统。\n"); break;
            default: printf("无效的选择，请重新输入。\n"); break;
        }
    } while(choice != 0);

    return 0;
}
```

![](https://i-blog.csdnimg.cn/direct/083f241b386b4666805e7464ebf0df70.png)

```c
void addRecord() {
    int subChoice;
    printf("\n--- 添加记录 ---\n");
    printf("1. 添加学生\n");
    printf("2. 添加课题\n");
    printf("请输入选择: ");
    scanf("%d", &subChoice);
    getchar();

    if (subChoice == 1) {
        if (g_studentCount >= MAX_STUDENTS) {
            printf("学生数量已达上限，无法添加。\n");
            return;
        }
        Student temp;
        printf("请输入学号: ");
        scanf("%s", temp.id);
        getchar();

        if (isStudentIdExist(temp.id)) {
            printf("该学号已存在，添加失败。\n");
            return;
        }

        printf("请输入姓名: ");
        scanf("%s", temp.name);
        getchar();

        printf("请输入性别: ");
        scanf("%s", temp.sex);
        getchar();

        printf("请输入年龄: ");
        scanf("%d", &temp.age);
        getchar();

        printf("请输入班级: ");
        scanf("%s", temp.className);
        getchar();

        printf("请输入专业: ");
        scanf("%s", temp.major);
        getchar();

        strcpy(temp.chosenTopicId, "");
        g_students[g_studentCount++] = temp;
        printf("学生信息添加成功！\n");
    } else if (subChoice == 2) {
        if (g_topicCount >= MAX_TOPICS) {
            printf("课题数量已达上限，无法添加。\n");
            return;
        }
        Topic temp;
        //此处略去至少10行代码

        printf("请输入指导教师姓名: ");
        scanf("%s", temp.teacher);
        getchar();

        g_topics[g_topicCount++] = temp;
        printf("课题信息添加成功！\n");
    } else {
        printf("无效选择。\n");
    }
}
```

![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)  
![](https://i-blog.csdnimg.cn/direct/0d3abdca851b4834a1dc5a316f2347ab.png)

```c
void searchRecord() {
    int subChoice;
    printf("\n--- 查询记录 ---\n");
    printf("1. 查询学生\n");
    printf("2. 查询课题\n");
    printf("请输入选择: ");
    scanf("%d", &subChoice);
    getchar();

    if (subChoice == 1) {
        printf("查询学生: 1. 按学号  2. 按姓名\n");
        int mode;
        scanf("%d", &mode);
        getchar();

        //此处略去至少10行代码
            }
        }
    }
}
```

![](https://i-blog.csdnimg.cn/direct/c2bfc7394db248889aec445c81c2d7bb.png)



```c
void saveToFile() {
    FILE *fpStu = fopen("students.txt", "w");
    if (!fpStu) {
        printf("无法保存学生信息。\n");
        return;
    }
    for (int i = 0; i < g_studentCount; i++) {
        fprintf(fpStu, "%s %s %s %d %s %s %s\n", g_students[i].id, g_students[i].name,
                g_students[i].sex, g_students[i].age, g_students[i].className,
                g_students[i].major, g_students[i].chosenTopicId);
    }
    fclose(fpStu);
}
```

![](https://i-blog.csdnimg.cn/direct/80f008191b914ecebe2fc1edc66acc0f.png)



统计信息代码

```c
void statisticsInfo() {
    if (g_topicCount == 0) {
        printf("当前无课题信息，无法统计。\n");
        return;
    }
    char tName[50];
    printf("请输入要统计的课题名称: ");
    scanf("%s", tName);
    getchar();

    // 找到课题编号
    int topicIndex = -1;
    for (int i = 0; i < g_topicCount; i++) {
        if (strcmp(g_topics[i].name, tName) == 0) {
            topicIndex = i;
            break;
        }
    }
    if (topicIndex == -1) {
        printf("未找到名称为 [%s] 的课题。\n", tName);
        return;
    }

    // 根据课题编号统计选课学生
   //此处略去至少10行代码

    if (!found) {
        printf("目前没有学生选择该课题。\n");
    }
}
```

统计信息模块的设计旨在按课题名称统计选课学生的详细信息。通过输入课题名称，系统首先在课题列表中检索匹配的课题编号，然后遍历学生信息，筛选出已选择该课题的学生并输出详细信息。如果未找到匹配的课题或没有学生选择该课题，系统会提示用户，确保结果的明确性和友好性。  
此模块结合课题与学生数据，实现了两者间的联动统计，满足了教务管理对选题分布信息的分析需求。代码逻辑清晰，模块化程度高，便于扩展，例如可以进一步加入统计选题人数限制或分析选题分布趋势等功能，提升管理效率和数据洞察能力。

![](https://i-blog.csdnimg.cn/direct/4e1f75f11167499ba8a4fe258407497f.png)

编辑信息代码

```c
void editRecord() {
    int subChoice;
    printf("\n--- 编辑记录 ---\n");
    printf("1. 编辑学生\n");
    printf("2. 编辑课题\n");
    printf("请输入选择: ");
    scanf("%d", &subChoice);
    getchar();

    if (subChoice == 1) {
        // 编辑学生
        char sId[20];
        printf("请输入要编辑的学生学号: ");
        scanf("%s", sId);
        getchar();

        int idx = findStudentIndexById(sId);
        if (idx == -1) {
            printf("未找到该学号的学生。\n");
            return;
        }

        // 显示原信息并开始编辑
        Student *ps = &g_students[idx];
        printf("原信息: 学号:%s 姓名:%s 性别:%s 年龄:%d 班级:%s 专业:%s 选题编号:%s\n",
               ps->id, ps->name, ps->sex, ps->age, ps->className, ps->major, ps->chosenTopicId);

        printf("请输入新的姓名: ");
        scanf("%s", ps->name);
        getchar();

        printf("请输入新的性别: ");
        scanf("%s", ps->sex);
        getchar();

        printf("请输入新的年龄: ");
        scanf("%d", &ps->age);
        getchar();

        printf("请输入新的班级: ");
        scanf("%s", ps->className);
        getchar();

        printf("请输入新的专业: ");
        scanf("%s", ps->major);
        getchar();

        printf("学生信息更新成功！\n");

    } else if (subChoice == 2) {
        // 编辑课题
        char tId[20];
        printf("请输入要编辑的课题编号: ");
        scanf("%s", tId);
        getchar();

        int idx = findTopicIndexById(tId);
        if (idx == -1) {
            printf("未找到该编号的课题。\n");
            return;
        }
}
```

编辑信息模块允许用户更新学生或课题的相关信息，确保系统中的数据始终准确且与实际需求一致。该模块通过唯一标识符（学生的学号或课题编号）检索目标记录，避免了对无关数据的误操作。  
功能逻辑设计分为两部分：一是更新学生信息，包括姓名、性别、年龄、班级和专业；二是更新课题信息，包括课题名称、预计人员数和指导教师。编辑功能在显示原有信息的基础上逐项更新，直观地提示用户输入修改项，同时避免无意覆盖。代码清晰简洁，便于扩展，如可增加对输入数据的格式校验或对特定字段的编辑权限控制，以进一步提升系统的安全性与鲁棒性。

![](https://i-blog.csdnimg.cn/direct/bcedec5259274246b9e75966f9141519.png)

![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)

```c
void saveToFile() {
    // 保存学生信息
    FILE *fpStu = fopen("students.txt", "w");
    if (fpStu == NULL) {
        printf("无法打开文件 students.txt 进行保存。\n");
        return;
    }
    for (int i = 0; i < g_studentCount; i++) {
        fprintf(fpStu, "%s %s %s %d %s %s %s\n",
                g_students[i].id, g_students[i].name, g_students[i].sex,
                g_students[i].age, g_students[i].className, g_students[i].major,
                g_students[i].chosenTopicId);
    }
    fclose(fpStu);

    // 保存课题信息
    //此处略去至少10行代码

    printf("数据保存成功！\n");
}

```

保存信息模块的主要作用是将内存中的学生和课题数据写入到外部文件 (students.txt 和 topics.txt)，以实现数据的持久化存储。模块通过文件流操作，将结构化数据逐行格式化输出到文本文件中，每条记录的字段按照固定顺序排列，方便后续读取和解析。  
此功能确保系统在退出后仍能保留当前数据，便于下次启动时加载，避免数据丢失。设计中对文件打开失败进行了检查，以防止文件系统错误引发的程序崩溃。同时，通过分别保存学生和课题信息，简化了数据管理逻辑。未来可以进一步优化文件存储格式，例如采用二进制文件以提高效率，或引入加密机制以保护敏感数据的安全性。

![](https://i-blog.csdnimg.cn/direct/1da367bba8014edfbc4e4c2ebec53c9c.png)



```c
删除信息代码
void deleteRecord() {
    int subChoice;
    printf("\n--- 删除记录 ---\n");
    printf("1. 删除学生\n");
    printf("2. 删除课题\n");
    printf("请输入选择: ");
    scanf("%d", &subChoice);
    getchar();

    if (subChoice == 1) {
        // 删除学生
        if (g_studentCount == 0) {
            printf("当前没有学生记录可删除！\n");
            return;
        }
        char sId[20];
        printf("请输入要删除的学生学号: ");
        scanf("%s", sId);
        getchar();

        int idx = findStudentIndexById(sId);
        if (idx == -1) {
            printf("未找到该学号的学生。\n");
            return;
        }
}

```

删除信息模块实现了从系统中移除指定学生或课题记录的功能。通过输入学号或课题编号，系统定位到需要删除的记录，并采用覆盖替换法删除数据：将数组中最后一条记录复制到目标位置，并减少记录计数。这种方法避免了在数组中移动大量数据，提高了删除操作的效率。  
模块设计简洁且高效，适合小型数据管理需求。然而，删除操作并未检查学生与课题的关联性，例如删除课题前未验证是否有学生选了该课题。未来可进一步优化逻辑，例如提示用户删除的潜在影响或通过链式存储实现动态数据管理，提升系统的安全性和可维护性。

![](https://i-blog.csdnimg.cn/direct/9302e5806af846c3bac4f1ab16df722f.png)

```c
学生选题代码
void chooseTopic() {
    // 输入学生学号
    char sId[20];
    printf("请输入学生学号: ");
    scanf("%s", sId);
    getchar();

    int studentIdx = findStudentIndexById(sId);
    if (studentIdx == -1) {
        printf("未找到该学号的学生。\n");
        return;
    }

    // 显示当前可选课题
    printf("\n--- 可选课题列表 ---\n");
    for (int i = 0; i < g_topicCount; i++) {
        Topic t = g_topics[i];
        printf("编号:%s 名称:%s 人数:%d 教师:%s\n", t.id, t.name, t.personCount, t.teacher);
    }

    // 输入课题编号
   // ...................

    // 更新学生选题信息
    strcpy(g_students[studentIdx].chosenTopicId, tId);
    printf("学生 [%s] 成功选择课题 [%s]。\n", g_students[studentIdx].name, tId);
}


```

学生选题模块是系统中学生与课题之间的核心交互功能，允许学生通过输入学号登录并选择课题。实现流程包括验证学生身份、显示当前可选课题、验证课题编号有效性，并最终记录学生的选题信息。系统还检查学生是否已选课题，避免重复选题。  
此模块的设计简单明了，易于扩展。例如，可进一步添加人数限制检查，防止某一课题超额选题；也可以实现选题的撤销或修改功能，增强用户灵活性。选题模块与学生和课题数据关联密切，增强了系统功能的实用性和数据的可追溯性。



---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/9d815913aaa64ce99b06d1a5e23ce273.png)  
![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)  
![](https://i-blog.csdnimg.cn/direct/97dbf9d8a3944aefb4a3f6796dbe05de.png)



![](https://i-blog.csdnimg.cn/direct/90c8e72a6eec4b49a5c319deec4e0c7d.png)

![](https://i-blog.csdnimg.cn/direct/930f2b4cbb554e83a5d36af0e4e124d7.png)  
![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)  
![](https://i-blog.csdnimg.cn/direct/3d065d1c354f4962ad7bf3296a38b4eb.png)



![](https://i-blog.csdnimg.cn/direct/2796b3ca274040d79b417276738c3188.png)



![](https://i-blog.csdnimg.cn/direct/0b9f5b6ba91a4ceb8e288294a2dc7d3e.png)



![](https://i-blog.csdnimg.cn/direct/d1237b2128734a01ba41c8457f33f6a3.png)



![](https://i-blog.csdnimg.cn/direct/37c9388474d3420bb0385f04850d6992.png)



---

