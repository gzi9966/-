---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
### 1. 项目名称
文具店商品库存管理系统

### 2. 项目简介
设计并开发一款商品库存管理系统，用于高效管理文具店的商品库存。该系统支持商品的入库、出库操作，并提供库存查询功能，帮助文具零售店优化库存管理、提高运营效率。

**文具商品的基本信息包括：**

+ **商品编号**  
+ **商品名称**  
    - 数据类型：字符数组  
    - 示例：签字笔、水彩笔、田格本、日记本等（可自行设计）
+ **类别**  
    - 包括：笔、本、颜料、其他
+ **品牌**  
    - 示例：晨光、得力等（可自行设计）
+ **商品单价**  
    - 数据类型：浮点型
+ **商品库存**  
    - 数据类型：int



### 1. 系统功能要求
系统主要功能模块示意见“图1”以及“图2”（图示仅供参考），具体功能如下：

1. **批量录入初始商品信息**  
    - 提示用户批量录入初始商品信息，要求录入的商品数量不少于 5 条。  
    - 商品信息可以通过读取指定文本文件（例如：test.txt，格式参见图3）实现录入。  
    - 在录入过程中，对商品编号进行查重，同一编号的商品不能重复出现。  
    - 对于重复数据，应跳过录入并反馈提示信息，如“商品序号 xxx 已存在，跳过此商品”。  
    - 确保录入后的商品数据完整、有效，并保存于系统中以备后续操作。
2. **浏览全部商品信息**  
    - 在屏幕上显示仓库中所有商品的全部信息，要求数据对齐、格式美观。  
    - 如果当前仓库中没有商品，则显示提示“当前仓库无商品”。  
    - 显示格式参考图5。
3. **查询商品信息**  
    - 用户可根据以下条件查询商品信息：  
        * 商品编号  
        * 商品名称  
        * 品牌  
        * 类别
    - 用户选择查询条件后，系统根据输入内容匹配相应商品信息，并以整齐、格式化的方式显示查询结果（参见图6）。  
    - 若未找到符合条件的商品，系统应提示“未找到符合条件的商品信息”。
4. **增加商品信息**  
    - 提示用户按照规定的格式输入一条新增的商品信息（参考图7）。  
    - 系统需对新增商品的商品编号进行查重，若编号已存在，则提示“编号已存在，请重新输入”，直至输入有效。  
    - 新增的信息必须保证格式正确且数据完整。
5. **删除商品信息**  
    - 提示用户输入要删除的商品编号或商品名称。  
    - 系统显示该商品的全部信息（参照图8），并询问用户是否确认删除。  
    - 若存在同名商品，系统应提示“找到多条同名商品，将同时删除”，并在用户确认后进行删除操作。
6. **修改商品信息**  
    - 提示用户输入需要修改的商品编号，系统根据该编号显示商品的全部信息（参见图9）。  
    - 系统随后询问用户需要修改哪一项信息（除商品编号外的所有数据项均可修改）。  
    - 修改完成后，系统应提示“修改成功”。
7. **按类别统计商品数量**  
    - 提示用户输入需要统计的商品类别，系统统计该类别商品的数量，并以清晰格式显示结果（参考图10）。  
    - 若输入类别不存在，则提示“未找到该类别的商品信息”。
8. **按单价对商品排序**  
    - 系统根据商品单价对商品进行排序：  
        * 默认按单价升序显示，同一单价的商品按商品编号升序排序。  
        * 用户可选择按单价降序排序，同一单价的商品仍按商品编号升序排列。
    - 查询结果应以整齐格式展示（参考图11）。
9. **计算当前库存商品的总价值**  
    - 系统计算当前仓库中所有商品的总价值：  
        * 对每个商品，计算公式为：商品单价 × 库存数量  
        * 总价值为所有商品个别计算值的累加结果
    - 计算结果应清晰显示（参考图12）。  
    - 若仓库中无商品，系统提示“当前仓库无商品，无法计算总价值”。
10. **退出系统**  
    - 在退出前提示用户是否退出系统，经确认后退出。

