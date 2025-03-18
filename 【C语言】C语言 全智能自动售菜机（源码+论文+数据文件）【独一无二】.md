---

> # 【C语言】C语言 全智能自动售菜机（源码+论文+数据文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
模拟一个智能自动售菜机系统，主要功能是通过卡号验证后，用户可以选择购买不同种类的商品（如蔬菜或肉类）。系统首先通过 `verifyCard` 函数验证用户输入的卡号是否存在于文件 `cards.txt` 中，若验证成功，则进入购买流程，否则提示刷卡失败。接下来，用户可以选择购买有机蔬菜或肉类，每类商品展示出不同的商品选项以及对应的价格。用户通过选择商品编号来购买相应的商品，系统将自动累加所选商品的价格，直到用户选择返回或退出。商品分为两类：蔬菜（如菠菜、上海青、大白菜等）和肉类（如鸡肉、牛肉、猪肉等），每个商品都定义了名称和价格。程序的界面简单明了，通过 `showMenu` 和 `showProducts` 函数引导用户进行商品选择。最终，程序会显示用户购买的商品总价，完成购买流程。此程序模拟了一个基本的自动售货机功能，通过文件验证和商品选择的方式实现了自动化购物体验。



![](https://i-blog.csdnimg.cn/direct/39857f30d53844f097ae95025bfbeb4f.png)

---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/2aaaa7292bd648a9944df2cc137775ad.png)



## 1. 欢迎模块
### 设计思路
该模块用于程序启动时打印欢迎信息，告诉用户进入了全智能自动售菜机系统。函数 `welcome()` 直接调用 `printf` 打印欢迎横幅，具有较好的用户友好性。

### 代码
```c
void welcome() {
    printf("*********************************\n");
    printf("*    欢迎使用全智能自动售菜机    *\n");
    printf("*********************************\n");
}
```

---

## 2. 卡号验证模块
### 设计思路
为了确保只有合法用户才能使用自动售菜机，本模块通过读取存有合法卡号的文件（如 `"cards.txt"`），并将用户输入的卡号与文件中记录的卡号进行比对。  

+ 打开文件失败时给予提示；  
+ 用 `fgets` 逐行读取，并使用 `strcmp` 进行比较；  
+ 如果找到匹配的卡号，返回验证成功，否则返回失败。

### 代码
```c
int verifyCard(const char *filename, char *cardNumber) {
    FILE *file = fopen(filename, "r");
    if (!file) {
        printf("无法打开卡号文件！\n");
        return 0;
    }

    char line[20];
    // 此处略去10+行代码

    fclose(file);
    return 0; // 验证失败
}
```

---

![](https://i-blog.csdnimg.cn/direct/86b728738d664f1d876a97751b2aee3b.png)

## 3. 主菜单显示模块
### 设计思路
`showMenu()` 用于显示购买商品的主菜单，列出商品类别（如有机蔬菜、肉类）及退出选项，让用户根据需要选择购买的种类。  

+ 菜单内容清晰明了，便于后续流程控制。

### 代码
```c
void showMenu() {
    printf("请选择购买的商品类型：\n");
    printf("1. 有机蔬菜\n");
    printf("2. 肉类\n");
    printf("0. 退出\n");
}
```

---

## 4. 商品显示模块
### 设计思路
`showProducts()` 用于显示某一类别下的具体商品列表。  

+ 接收一个商品数组及该数组的商品数，依次打印每个商品的名称和价格；  
+ 同时提示返回上一级选项（输入 0）。

### 代码
```c
void showProducts(Product products[], int count) {
    printf("请选择商品：\n");
    // 此处略去10+行代码
    printf("0. 返回上一级\n");
}
```

> 注：代码中价格前的 "?" 可根据需要调整为货币符号或其他标识。
>

---

## 5. 商品结构体定义模块
### 设计思路
定义了 `Product` 结构体来表示售卖的商品，包含商品名和价格两个基本属性。  

+ 便于后续构造商品数组，存储不同类别（例如蔬菜、肉类）的商品信息。

### 代码
```c
typedef struct {
    char name[20];
    double price;
} Product;
```

---

## 6. 主函数与流程控制模块
### 设计思路
主函数是整个程序的核心控制模块，主要实现以下流程：

1. 程序启动时调用 `welcome()` 显示欢迎信息；
2. 提示用户输入卡号，并调用 `verifyCard()` 进行验证，验证失败则退出程序；
3. 定义两个商品数组（蔬菜和肉类）及初始总价变量；
4. 进入主菜单循环：  
    - 调用 `showMenu()` 显示主菜单，让用户选择购买类别；  
    - 如果用户选择了蔬菜或肉类，则进入相应的内部循环，调用 `showProducts()` 显示对应商品列表；
    - 用户在内部菜单选择具体商品，则将商品价格累加到总价中；用户输入 0 返回上一级继续选择其他种类；
5. 当用户在主菜单中输入 0 时退出循环，最后输出购买完成的总价。  
![](https://i-blog.csdnimg.cn/direct/2f3836e0a88142f2800e093cd4262e08.png)

### 代码
```c
int main() {
    welcome();

    // 卡号验证
    char cardNumber[20];
    printf("请输入卡号：");
    scanf("%s", cardNumber);

    if (!verifyCard("cards.txt", cardNumber)) {
        printf("卡号不存在，刷卡失败。\n");
        return 0;
    }
    printf("刷卡成功，欢迎您！\n");

    // 定义商品：有机蔬菜和肉类
    Product vegetables[] = {
        {"菠菜", 3.5}, {"上海青", 4.0}, {"大白菜", 2.5}, {"鸡毛菜", 5.0}, {"空心菜", 4.5}};
    Product meats[] = {
        {"鸡肉", 17.0}, {"牛肉", 25.0}, {"猪肉", 20.0}, {"羊肉", 28.0}};

    double totalPrice = 0.0;
    int choice;
    
    // 主菜单循环
    do {
        showMenu();
        scanf("%d", &choice);

        // 此处略去10+行代码
    } while (choice != 0);

    printf("购买完成，总价为：?%.2f\n", totalPrice);
    return 0;
}
```

---

![](https://i-blog.csdnimg.cn/direct/ae95f17fb44d44bd91fac12feef0aeaf.png)



1. **欢迎模块**：打印欢迎信息，提示用户进入系统。  
2. **卡号验证模块**：通过读取文件验证用户卡号，控制用户合法性。  
3. **主菜单与商品显示模块**：显示购买菜单和具体商品列表，方便用户选择。  
4. **商品数据结构模块**：用结构体 `Product` 存储商品信息，为后续操作提供数据支持。  
5. **核心控制模块（主函数）**：组织整个购买流程，负责调用各模块完成验证、选择、累计金额及最终输出总价。









---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/0f4aad1224d4445cab599bef86be1537.png)

![](https://i-blog.csdnimg.cn/direct/a3f3fd2ff7cb4a138a2c50928e4453ac.png)  
![](https://i-blog.csdnimg.cn/direct/c5b639233b6746e3991f77b4af9ddfca.png)



![](https://i-blog.csdnimg.cn/direct/1081e4dba2d94fcab053c3cb24927f76.png)

![](https://i-blog.csdnimg.cn/direct/02eac85b306749ceb0d79b2153cf66f5.png)

![](https://i-blog.csdnimg.cn/direct/4cf7dd84bfbb4c2cbfb5ab744afb60ee.png)

![](https://i-blog.csdnimg.cn/direct/b961439d5b6b4c5fbc933c44e857d7a8.png)























---

