---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
程序设计一个高考成绩管理系统，程序分为多个模块，包括用户注册、登录、修改密码、学生信息输入、数据存储、查询及排序等功能。系统中主要有两个结构体：`Student`和`User`。`Student`结构体存储学生的个人信息，包括学号、姓名、性别、学校、五门科目的成绩及总成绩；而`User`结构体用于管理用户的账号和密码。

系统支持用户注册和登录，通过验证用户名和密码的方式来确保用户的安全性。登录成功后，用户可以进入主菜单，执行学生数据的输入、保存、查询等操作。此外，系统还提供了多种排序算法，如冒泡排序、堆排序、快速排序等，用于按照学生的总成绩对学生信息进行排序。

在学生管理方面，程序可以统计各学校的录取情况，显示按成绩排序的学生信息，以及删除学生记录等功能。同时，系统能够根据学生的成绩计算各批次的录取分数线，并统计不同性别学生的数量。用户还可以根据不同条件进行学生信息的查询和修改。

此外，系统支持将学生数据导出到文件中，方便后续的数据分析或存档。每个功能模块都有详细的操作提示，用户可以通过选择不同的操作编号来执行相应的功能。

总体来说，该程序是一个综合性的学生信息管理系统，具有较强的扩展性，可以根据需要添加更多功能，如数据分析、图表展示等。

---

