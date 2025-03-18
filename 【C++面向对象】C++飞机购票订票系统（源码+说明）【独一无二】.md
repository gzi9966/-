
---

> # C++飞机购票订票系统（源码+说明）【独一无二】
> @[TOC](目录)
>

---



---

# 一、功能描述
实现了一个飞机订票系统。主要功能包括：

1. 用户可以录入航班信息，包括航班号、起飞时间、降落时间、起飞城市、降落城市、座位信息、价格和折扣等。
2. 用户可以查询特定航班的信息，包括起飞时间、降落时间、起飞城市、降落城市、剩余座位、价格和折扣等。
3. 用户可以删除航班信息，删除后航班及其相关的用户订票信息将从系统中移除。
4. 用户可以查看所有航班的信息，包括航班号、起飞时间、降落时间、起飞城市、降落城市、剩余座位、价格和折扣等。
5. 用户可以进行订票操作，录入自己的姓名、身份证号和要订票的航班号，系统会为用户分配座位。
6. 用户可以查询特定用户的信息，包括姓名、身份证号、订票航班和座位号等。
7. 用户可以进行退票操作，输入要退票的用户姓名，系统会取消该用户对应的座位，并将座位号返还给航班的空余座位。
8. 用户可以查看所有用户的信息，包括姓名、身份证号、订票航班和座位号等。
9. 用户可以选择退出系统。



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035261854-7f6a7f05-6b32-4ed0-a952-a6509de86f94.png)



---

# 二、功能实现
这段代码实现了一个简单的飞机订票系统。以下是其功能简述：

1. **LQueue 类（队列）**：
    - 实现了队列数据结构，支持入队（EnQueue）、出队（DeQueue）和检查队是否为空（Emply）等操作。
2. **userLink 类（用户）**：
    - 实现了用户信息的添加、删除和查询功能，包括添加用户（add_user）、删除用户（delete_user）、查询用户（seek_user）和输出用户信息（out_user）等方法。
3. **planeLink 类（飞机）**：
    - 实现了航班信息的添加、删除和查询功能，包括添加航班（add_plane）、删除航班（delete_plane）、查询航班（seek_plane）和输出航班信息（out_plane）等方法。
    - 还包括了处理航班座位的功能，如录入退订座位号（back_seat）和返回座位号（out_seat）等方法。



4. **主函数**：

+ 提供了用户界面，支持以下操作：
    - 录入航班信息（1）
    - 查询航班信息（2）
    - 删除航班信息（3）
    - 输出航班信息（4）
    - 用户订票（5）
    - 查询用户信息（6）
    - 用户退票（7）
    - 输出用户信息（8）
    - 退出系统（9）

通过以上功能，用户可以实现对航班和用户信息的管理，包括录入、查询、删除和输出等操作。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035269485-8173a741-3502-4851-a14c-365996dcd319.png)

# 三、功能展示
## 1.录入航班
![](https://i-blog.csdnimg.cn/blog_migrate/10b8f1992833b92015063dfa43fc340d.png)



## 2.查询航班
![](https://i-blog.csdnimg.cn/blog_migrate/1b0446add6c36e9ff10c4ca2db916350.png)

## 3.删除航班
![](https://i-blog.csdnimg.cn/blog_migrate/2ce663aff1ccae350e9421937b27b41e.png)

## 4.输出航班信息
![](https://i-blog.csdnimg.cn/blog_migrate/3fda8c251fa511c5b141878a14e8223f.png)



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035282865-86344c1a-606b-49b0-a203-d375dbfdef05.png)

## 5.用户订票
![](https://i-blog.csdnimg.cn/blog_migrate/07c9bcef4f695e7c3bffa8dd04cbbc4c.png)



## 6.查询用户
![](https://i-blog.csdnimg.cn/blog_migrate/97a06362744ddee6225f91b3990facd0.png)

## 7.输出用户信息
![](https://i-blog.csdnimg.cn/blog_migrate/55d7283ca7e33cfd3adaf3b3b1839ec5.png)

## 8.用户退票
![](https://i-blog.csdnimg.cn/blog_migrate/b4e88322e0671ba036030a5dde207fbb.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035330863-ca0bad2f-0c3a-43d9-b6d9-69507fa76125.png)

---

# 四、部分代码示例
```cpp
#include <iostream>
#include <stdlib.h>
#include <stdio.h>
#include <cstring>
using namespace std;

class userLink;
struct Node
{
    int i;
    Node* next;
};

class LQueue
{
private:
    Node* front, * rear;
public:
    LQueue();
    ~LQueue();
    void EnQueue(int x);
    int DeQueue();
    bool Emply();
};


struct Plane
{
    char plane_number[10];
    char strat_time[10];
    char end_time[10];
    char strat_city[20];
    char end_city[20];
    int full;
    int empty_seats;
    int all_seats;
    LQueue backseats;
    float price;
    float discount;
    Plane* next;
};

struct user
{
    char name[10];
    char ID[20];
    char plane_number[10];
    int order_number;
    user* next;
};

// 略.........
// 略.........

int main()
{
    planeLink air;
    userLink uli;
    int n, i, options;
    do
    {

        //system("cls");
        cout << "============================================================================" << endl
            << "=============                 飞机订票系统                     =============" << endl
            << "============================================================================" << endl
            << "                      1.录入航班." << endl
            << "                      2.查询航班." << endl
            << "                      3.删除航班." << endl
            << "                      4.输出航班信息." << endl
            << "                      5.用户订票." << endl
            << "                      6.查询用户." << endl
            << "                      7.用户退票." << endl
            << "                      8.输出用户信息." << endl
            << "                      9.退出." << endl
            << "============================================================================" << endl
            << "                      输入选项(1-8):";
        cin >> options;
        switch (options)
        {
        case 1:
        {
            //system("cls");
            cout << "录入几个航班:";
            cin >> n;
            //system("cls");
            for (i = 0; i < n; i++)
                air.imp_plane();
        }break;
        case 2:
        {
            //system("cls");
            char s[10];
            cout << "请输入要查询的航班:";
            cin >> s;
            //system("cls");
            air.seek_plane(s);
            system("pause");
        }break;
        case 3:
        {
            //system("cls");
            char s[10];
            cout << "请输入要删除的航班:";
            cin >> s;
            air.delete_plane(s, uli);
            system("pause");
        }break;
        case 4:
        {
            //system("cls");
            air.out_plane();
            system("pause");
        }break;
        case 5:
        {
            //system("cls");
            cout << "需要定票几张:";
            cin >> n;
            //system("cls");
            for (i = 0; i < n; i++)
                uli.imp_user(air);
        }break;
        case 6:
        {
            //system("cls");
            char s[10];
            cout << "请输入查询的用户:";
            cin >> s;
            //system("cls");
            uli.seek_user(s);
            system("pause");
        }break;
        case 7:
        {
            //system("cls");
            char s[10];
            cout << "请输入要退票的姓名:";
            cin >> s;
            uli.delete_user(s, air);
            system("pause");
        }break;
        case 8:
        {
            //system("cls");
            uli.out_user();
            system("pause");
        }break;
        }
    } while (options < 9);

}

```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742035339796-c1350a50-b739-4322-8943-9d164123a45f.png)

