---

> # 【C语言】C语言-学生选课管理系统（源码）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
某校某学期共开设了 n门课程，开学前学生根据所学专业和学分要求完成选课，每门课程基本信息包括:课程号、课程名、课程性质(考试/考查)、学时、学分、任课老师、开课系部、限选人数、已选人数等,学生选课信息包括:学号、姓名、专业、班级、所选课程信息等试设计一个学生选课管理系统，要求完成以下功能:  
1、课程信息管理:课程基本信息的增删改;注意:课程基本信息主要指课程选课前的有关信息，包括课程号、课程名、课程性质(考试/考查)、学时、学分、任课老师、开课系部、限选人数等,  
2、选课学生信息管理:学生选课基本信息的增删改:注意:基本信息主要指学生选课前的有关信息，包括学号、姓名、专业、班级等，  
3、选课:学生通过查询课程信息的基本信息后完成选课操作;注意:可分别按课程名、任课老师、开课系部等多种形式查询。  
4、退课:学生进行退课操作;  
5、信息查询:教师可查询某门课学生选课情况;学生可查询已完成的选课情况。

---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/0c2cbb5dac8a440c90ab26d0cbee2cf5.png)



## 1. 数据结构设计
本系统采用结构体（struct）来描述核心对象，主要包括三个数据对象：

+ **课程（Course）**  
存储课程编号、名称、性质（考试或考查）、学时、学分、任课老师、所属系部、限选人数以及当前选课人数等信息。
+ **学生（Student）**  
记录学生的学号、姓名、专业和班级信息，实现对学生基本信息的管理。
+ **选课记录（Enrollment）**  
用于表示学生与课程之间的多对多关系，每条记录都保存了学生ID与课程ID的对应关系。

```c
typedef struct {
    char courseID[MAX_NAME_LEN];
    char courseName[MAX_NAME_LEN];
    char courseType[MAX_NAME_LEN];  // 考试/考查
    int hours;
    int credits;
    char teacher[MAX_NAME_LEN];
    char department[MAX_NAME_LEN];
    int maxStudents;
    int enrolledStudents;
} Course;

typedef struct {
    char studentID[MAX_NAME_LEN];
    char studentName[MAX_NAME_LEN];
    char major[MAX_NAME_LEN];
    char className[MAX_NAME_LEN];
} Student;

typedef struct {
    char studentID[MAX_NAME_LEN];
    char courseID[MAX_NAME_LEN];
} Enrollment;
```

---

## 2. 功能模块设计
### 2.1 课程信息管理模块
**目标：**  
实现课程的添加、删除和修改操作。  

**设计思路：**  

+ **添加课程（addCourse）**  
检查当前课程数量是否达到上限，若未达到，则利用`scanf`从控制台获取用户输入的课程信息，并保存到课程数组中，同时课程的`enrolledStudents`初始设为0。  
+ **删除课程（removeCourse）**  
根据输入的课程编号在数组中查找匹配项，若存在则用数组末尾的课程覆盖当前删除项，并减少课程计数。  
+ **修改课程（updateCourse）**  
查找到目标课程后，重新获取用户输入的新信息并覆盖原有数据。

```c
void addCourse() {
    if (courseCount >= MAX_COURSES) {
        printf("课程数量已达到上限。\n");
        return;
    }
    Course c;
    printf("请输入课程号: ");
    scanf("%s", c.courseID);
    printf("请输入课程名: ");
    scanf("%s", c.courseName);
    printf("请输入课程性质(考试/考查): ");
    scanf("%s", c.courseType);
    printf("请输入学时: ");
    scanf("%d", &c.hours);
    printf("请输入学分: ");
    scanf("%d", &c.credits);
    printf("请输入任课老师: ");
    scanf("%s", c.teacher);
    printf("请输入开课系部: ");
    scanf("%s", c.department);
    printf("请输入限选人数: ");
    scanf("%d", &c.maxStudents);
    c.enrolledStudents = 0;
    courses[courseCount++] = c;
    printf("课程添加成功。\n");
}
```

---