![](https://i-blog.csdnimg.cn/direct/5c15caf5605047d1875ba105598ffc94.png)



---

# 二、设计思路
## 1. 总体设计思路
该系统主要目的是管理文具店的商品库存，通过文件实现数据持久化。系统支持以下主要操作：

+ **数据文件操作**  
程序启动时从文件中加载商品数据，退出时保存所有数据到文件。
+ **数据合法性校验**  
每条商品记录在录入时需进行合法性检查，确保输入数据完整且符合要求。
+ **基础数据操作**  
支持批量录入初始商品、浏览全部商品、查询、添加、删除、修改商品信息。
+ **统计与排序功能**  
根据商品类别统计数量、按照单价排序展示商品、计算库存总价值。
+ **菜单式操作**  
采用文本菜单驱动系统，用户根据提示输入指令，实现对各种操作模块调用。

整个系统基于结构体数组实现数据存储，每个功能模块独立成函数，主函数中先加载数据再进入菜单循环操作，最后退出前保存数据。

---

![](https://i-blog.csdnimg.cn/direct/a9ab6814f5e94952b86233c2e7311ae1.png)

## 2. 数据结构设计
系统使用如下结构体存储商品信息：

```c
#define NAME_LEN 50
#define BRAND_LEN 20
#define CATEGORY_LEN 20

typedef struct {
    int id;                          // 商品编号
    char name[NAME_LEN];             // 商品名称
    char category[CATEGORY_LEN];     // 类别
    char brand[BRAND_LEN];           // 品牌
    float price;                     // 单价
    int quantity;                    // 库存数量
} Product;
```

说明：  

+ 每个商品由编号、名称、类别、品牌、单价和库存数量构成。  
+ 全局数组 `products[MAX_PRODUCTS]` 用于存储所有商品记录，整型变量 `productCount` 表示当前商品数量。

---

## 3. 模块说明与代码分析
### 3.1 数据合法性校验
为了保证录入的数据是符合要求的，定义了函数 `validateProduct()`。

```c
// 数据合法性校验函数，判断一个商品记录是否有效
int validateProduct(Product p) {
    if (p.id <= 0 || strlen(p.name) == 0 || strlen(p.category) == 0 ||
        strlen(p.brand) == 0 || p.price <= 0 || p.quantity < 0) {
        return 0; // 不合法
    }
    return 1; // 合法
}
```

思路：  

+ 检查商品编号是否大于零；  
+ 检查名称、类别、品牌字符串不为空；  
+ 保证商品单价大于零、库存数量不为负。

---

### 3.2 数据文件操作模块
为确保数据的持久化，提供了**加载**和**保存**文件的两个函数。

#### 3.2.1 从文件加载数据
```c
void loadProductsFromFile() {
    FILE *file = fopen("test.txt", "r");
    if (file == NULL) {
        printf("未找到数据文件，已创建新文件。\n");
        return;
    }
    char line[256];
    while (fgets(line, sizeof(line), file)) {
        Product newProduct;
        int fieldsRead = sscanf(line, "%d %s %s %s %f %d", &newProduct.id, newProduct.name,
                                newProduct.category, newProduct.brand, &newProduct.price, &newProduct.quantity);

        // 检查数据完整性
        if (fieldsRead != 6 || !validateProduct(newProduct)) {
            printf("跳过格式错误或无效的数据行：%s", line);
            continue;
        }
        // 检查商品编号是否重复
        int duplicate = 0;
        for (int i = 0; i < productCount; i++) {
            if (products[i].id == newProduct.id) {
                printf("商品编号 %d 重复，跳过此行：%s", newProduct.id, line);
                duplicate = 1;
                break;
            }
        }
        if (!duplicate) {
            products[productCount++] = newProduct;
        }
    }
    fclose(file);
    printf("数据加载完成，共加载 %d 条商品记录。\n", productCount);
}
```

说明：  

+ 采用 `fgets()` 逐行读取数据文件；  
+ 使用 `sscanf()` 分析每行中各个字段的值；  
+ 对数据完整性与合法性进行验证；  
+ 对重复的商品编号进行检查，跳过重复数据。

#### 3.2.2 保存数据到文件
```c
void saveProductsToFile() {
    FILE *file = fopen("test.txt", "w");
    if (file == NULL) {
        printf("无法保存数据。\n");
        return;
    }
    for (int i = 0; i < productCount; i++) {
        fprintf(file, "%d %s %s %s %.2f %d\n", products[i].id, products[i].name, products[i].category,
                products[i].brand, products[i].price, products[i].quantity);
    }
    fclose(file);
    printf("数据保存成功！\n");
}
```

说明：  

+ 文件以写模式打开，遍历所有商品记录，用格式化输出写入文件。

---

### 3.3 商品操作模块
![](https://i-blog.csdnimg.cn/direct/8d63b786d6e64b04b1a1c66f36d7d013.png)

#### 3.3.1 批量录入初始商品信息
```c
void addInitialProducts() {
    int count;
    printf("请输入要录入的商品数量（不少于5）：");
    scanf("%d", &count);
    if (count < 5) {
        printf("录入数量不足5条。\n");
        return;
    }
    // 此处略 10行代码 .....
        int duplicate = 0;
        for (int j = 0; j < productCount; j++) {
            if (products[j].id == newProduct.id) {
                printf("商品编号 %d 已存在，跳过此商品。\n", newProduct.id);
                duplicate = 1;
                break;
            }
        }
        if (!duplicate) {
            products[productCount++] = newProduct;
        }
    }
}
```

说明：  

+ 要求用户录入不少于 5 条商品信息；  
+ 逐条录入时先读取各项数据，再进行数据合法性和查重处理；  
+ 如果数据不合法或编号重复，则循环重新输入。

#### 3.3.2 浏览全部商品信息
```c
void displayAllProducts() {
    if (productCount == 0) {
        printf("当前仓库无商品。\n");
        return;
    }
    printf("编号\t名称\t类别\t品牌\t单价\t库存\n");
    for (int i = 0; i < productCount; i++) {
        printf("%d\t%s\t%s\t%s\t%.2f\t%d\n", products[i].id, products[i].name,
               products[i].category, products[i].brand, products[i].price, products[i].quantity);
    }
}
```

说明：  

+ 如果当前商品数量为 0，则提示“当前仓库无商品”；  
+ 否则以表格形式输出所有商品信息。

#### 3.3.3 查询商品信息
```c
void queryProduct() {
    int option;
    printf("请选择查询条件：1.编号 2.名称 3.品牌 4.类别\n");
    scanf("%d", &option);

    char queryStr[NAME_LEN];
    int queryId;
    int found = 0;
    // 此处略 10行代码 .....
    }
    if (!found) {
        printf("未找到符合条件的商品信息。\n");
    }
}
```

说明：  

+ 用户可以根据编号、名称、品牌或类别进行查询；  
+ 根据用户选择，程序分别比较对应字段；  
+ 如果没有找到任何匹配记录，则提示“未找到符合条件的商品信息”。

#### 3.3.4 添加新商品
```c
void addProduct() {
    Product newProduct;
    while (1) {
        printf("请输入新增商品信息（编号 名称 类别 品牌 单价 库存）：\n");
        scanf("%d %s %s %s %f %d", &newProduct.id, newProduct.name, newProduct.category,
              newProduct.brand, &newProduct.price, &newProduct.quantity);

        // 查重
        // 此处略 10行代码 .....
    printf("商品添加成功！\n");
}
```

说明：  

+ 循环读取输入直到用户输入的商品编号不重复；  
+ 将新商品直接存入数组中，并更新商品数量。

#### 3.3.5 删除商品信息
![](https://i-blog.csdnimg.cn/direct/def74df72d604fc68ebc893ab506905a.png)

```c
void deleteProduct() {
    int id;
    printf("请输入要删除的商品编号：");
    scanf("%d", &id);

    for (int i = 0; i < productCount; i++) {
        if (products[i].id == id) {
            printf("删除商品：编号 %d 名称 %s\n", products[i].id, products[i].name);
            for (int j = i; j < productCount - 1; j++) {
                products[j] = products[j + 1];
            }
            productCount--;
            printf("商品删除成功！\n");
            return;
        }
    }
    printf("未找到指定编号的商品。\n");
}
```

说明：  

+ 根据用户输入的商品编号查找对应商品；  
+ 找到后，将该商品从数组中删除，并后移后续记录，最后减少 `productCount`。

### 3.3.6 修改商品信息
```c
void modifyProduct() {
    int id;
    printf("请输入要修改的商品编号：");
    scanf("%d", &id);

    for (int i = 0; i < productCount; i++) {
        // 此处略 10行代码 .....
            scanf("%d", &option);
            switch (option) {
                case 1:
                    printf("请输入新名称：");
                    scanf("%s", products[i].name);
                    break;
                case 2:
                    printf("请输入新类别：");
                    scanf("%s", products[i].category);
                    break;
                case 3:
                    printf("请输入新品牌：");
                    scanf("%s", products[i].brand);
                    break;
                case 4:
                    printf("请输入新单价：");
                    scanf("%f", &products[i].price);
                    break;
                case 5:
                    printf("请输入新库存：");
                    scanf("%d", &products[i].quantity);
                    break;
                default:
                    printf("无效选项。\n");
            }
            printf("商品信息修改成功！\n");
            return;
        }
    }
    printf("未找到指定编号的商品。\n");
}
```

说明：  

+ 用户输入目标商品编号，程序显示该商品所有信息；  
+ 用户选择要修改的项（除商品编号外的项），修改后提示“修改成功”。

### 3.3.7 按类别统计商品数量
```c
void categoryStatistics() {
    char category[CATEGORY_LEN];
    printf("请输入要统计的类别：");
    scanf("%s", category);

    int count = 0;
    for (int i = 0; i < productCount; i++) {
        if (strcmp(products[i].category, category) == 0) {
            count++;
        }
    }
    if (count > 0) {
        printf("类别 %s 的商品数量为 %d。\n", category, count);
    } else {
        printf("未找到该类别的商品信息。\n");
    }
}
```

说明：  

+ 用户输入需要统计的类别，遍历数组统计该类别的商品数量，最后输出统计结果或错误提示。

### 3.3.8 按单价排序商品
```c
void sortProducts() {
    int option;
    printf("请选择排序方式：1.升序 2.降序\n");
    scanf("%d", &option);

    for (int i = 0; i < productCount - 1; i++) {
        for (int j = 0; j < productCount - i - 1; j++) {
            // 此处略 10行代码 .....
                products[j + 1] = temp;
            }
        }
    }
    printf("排序完成，结果如下：\n");
    displayAllProducts();
}
```

说明：  

+ 用户选择升序或者降序排序；  
+ 采用冒泡排序算法，根据商品的单价进行排序；  
+ 排序完成后调用 `displayAllProducts()` 显示排序结果。

### 3.3.9 计算库存总价值
```c
void calculateTotalValue() {
    if (productCount == 0) {
        printf("当前仓库无商品，无法计算总价值。\n");
        return;
    }
    float totalValue = 0;
    for (int i = 0; i < productCount; i++) {
        totalValue += products[i].price * products[i].quantity;
    }
    printf("当前库存商品的总价值为：%.2f\n", totalValue);
}
```

说明：  

+ 遍历所有商品，将每个商品的单价与库存数量相乘，累加得到总价值；  
+ 如果没有商品则提示无法计算。

---

## 3.4 主菜单及主函数
### 3.4.1 菜单函数
```c
void menu() {
    int choice;
    do {
        printf("\n************文具店商品库存管理系统************\n");
        printf("\n--------------------------------------------\n");
        printf("---------1. 批量录入初始商品信息---------\n");
        printf("---------2. 浏览全部商品信息---------\n");
        printf("---------3. 查询商品信息---------\n");
        printf("---------4. 增加商品信息---------\n");
        printf("---------5. 删除商品信息---------\n");
        printf("---------6. 修改商品信息---------\n");
        printf("---------7. 按类别统计商品数量---------\n");
        printf("---------8. 按单价排序商品---------\n");
        printf("---------9. 计算库存总价值---------\n");
        printf("---------10. 退出---------\n");
        printf("请选择功能：");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addInitialProducts(); break;
            case 2: displayAllProducts(); break;
            case 3: queryProduct(); break;
            case 4: addProduct(); break;
            case 5: deleteProduct(); break;
            case 6: modifyProduct(); break;
            case 7: categoryStatistics(); break;
            case 8: sortProducts(); break;
            case 9: calculateTotalValue(); break;
            case 10: printf("系统已退出。\n"); break;
            default: printf("无效选择，请重新输入。\n");
        }
    } while (choice != 10);
}
```

说明：  

+ 使用 do...while 循环持续显示菜单；  
+ 根据用户输入的选项调用对应的操作函数；  
+ 当用户选择退出 (10) 时结束循环。

### 3.4.2 主函数
```c
int main() {
    loadProductsFromFile(); // 程序启动时加载文件数据
    menu();                 // 进入主菜单
    saveProductsToFile();   // 程序退出时保存数据
    return 0;
}
```

说明：  

+ 程序启动时先调用 `loadProductsFromFile()` 从数据文件加载商品记录；  
+ 进入菜单驱动的操作界面；  
+ 用户操作结束退出后，再调用 `saveProductsToFile()` 将最新数据保存至文件中。

通过模块化编程方式将文具店商品库存管理系统各项功能分解为独立函数，在满足业务需求的同时实现以下目标：

+ 数据输入、校验与持久化：通过文件读写与数据合法性检查确保数据可靠性；
+ 商品管理与查询：提供批量录入、浏览、查询、添加、删除、修改等全方位操作；
+ 数据统计与排序：支持按类别统计、按单价排序及计算库存总价值，有助于快速了解库存状况；
+ 用户交互：基于简单的文本菜单实现友好的用户操作体验。  
![](https://i-blog.csdnimg.cn/direct/e63cf707ac574e1691d131028e38f876.png)

---

# 三、结果
![](https://i-blog.csdnimg.cn/direct/b97c29403a3f477d94ca6642b89fb24f.png)

![](https://i-blog.csdnimg.cn/direct/a7b3c2a8952b40a0934a60147b0f1564.png)

![](https://i-blog.csdnimg.cn/direct/cb577158334e458489487f9785214190.png)  
![](https://i-blog.csdnimg.cn/direct/52283e103a854315ad4893fb8785be0e.png)  
![](https://i-blog.csdnimg.cn/direct/b5ff3f68fc16488ea473f1ad1ec68882.png)

![](https://i-blog.csdnimg.cn/direct/5841b91b66174a32ada9b0f83e935647.png)

![](https://i-blog.csdnimg.cn/direct/3b5e32d7135644f0acb469b0693383a9.png)  
![](https://i-blog.csdnimg.cn/direct/2c17a07c68104bf498df82bc9f78813c.png)

---![](https://i-blog.csdnimg.cn/direct/883609acdd654011933dfcb26e05bcfc.png)

