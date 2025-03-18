> # C/C++招聘信息管理系统（源码）【独一无二】
> @[TOC](目录)
>

---



---

# 一、功能描述
C++/C实现的招聘信息管理系统，主要功能包括录入招聘信息、浏览招聘信息、查询单位用人数、统计某职位有多少招聘单位、修改学历和薪资、薪资升序排列以及删除招聘单位信息。详细解释每个模块的功能：

1. **头文件包含和结构体定义：**
    - 包含了 `<stdio.h>` 和 `<string.h>` 头文件，分别用于输入输出和字符串处理。
    - 定义了一个结构体 `JobInfo`，用来表示招聘信息，包括招聘单位、职位、学历要求、招聘人数和薪资。
2. **全局变量和结构体数组初始化：**
    - 定义了一个结构体数组 `jobs` 用来存储招聘信息。
    - 定义了一个全局变量 `jobCount` 来记录已录入的招聘信息数量。
3. **录入招聘信息函数 (**`inputJob`**)：**
    - 提供了一个交互式界面，允许用户输入招聘信息，并将其存储到 `jobs` 数组中。



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035563004-223b294f-beea-4447-b95b-1eba257cf285.png)



4. **浏览招聘信息函数 (**`listJobs`**)：**
    - 循环遍历 `jobs` 数组，将每条招聘信息输出到屏幕上。
5. **查询单位用人数函数 (**`queryTotalCount`**)：**
    - 统计指定单位需要的总人数，通过输入单位名称，在所有招聘信息中查找单位名称匹配的记录，然后将其招聘人数累加返回。
6. **统计某职位有多少招聘单位函数 (**`statsPositions`**)：**
    - 统计指定职位有多少个招聘单位，通过输入职位名称，在所有招聘信息中查找职位名称匹配的记录，然后计数并输出结果。
7. **修改招聘信息函数 (**`modifyJob`**)：**
    - 允许用户输入招聘单位名称，然后修改该单位的学历要求和薪资。
8. **薪资升序排列函数 (**`sortJobsBySalary`**)：**
    - 将招聘信息按照薪资升序排列，采用冒泡排序算法实现。
9. **删除招聘单位信息函数 (**`deleteJob`**)：**
    - 允许用户输入要删除的招聘单位名称，然后删除该单位的招聘信息。
10. **主函数 (**`main`**)：**
+ 提供了一个循环菜单，允许用户选择不同的操作。
+ 调用相应的函数来执行用户选择的操作，直到用户选择退出。

程序实现了一个简单的招聘信息管理系统，用户可以通过菜单选择不同的功能来管理招聘信息，包括录入、浏览、查询、统计、修改、排序和删除。



# 二、功能展示
## 2.1. 录入招聘信息
![](https://i-blog.csdnimg.cn/blog_migrate/eb90f9538a63b091da48ec8d475c7ebe.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035569219-07f55e8c-0830-4d49-aa48-d42fb588d0ae.png)

## 2.2. 浏览招聘信息
![](https://i-blog.csdnimg.cn/blog_migrate/e4f5cc7b6b4c242c714eb93dcdde2953.png)



## 2.3. 查看单位用人数
![](https://i-blog.csdnimg.cn/blog_migrate/49468fc312ec3ee770b6df5c5ece5c2f.png)



## 2.4.统计职位招聘单位
![](https://i-blog.csdnimg.cn/blog_migrate/f4321c76e77adbebf019e94c6fc36a41.png)

## 2.5.修改学历和薪资
![](https://i-blog.csdnimg.cn/blog_migrate/06aa16c59bf49e7f93ce52d972e8b39d.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035575420-8236fc0c-6b81-42fb-9fa8-d149c526a96c.png)

## 2.6.薪资升序排列
![](https://i-blog.csdnimg.cn/blog_migrate/7df92f75f0fee4bf610cf06e83b46db6.png)

## 2.7.退出
![](https://i-blog.csdnimg.cn/blog_migrate/a8a5d44e2b00d738491be530c110ecfd.png)

## 2.8.删除招聘单位信息
![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035581536-773d5bc8-af5f-412f-859f-5b841e2fa66e.png)

![](https://i-blog.csdnimg.cn/blog_migrate/1f56d207b7bc3bfda6906a9786a5cf6e.png)

# 三、代码示例
```cpp
#include<stdio.h>
#include<string.h>
//定义结构体
struct Joblnfo{
    char unit[50];
    char position[50];
    char education[20];
    int count;
    int salary;
};

//定义结构体数组并初始化
#define MAX_JOBS 100
#define MAX_UNIT_LEN 50
#define MAX_POSITION_LEN 50
#define MAX_SALARY_LEN 4000
Joblnfo jobs[MAX_JOBS];
int jobCount=0;


//录入招聘信息函数
void inputJob(){
    Joblnfo newJob;
    printf("招聘单位：");
    scanf("%49s",newJob.unit);
    printf("职位：");
    scanf("%49s",newJob.position);
    printf("学历：");
    scanf("%19s",newJob.education);
    printf("招聘人数：");
    scanf("%d",&newJob.count);
    printf("薪资：");
    scanf("%d",&newJob.salary);
    jobs[jobCount++]=newJob;
}

// 略 .....................
// 略 .....................


//主函数
int main(){
    int choice;
    int jobCount=0;
    //循环菜单
    do{
        printf("=====招聘信息管理系统=====\n");
        printf("1.录入招聘信息\n");
        printf("2.浏览招聘信息\n");
        printf("3.查看单位用人数\n");
        printf("4.统计某职位有多少招聘单位\n");
        printf("5.修改学历和薪资\n");
        printf("6.薪资升序排列\n");
        printf("7.退出\n");
        printf("0.删除招聘单位信息\n");
        printf("==========================\n");
        scanf("%d",&choice);
    switch(choice){
    case 0:
        deleteJob();
        break;
    case 1:
        inputJob();
        break;
    case 2:
        listJobs();
        break;
    case 3:
        {
        char unit[MAX_UNIT_LEN];
        printf("输入要查询的招聘单位:");
        scanf("%s",unit);
        int total=queryTotalCount(unit);
        printf("招聘单位%s需要的总人数:%d\n",unit,total);
        }
        break;
    case 4:
        {
            char position[MAX_POSITION_LEN];
            printf("输入要统计的职位");
            scanf("%s",position);
            statsPositions(position);
        }
        break;

    case 5:
        {
            char unit[MAX_UNIT_LEN];
            printf("输入需要修改的招聘单位");
            scanf("%s",unit);
            modifyJob(unit);
        }
        break;
    case 6:
        sortJobsBySalary();
        listJobs();
        break;
    case 7:
        printf("感谢使用,谢谢");
        return 0;
    default:
        printf("输入无效,请重新输入\n");
    }
    while(getchar()!='\n');

    }
    while(choice!=7);
    return 0;
}

```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035590505-e138b031-4934-459f-8e11-49e5306ccc6d.png)