![](https://i-blog.csdnimg.cn/direct/c809ae1d26254da6a1308cbce0ac58c9.png)

### 2.2 学生信息管理模块
**目标：**  
添加、删除和修改学生基本信息，确保每个学生的记录独一无二。

**设计思路：**  

+ **添加学生（addStudent）**  
检查当前学生数量是否达到上限，并从用户处读取学号、姓名、专业和班级信息，将信息存储在学生数组中。  
+ **删除/修改学生（removeStudent/updateStudent）**  
同样利用学号作为唯一标识，寻找目标学生，并对其记录进行删除或覆盖更新操作。

```c
void addStudent() {
    if (studentCount >= MAX_STUDENTS) {
        printf("学生数量已达到上限。\n");
        return;
    }
    Student s;
    printf("请输入学号: ");
    scanf("%s", s.studentID);
    printf("请输入姓名: ");
    scanf("%s", s.studentName);
    printf("请输入专业: ");
    scanf("%s", s.major);
    printf("请输入班级: ");
    scanf("%s", s.className);
    students[studentCount++] = s;
    printf("学生添加成功。\n");
}
```

---

### 2.3 选课与退课管理模块
**目标：**  
完成学生选课和退课的业务逻辑，实现学生与课程之间的关联管理。

**设计思路：**  

+ **选课（enrollCourse）**  
用户提供学生学号和课程编号后，首先验证课程是否存在及其选课人数是否已满；若条件满足，则在课程记录中更新已选学生数，并在选课数组中增加一条记录。  
+ **退课（dropCourse）**  
根据学生和课程的信息查找相应的选课记录，删除记录并在对应课程中减少已选人数。

```c
void enrollCourse() {
    if (enrollmentCount >= MAX_STUDENTS * MAX_COURSES) {
        printf("选课记录已达到上限。\n");
        return;
    }
    char studentID[MAX_NAME_LEN];
    char courseID[MAX_NAME_LEN];
    printf("请输入学生学号: ");
    scanf("%s", studentID);
    printf("请输入课程号: ");
    scanf("%s", courseID);
    // 此处略去50 行代码
            strcpy(e.courseID, courseID);
            enrollments[enrollmentCount++] = e;
            printf("选课成功。\n");
            return;
        }
    }
    printf("未找到该课程。\n");
}
```

---

### 2.4 信息查询模块
**目标：**  
支持通过课程编号查询全部选课学生信息，或通过学生学号查询其选课详情。

**设计思路：**  

+ **查询课程选课情况（queryEnrollmentByCourse）**  
用户输入课程编号后，遍历选课记录，筛选出对应课程的所有学生，再逐一打印每个学生的信息。  
+ **查询学生选课情况（queryEnrollmentByStudent）**  
同理，遍历记录，找到与输入学号匹配的所有选课记录，并展示相应课程的详细信息。

```c
void queryEnrollmentByCourse() {
    char courseID[MAX_NAME_LEN];
    printf("请输入课程号: ");
    scanf("%s", courseID);
    printf("学生选课情况如下:\n");
    for (int i = 0; i < enrollmentCount; ++i) {
        if (strcmp(enrollments[i].courseID, courseID) == 0) {
            for (int j = 0; j < studentCount; ++j) {
                if (strcmp(students[j].studentID, enrollments[i].studentID) == 0) {
                    printf("学号: %s, 姓名: %s, 专业: %s, 班级: %s\n",
                           students[j].studentID, students[j].studentName, students[j].major, students[j].className);
                }
            }
        }
    }
}
```

---

![](https://i-blog.csdnimg.cn/direct/759818b8da94457198e9bf749fe9e968.png)

### 2.5 用户交互与菜单设计
**目标：**  
为用户提供一个简单直观的界面，通过菜单引导用户完成各项操作。

**设计思路：**  

+ 利用`displayMenu()`及其他辅助函数展示不同模块的操作选项。
+ 主函数中通过无限循环和`switch`语句，根据用户输入调用对应功能模块函数，实现模块间的无缝衔接和整体流程的控制。

```c
void displayMenu() {
    printf("学生选课管理系统\n");
    printf("1. 课程信息管理\n");
    printf("2. 学生信息管理\n");
    printf("3. 选课\n");
    printf("4. 退课\n");
    printf("5. 信息查询\n");
    printf("6. 退出\n");
}

int main() {
    int choice;
    while (1) {
        displayMenu();
        printf("请输入选择: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1: {
               // 此处略去50 行代码
                break;
            }
            case 6: return 0;
            default: printf("无效的选择。\n"); break;
        }
    }
    return 0;
}
```

---

整个系统采用模块化设计，将课程管理、学生管理、选退课和信息查询功能独立拆分，每一部分职责明确：

+ 利用结构体及数组存储数据，确保数据之间可以互相关联，
+ 通过各个独立的函数处理具体业务逻辑，使代码清晰且便于后续扩展（如加入文件存储或图形化界面）；
+ 基于文本菜单与交互使得用户体验简单直观。



---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/14f110edbd6744feb0b9fd584f71759b.png)

![](https://i-blog.csdnimg.cn/direct/d0458040817442a6ae247b182e276ffe.png)



![](https://i-blog.csdnimg.cn/direct/68fa2577504a401da7bc7a6c8f64914b.png)

![](https://i-blog.csdnimg.cn/direct/b776e803ba1b4e99a7a4f55575197299.png)

**其他截图 略....**



![](https://i-blog.csdnimg.cn/direct/d02d5e302c2349d79ff3768863b9b520.png)

---