![](https://i-blog.csdnimg.cn/direct/1abac1ca56674ab4bd17a0fa465e6926.png)



# 二、设计思路
本系统的设计思路说明，程序实现了基于用户登录的考生数据管理系统。系统主要包括用户管理模块、考生数据输入/输出模块、成绩统计与录取批次模块、排序模块、查询与修改模块以及数据导出模块。各个模块逻辑功能清晰，下面逐一说明每个模块的设计思路和关键代码。

---

## 1. 用户管理模块
### 设计思路
本模块用于实现用户注册、登录和修改密码等功能，确保只有合法用户可以访问系统。主要的数据结构为 `User`（包含用户名和密码），并使用全局数组存储所有用户；  
设计的主要函数包括：  

+ **register_user()**：注册新用户，检测用户数是否超限，然后录入用户名和密码；  
+ **login_user()**：实现登录，通过遍历用户数组匹配用户名与密码；  
+ **change_password()**：允许用户通过输入用户名、旧密码，再录入新密码来修改密码；  
+ **user_menu()**：提供一个用户操作菜单，包含注册、登录、修改密码与退出。

### 关键代码
```c
typedef struct {
    char username[USERNAME_LEN];
    char password[PASSWORD_LEN];
} User;

User users[USER_COUNT];
int user_count = 0;

void register_user() {
    if (user_count >= USER_COUNT) {
        printf("用户数量已达到上限\n");
        return;
    }
    //  此处略去至少20 行代码
        }
    }
    printf("用户名或密码错误\n");
    return 0;
}

void change_password() {
    char username[USERNAME_LEN];
    //  此处略去至少20 行代码
            return;
        }
    }
    printf("用户名或旧密码错误\n");
}

void user_menu() {
    int choice;
    while (1) {
        printf("请选择操作:\n");
        printf("1. 注册\n");
        //  此处略去至少20 行代码
        }
    }
}
```

---

## 2. 考生数据输入/输出模块
### 设计思路
本模块负责录入考生（学生）数据、保存和加载数据到文件。考生数据存储在全局数组 `students` 中，结构体 `Student` 包含考号、姓名、性别、学校名、各科成绩（5门）以及总成绩。  
主要函数包括：

+ **input_students_data()**：录入一定数量的考生数据，同时计算总成绩并写入数据文件；
+ **save_students_data()**：将数组中的所有考生信息写入文件，覆盖旧数据；
+ **load_students_data()**：从数据文件中读取考生信息更新内存中的数据，并更新 `student_count`。

### 关键代码
```c
typedef struct {
    char id[15];
    char name[NAME_LEN];
    char gender[10];
    char school[SCHOOL_NAME_LEN];
    int subject_scores[5];
    int total_score;
} Student;

Student students[STUDENT_COUNT];
int student_count = 0;

void input_students_data() {
    FILE* file = fopen("students_data.txt", "w");
    if (!file) {
        printf("无法创建文件\n");
        return;
    }
    
    //  此处略去至少20 行代码
}

void save_students_data() {
    FILE* file = fopen("students_data.txt", "w");
    if (!file) {
        printf("无法创建文件\n");
        return;
    }
    for (int i = 0; i < student_count; i++) {
        fprintf(file, "%s %s %s %s %d %d %d %d %d %d\n",
                students[i].id, students[i].name, students[i].gender, students[i].school,
                students[i].subject_scores[0], students[i].subject_scores[1], students[i].subject_scores[2],
                students[i].subject_scores[3], students[i].subject_scores[4], students[i].total_score);
    }
    fclose(file);
}

void load_students_data() {
    FILE* file = fopen("students_data.txt", "r");
    if (!file) {
        printf("无法打开文件\n");
        return;
    }
    for (int i = 0; i < STUDENT_COUNT; i++) {
        if (fscanf(file, "%s %s %s %s %d %d %d %d %d %d",
                   students[i].id, students[i].name, students[i].gender, students[i].school,
                   &students[i].subject_scores[0], &students[i].subject_scores[1],
                   &students[i].subject_scores[2], &students[i].subject_scores[3],
                   &students[i].subject_scores[4], &students[i].total_score) != 10) {
            student_count = i;
            break;
        }
    }
    fclose(file);
}
```

---

![](https://i-blog.csdnimg.cn/direct/c45daf5670d94e028ff7b2dc51a532eb.png)

## 3. 成绩统计与录取批次模块
### 设计思路
此模块用于根据录取比例计算各批次录取分数线和统计各批次录取人数。  

+ **calculate_cutoff_scores()**：将所有考生总成绩存入数组后按从高到低排序，然后根据给定录取比例（如10%、25%、45%、70%）计算每个批次的分数线；  
+ **count_admissions()**：根据各考生的总成绩与录取分数线，统计每个批次的录取人数，并输出结果。

### 关键代码
```c
void calculate_cutoff_scores(int* cutoff_scores) {
    int total_scores[STUDENT_COUNT];
    for (int i = 0; i < student_count; i++) {
        total_scores[i] = students[i].total_score;
    }
    // 冒泡排序降序排列
    for (int i = 0; i < student_count - 1; i++) {
        for (int j = 0; j < student_count - i - 1; j++) {
            if (total_scores[j] < total_scores[j + 1]) {
                int temp = total_scores[j];
                total_scores[j] = total_scores[j + 1];
                total_scores[j + 1] = temp;
            }
        }
    }
    cutoff_scores[0] = total_scores[(int)(student_count * 0.10) - 1];
    c//  此处略去至少20 行代码
    }
    printf("一类本科录取人数: %d\n", counts[0]);
    printf("二类本科录取人数: %d\n", counts[1]);
    printf("三类本科录取人数: %d\n", counts[2]);
    printf("四类高职高专录取人数: %d\n", counts[3]);
}
```

---

## 4. 排序模块
### 设计思路
系统提供多种排序算法对考生成绩进行排序，以便进行排名、输出前十名信息等。  

+ 定义了 **bubble_sort()**、**heap_sort()**、**quick_sort()** 以及 **linear_sort()**（计数排序示例）几种算法；
+ 同时定义了通用函数 **sort_students()**，通过函数指针调用具体排序算法；
+ **display_sorted_students()** 用于输出排序后的考生详细信息。

### 关键代码
```c
void bubble_sort(Student* students, int count) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (students[j].total_score < students[j + 1].total_score) {
                Student temp = students[j];
                students[j] = students[j + 1];
                students[j + 1] = temp;
            }
        }
    }
}

void heapify(Student* students, int count, int root) {
    //  此处略去至少20 行代码
    }
}

void heap_sort(Student* students, int count) {
    //  此处略去至少20 行代码
        }
    }
    Student temp = students[i + 1];
    students[i + 1] = students[high];
    students[high] = temp;
    return i + 1;
}

void quick_sort(Student* students, int low, int high) {
    if (low < high) {
        int pi = partition(students, low, high);
        quick_sort(students, low, pi - 1);
        quick_sort(students, pi + 1, high);
    }
}

void linear_sort(Student* students, int count) {
    int max_score = students[0].total_score;
    for (int i = 1; i < count; i++) {
        if (students[i].total_score > max_score) {
            max_score = students[i].total_score;
        }
    }
    //  此处略去至少20 行代码
}

void sort_students(void (*sort_func)(Student*, int)) {
    sort_func(students, student_count);
}

void display_sorted_students() {
    printf("考生信息(按成绩排序):\n");
    for (int i = 0; i < student_count; i++) {
        printf("%s %s %s %s %d %d %d %d %d %d\n", students[i].id, students[i].name,
               students[i].gender, students[i].school,
               students[i].subject_scores[0], students[i].subject_scores[1],
               students[i].subject_scores[2], students[i].subject_scores[3],
               students[i].subject_scores[4], students[i].total_score);
    }
}
```

---

## 5. 统计与查询模块
### 设计思路
该模块实现了以下功能：

+ **search_student()**：通过输入考号或姓名查询考生的基本信息；
+ **school_admissions()**：统计各学校录取人数（按学校对考生进行分组统计）；
+ **top_students()**：输出总成绩排名前10的考生信息；
+ **delete_student()**：根据考号或姓名查找并删除对应考生记录，然后调整数组；
+ **count_gender()**：统计男女考生的人数；
+ **search_by_school()**：输入学校名后显示来自该学校的所有考生；
+ **modify_student()**：通过考号或姓名查找后允许修改考生信息，再重新计算总成绩；
+ **export_student_data()**：将所有考生数据导出到新的文本文件中。

### 关键代码
```c
void search_student() {
    char query[NAME_LEN];
    printf("请输入考号或姓名: ");
    scanf("%s", query);
    //  此处略去至少20 行代码
            return;
        }
    }
    printf("未找到考生信息\n");
}

void school_admissions() {
    char schools[STUDENT_COUNT][SCHOOL_NAME_LEN];
    int school_counts[STUDENT_COUNT] = {0};
    //  此处略去至少20 行代码
            }
        }
        if (!found) {
            strcpy(schools[unique_schools], students[i].school);
            school_counts[unique_schools]++;
            unique_schools++;
        }
    }
    //  此处略去至少20 行代码
}

void delete_student() {
    char query[NAME_LEN];
    printf("请输入要删除的考号或姓名: ");
    scanf("%s", query);
    for (int i = 0; i < student_count; i++) {
        if (strcmp(students[i].id, query) == 0 || strcmp(students[i].name, query) == 0) {
            for (int j = i; j < student_count - 1; j++) {
                students[j] = students[j + 1];
            }
            student_count--;
            printf("考生信息已删除\n");
            return;
        }
    }
    printf("未找到考生信息\n");
}

void count_gender() {
    int male_count = 0;
    int female_count = 0;
    //  此处略去至少20 行代码
    }
}

void modify_student() {
    char query[NAME_LEN];
    printf("请输入要修改的考号或姓名: ");
    scanf("%s", query);
    for (int i = 0; i < student_count; i++) {
        //  此处略去至少20 行代码
            printf("考生信息修改成功\n");
            return;
        }
    }
    printf("未找到考生信息\n");
}

void export_student_data() {
    FILE* file = fopen("exported_students_data.txt", "w");
    if (!file) {
        printf("无法创建文件\n");
        return;
    }
    //  此处略去至少20 行代码
    fclose(file);
    printf("考生成绩导出成功\n");
}
```

---

![](https://i-blog.csdnimg.cn/direct/7b6ff646d5db4dc88962ab963e15a992.png)

## 6. 主菜单模块
### 设计思路
`main_menu()` 为系统的主要功能菜单，提供录入数据、计算分数线、统计录取人数、查询成绩、排序、学校录取情况、前10名考生、删除数据、性别统计、按学校查询、修改数据和导出成绩等功能。  
在每个选项中，根据需要先调用 `load_students_data()` 读取文件数据，然后再调用相应功能函数完成操作。此模块通过循环不断显示菜单，直至用户选择退出。

### 关键代码
```c
void main_menu() {
    int choice;
    int cutoff_scores[4] = {0};
    while (1) {
        printf("请选择操作:\n");
        printf("1. 输入并保存考生数据\n");
        printf("2. 计算各批次录取分数线\n");
        printf("3. 统计各批次录取人数及考生信息\n");
        printf("4. 查询考生成绩及录取情况\n");
        printf("5. 按成绩排序并输出考生信息\n");
        printf("6. 统计各学校录取情况\n");
        printf("7. 输出总成绩排名前10的考生信息\n");
        printf("8. 删除考生信息\n");
        printf("9. 统计男女考生人数\n");
        printf("10. 按学校查询考生信息\n");
        printf("11. 修改考生信息\n");
        printf("12. 导出考生成绩\n");
        printf("13. 退出\n");
        printf("输入您的选择: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1:
                input_students_data();
                save_students_data();
                break;
            case 2:
                //  此处略去至少20 行代码
                break;
            case 12:
                load_students_data();
                export_student_data();
                break;
            case 13:
                return;
            default:
                printf("无效选择，请重新输入\n");
        }
    }
}
```

---

## 7. 总结
本系统采用模块化设计，各模块相对独立，主要包括：

1. **用户管理模块**：实现注册、登录、修改密码，并通过 `user_menu()` 控制用户访问系统；
2. **考生数据管理模块**：负责考生数据录入、保存和文件加载，确保数据持久化；
3. **成绩统计与录取模块**：从文件读入数据后计算批次录取分数线，并统计录取人数；
4. **排序模块**：提供多种排序算法（冒泡、堆、快速、计数排序），支持成绩排序和输出排名信息；
5. **统计与查询模块**：实现按考号/姓名查询、按学校查询、性别统计、删除、修改及数据导出功能；
6. **主菜单模块**：利用循环显示综合功能菜单，根据用户选择调用对应功能进行操作。

这样的设计使得系统功能清晰、接口明确，便于后续维护、扩展以及数据的管理和展示。



---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/c3f160e24b3b4c2e8b2eefc52ee88f93.png)  
![](https://i-blog.csdnimg.cn/direct/dfa87c1794254224b822048a22ce455c.png)

![](https://i-blog.csdnimg.cn/direct/c1a81544ef84466b9c5328d5ac91afcb.png)  
![](https://i-blog.csdnimg.cn/direct/b595b92ac2044f7bae44e3022dde1d09.png)

![](https://i-blog.csdnimg.cn/direct/8645cd149b874e3499aa0ffbd63f6c8b.png)  
![](https://i-blog.csdnimg.cn/direct/f5d537a47f534a0eb3b4f6910387767a.png)



![](https://i-blog.csdnimg.cn/direct/f416fa75d50f402689d0d00134bbb61a.png)



![](https://i-blog.csdnimg.cn/direct/8629731343f34f758a7b942cf2c28b56.png)

![](https://i-blog.csdnimg.cn/direct/5b3d9f2446d149e2af1a9928879b6d4e.png)



![](https://i-blog.csdnimg.cn/direct/6cff80520c804af1b537fee4cd431fec.png)





---

