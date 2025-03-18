

# 1. 页面分析
# 1. 具体要求
使用面相对象设计方法设计一个仓储管理系统。

**操作的核心实体有**:

+ 货物包含(id、货物名称、货物种类、长度、宽度...)
+ 仓库 (id 、名称、长度、宽度、类别...)
+ 用户 (id、名称、类别、用户名、密码...)
+ 用户分为管理员、入库员、出库员、业务员。

 **对货物的操作管理包含**:

+ 新建货物
+ 入库 (货物需要根据自己的种类入库到对应类别的仓库，如食品如食品库仓库可以预先程序数据设置好)
+ 出库
+ 查询货信息
+ 查询出入库记录
+ 修改货物信息。

**登录功能**：  
系统运行后需要需要按照用户名密码来登录，管理员有最高权限，他可以给指定用户赋予特定的权限，如给入库员入库、查询的操作权限、给出库员出库、查询的权限、业务员查看的权限。

**菜单**：  
设计出便于用户操作的交互菜单例如下面示例(根据自己的需求进行修改)欢迎使用仓库管理系统，请选择您要进入的功能  
1、新增仓储货物信息  
2、货物入库3、货物出库  
4、修改货物信息  
5、查询库存  
6、查询出入库记录  
7、退出

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033756427-3d7f9340-ee05-4d16-a7ce-326be4a64824.png)

# 代码实现
本系统中有4种角色，`管理员、入库员、出库员、业务员`，分别对应不同的操作权限，代码如下：



```python
    # ......略.....

    # 初始化用户
    users = [
        User(id=1, name="张三", category="管理员", username="admin", password="admin123"),
        User(id=2, name="李四", category="入库员", username="stocker", password="stocker123"),
        User(id=3, name="王五", category="出库员", username="dispatcher", password="dispatcher123"),
        User(id=4, name="赵六", category="业务员", username="sales", password="sales123")
    ]
    for user in users:
        system.add_user(user)

    # 初始化货物
    goods = [
        Goods(id=1, name="苹果", category="食品", length=10, width=10),
        Goods(id=2, name="椅子", category="家具", length=50, width=50)
    ]
    for item in goods:
        system.add_goods(item)

    # 初始化仓库
    warehouses = [
        Warehouse(id=1, name="食品仓库", length=100, width=100, category="食品"),
        Warehouse(id=2, name="家具仓库", length=200, width=200, category="家具")
    ]
    for warehouse in warehouses:
        system.add_warehouse(warehouse)

    while True:
        print("欢迎使用仓库管理系统，请选择您要进入的功能:")
        print("========================================")
        print("1、用户登录")
        print("2、新增仓储货物信息")
        print("3、货物入库")
        print("4、货物出库")
        print("5、修改货物信息")
        print("6、查询库存")
        print("7、查看仓库状态")
        print("8、退出")
        print("========================================")

        choice = input("请选择: ")
        if choice == '1':
            username = input("请输入用户名: ")
            password = input("请输入密码: ")
            if system.login(username, password):
                print("登录成功！")
            else:
                print("登录失败，用户名或密码错误。")

        elif choice == '2':
            if not system.logged_in_user or system.logged_in_user.category != "管理员":
                print("只有管理员才能添加货物。")
                continue
                
       # ......略.....
       
```

---

# 代码截图
![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033763688-60bd0d9e-d6cf-43dc-adfb-fe93ed3ef7aa.png)

![](https://i-blog.csdnimg.cn/blog_migrate/d543ef1195883daeff8f393eb2ca1dd0.png)

## 【管理员】模式登录
![](https://i-blog.csdnimg.cn/blog_migrate/f0b8fab00cf48c0ba6d3ceb52e336b50.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033766812-26d7b0ca-6e96-4305-84d5-fc2d96a9bb1c.png)

## 【管理员】查看库存
![](https://i-blog.csdnimg.cn/blog_migrate/158d0161d89b7fef2ce1b68f097c4de8.png)

## 【管理员】新增仓储货物信息
![](https://i-blog.csdnimg.cn/blog_migrate/2edeccecd17b89b6f0fe798d65ca3086.png)

## 【入库员】模式登录
![](https://i-blog.csdnimg.cn/blog_migrate/70e1f76c134e83dab101155daf82e026.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033770703-9e8cdd13-5eff-43c0-8995-01f360134f6a.png)

## 【入库员】修改修改货物信息
无法修改

![](https://i-blog.csdnimg.cn/blog_migrate/bac02ec457fe07d85087e2e8024d2976.png)

## 【出库员】出库货物
![](https://i-blog.csdnimg.cn/blog_migrate/c67ab9831ed5f9f1a7693d9a6c8054a3.png)



## 【出库员】入库货物
无法入库

![](https://i-blog.csdnimg.cn/blog_migrate/2ce10504a096447ff7c6683fb36812ce.png)



**其他截图略...**.

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033775653-6d5be498-fafc-426d-a593-2582417df95c.png)

