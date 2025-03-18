---

> # 【Java】Java银行信息管理系统（源码+报告）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
设计总结中，将详细讨论在Eclipse中创建一个银行信息管理系统的具体步骤。该系统使用Java编程语言，通过面向对象编程（OOP）技术实现基本的增删改查功能。以下是详细步骤和代码分析。

使用工具：  
编程语言：Java  
开发环境：Eclipse IDE  
创建项目步骤：  
下载并安装Eclipse IDE：从Eclipse官方网站下载并安装最新版本的Eclipse IDE。

![](https://i-blog.csdnimg.cn/direct/71a8327b59b3404490fa5ec8fe138e09.png)



---

# 二、设计思路
## 1. 增加客户
创建一个新的BankCustomer对象，包含客户的姓名、账号和余额信息。  
将这个新创建的客户对象添加到系统管理的客户列表中。

```java
public void addCustomer(BankCustomer customer) {
    customers.add(customer);
    System.out.println("Customer added successfully.");
}
```

方法addCustomer接收一个BankCustomer对象作为参数。  
使用ArrayList的add方法将客户添加到customers列表中。  
打印一条成功添加的消息。



![](https://i-blog.csdnimg.cn/direct/9785935d98cc410a861f0db49292f3fc.png)



## 2. 删除客户
根据客户的账号找到相应的客户对象。如果找到该客户，则从列表中移除该客户对象。如果未找到，则输出提示信息。

```java
public void deleteCustomer(String accountNumber) {
    for (BankCustomer customer : customers) {
        if (customer.getAccountNumber().equals(accountNumber)) {
        // 略....
            System.out.println("Customer deleted successfully.");
            return;
        }
    }
    System.out.println("Customer not found.");
}
```

方法deleteCustomer接收一个字符串参数accountNumber。  
使用for-each循环遍历customers列表，查找匹配的账号。  
如果找到匹配的客户，调用remove方法将其从列表中移除，并打印成功删除的消息。  
如果循环结束未找到匹配的客户，则打印客户未找到的消息。

![](https://i-blog.csdnimg.cn/direct/a6a480f0761d471a8718f77c380a0a6a.png)



## 3. 修改客户信息
根据客户的账号找到相应的客户对象。如果找到该客户，则更新客户的姓名和余额信息。如果未找到，则输出提示信息。

```java
public void updateCustomer(String accountNumber, String newName, double newBalance) {
    for (BankCustomer customer : customers) {
        if (customer.getAccountNumber().equals(accountNumber)) {
        // 略....
            System.out.println("Customer updated successfully.");
            return;
        }
    }
    System.out.println("Customer not found.");
}
```

方法updateCustomer接收三个参数：客户账号accountNumber，新的客户姓名newName，新的余额newBalance。使用for-each循环遍历customers列表，查找匹配的账号。如果找到匹配的客户，调用setName和setBalance方法更新客户信息，并打印成功更新的消息。  
如果循环结束未找到匹配的客户，则打印客户未找到的消息。



![](https://i-blog.csdnimg.cn/direct/a06a31bbfa0e4a76b5f4a35563dea6c4.png)



## 4. 查询客户信息
根据客户的账号找到相应的客户对象。如果找到该客户，则返回客户对象。如果未找到，则输出提示信息。

```java
public BankCustomer searchCustomer(String accountNumber) {
    for (BankCustomer customer : customers) {
        if (customer.getAccountNumber().equals(accountNumber)) {
            return customer;
        }
    }
    System.out.println("Customer not found.");
        // 略....
    return null;
}
```

方法searchCustomer接收一个字符串参数accountNumber。使用for-each循环遍历customers列表，查找匹配的账号。如果找到匹配的客户，返回该客户对象。如果循环结束未找到匹配的客户，打印客户未找到的消息并返回null。



![](https://i-blog.csdnimg.cn/direct/c73a683a047649d0b00e51e5cd40114a.png)





## 5. 展示所有客户信息
遍历客户列表，打印每个客户的详细信息。

```java
public void displayAllCustomers() {
    for (BankCustomer customer : customers) {
        System.out.println(customer);
        // 略....
    }
}
```

方法displayAllCustomers没有参数。使用for-each循环遍历customers列表。对于每个客户对象，调用其toString方法并打印结果。

![](https://i-blog.csdnimg.cn/direct/4e411cf076fb432e90f29a789fb93d81.png)



## 主类 Main
在主类Main中，我们实现了一个命令行界面，允许用户与银行管理系统进行交互：

显示菜单选项，用户可以选择执行不同的操作（增加客户、删除客户、修改客户信息、查询客户信息、展示所有客户信息、退出系统）。  
根据用户的选择，调用相应的方法处理客户信息。  
使用Scanner类获取用户输入。

![](https://i-blog.csdnimg.cn/direct/66ac636aa5c6412dbb8b09f66a1655cd.png)

