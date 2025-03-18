# <font style="color:rgb(64,64,64);">功能要求</font>
>

**<font style="color:rgb(64,64,64);">办公用品管理系统的主题围绕资源的高效管理与流程的自动化展开</font>**

<font style="color:rgb(64,64,64);">根据上述功能模块，系统大致需要</font><font style="color:rgb(64,64,64);"> </font>**<font style="color:rgb(64,64,64);">15-20 </font>****<font style="color:rgb(64,64,64);">个页面</font>**<font style="color:rgb(64,64,64);">，具体如下：</font>

<font style="color:rgb(64,64,64);">1.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">登录页面</font>

<font style="color:rgb(64,64,64);">2.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">注册页面</font>

<font style="color:rgb(64,64,64);">3.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">用户列表页面</font>

<font style="color:rgb(64,64,64);">4.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">个人信息页面</font>

<font style="color:rgb(64,64,64);">5.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">角色列表页面</font>

<font style="color:rgb(64,64,64);">6.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">权限分配页面</font>

<font style="color:rgb(64,64,64);">7.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">库存列表页面</font>

<font style="color:rgb(64,64,64);">8.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">入库页面</font>

<font style="color:rgb(64,64,64);">9.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">出库页面</font>

<font style="color:rgb(64,64,64);">10.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">库存预警页面</font>

<font style="color:rgb(64,64,64);">11.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">申领页面</font>

<font style="color:rgb(64,64,64);">12.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">审批页面</font>

<font style="color:rgb(64,64,64);">13.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">申领记录页面</font>

<font style="color:rgb(64,64,64);">14.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">采购计划页面</font>

<font style="color:rgb(64,64,64);">15.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">采购订单页面</font>

<font style="color:rgb(64,64,64);">16.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">库存消耗报表页面</font>

<font style="color:rgb(64,64,64);">17.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">采购成本分析页面</font>

<font style="color:rgb(64,64,64);">18.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">用品使用趋势页面</font>

<font style="color:rgb(64,64,64);">19.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">菜单管理页面</font>

<font style="color:rgb(64,64,64);">20.</font><font style="color:rgb(64,64,64);"> </font><font style="color:rgb(64,64,64);">日志管理页面</font>

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966374231-4605861b-2e41-467c-9275-a4b7fa02fded.png)

# 部分代码块
**ApplyForItem.vue**

该组件展示了一个申领页面，主要用于用户提交办公用品的申领申请。页面包含侧边栏导航和主体内容区域，在主体区域内有一个表单，用户可以填写物品名称、申领数量和申领理由。提交表单时调用 `handleApply` 方法，打印申请信息并显示提示消息。



**ApplyRecords.vue**

该组件展示用户的申领记录。页面同样包含侧边栏导航和主体内容区域，主体区域利用表格展示招领记录信息（如申请人、物品名称、数量、日期及状态），数据通过模拟数据进行展示，可扩展为调用后端接口获取真实数据。



**Approval.vue**

该组件用于审批页面，展示待审批记录，每条记录显示申请人、物品名称、数量及理由，并提供“同意”和“拒绝”按钮。点击按钮时将触发相应的审批操作（这里只做了模拟处理，可扩展为调用接口）。



**Dashboard.vue**

该组件作为系统首页（Dashboard），主要展示办公用品管理系统的总体概览。包含侧边栏导航和主要内容区域，展示欢迎信息或更多统计数据。



**InventoryConsumptionReport.vue**

该组件用于展示库存消耗报表。页面包含侧边栏和主体区域，在主体区域中通过表格显示各个物品的初始库存、剩余库存、消耗数量以及统计周期，示例数据为模拟数据，可替换为实际接口数据。



**InventoryList.vue**

该组件展示库存列表数据。页面使用侧边栏导航和主体区域，在表格中展示每个库存项的物品名称、数量和单位。数据为模拟数据，实际应用中可以通过调用接口获取库存信息。



**InventoryWarning.vue**

该组件用于库存预警。当系统中某个物品的库存数量低于预先设定的预警阈值时，将此物品筛选出来，并在页面上以表格的方式展示；如果当前所有库存都充足，则显示提示信息。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966376841-654f5707-4920-4a79-ac42-d8e350147164.png)

**Login.vue**

该组件实现用户登录功能。页面包含用户名和密码的输入框、登录按钮以及注册跳转链接。用户输入信息后，利用 axios 调用后端接口进行登录，登录失败时展示错误提示信息。



**LogManagement.vue**

该组件用于日志管理，展示了系统中操作日志的列表。日志信息包括ID、操作时间、操作用户和具体操作内容，数据为模拟数据，可根据实际需求对接后端接口。



**MenuManagement.vue**

该组件实现菜单管理功能，通过展示菜单列表，提供编辑和删除功能。页面采用侧边栏导航和主体内容区域，列表中通过表格展示菜单的 ID、名称、URL、父级菜单等信息，后续可添加具体的增删改查操作。



其他代码文件讲解略..



涵盖了申领申请、记录展示、审批管理、库存统计、库存预警、登录、日志以及菜单管理等功能。每个组件都应用了侧边栏导航与主体内容布局，均可根据实际需求进一步修改和完善。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966379104-1266b43a-21cd-4c16-96cb-bf1dc8c12233.png)

# 运行截图
部分页面展示如下：

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961372059-3bc34f51-b28e-4735-ae51-63c3fafb96cf.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961372067-544a461a-d9e6-49a7-ab72-da5432fd84f8.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371383-afb8cece-3e04-425c-a3e8-74c1a3384947.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371472-6b70b7b4-f12e-450e-b564-5f3f785d8c6c.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966383551-32ce0b27-440f-43b7-ba68-07fa64aa9e49.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371408-3c2877d5-aa47-4d1a-8e0f-fed852506d4b.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371389-dd689bb0-f135-4a69-aa09-9ff51a5ee293.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371403-64c24bdb-94ac-4e63-a61e-6dafdb1e8f68.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371650-55025be4-a0ac-4e20-a494-d5467cb6e42a.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371627-70012de0-6464-4132-b667-ad7fadb1c819.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966386934-7798fabf-53c6-4af8-bd9a-e1a321a44444.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371643-d8e33006-6a42-4834-a691-030d891dc2cf.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371656-8b9dc1b8-3a5a-4a4f-bcea-7bce839f81d5.png)![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741961371682-75a2e21b-30b7-44ae-b6c0-2f044c91314a.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966389421-2451c71e-9b36-4131-82be-732f7118e364.png)

# 运行方法
## 后端运行方法
1. **创建后端项目目录并初始化工程**  
打开命令行，执行以下命令，新建一个后端项目目录并初始化 Node.js 工程：

```bash
cd backend
npm init -y
```

2. **安装依赖**  
在后端项目目录下安装所需依赖（Express、mysql2、cors、bcrypt）：

```bash
npm install express mysql2 cors bcrypt
```

3. **创建数据库和数据表**  
在你的 MySQL 数据库中执行如下 SQL 语句，创建数据库（如 office_supply）以及 `users` 表：

```sql
CREATE DATABASE IF NOT EXISTS office_supply;
USE office_supply;

CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

4. **编写并启动后端代码**  
在项目中创建一个 `server.js` 文件，编写后端代码（包括数据库连接、接口路由等），之后在命令行中运行：

```bash
node server.js
```

如果后端代码中输出“服务已启动，端口：3000”以及“MySQL 连接成功”，说明后端服务启动成功。



## 前端运行方法
1. **进入项目目录并安装依赖**  
进入项目目录后安装项目依赖，再安装 axios 和 vue-router（版本4）：

```bash
cd office-supply-frontend
npm install
npm install axios vue-router@4
```

2. **配置前端路由和其他组件**  
根据项目需求创建并配置 Vue 单文件组件、路由（例如在 `src/router/index.js` 中配置各页面路径）以及调用后端 API（例如使用 axios）。
3. **启动前端开发服务器**  
确认所有代码无误后，在项目根目录下运行：

```bash
npm run dev
```

此命令会启动 Vite 开发服务器，并在命令行中显示访问的本地地址（如 `http://localhost:5173`）。在浏览器中通过具体路径（例如 `/login`、`/register` 等）即可访问相应页面。

## 整体运行流程
1. **启动后端服务**  
在 backend 目录下运行 `node server.js`，确保后端服务成功连接数据库并监听 3000 端口。
2. **启动前端服务**  
在 office-supply-frontend 目录下运行 `npm run dev`，启动前端开发服务器，打开浏览器访问对应页面（例如 `http://localhost:5173/login` 或 `http://localhost:5173/dashboard`）。
3. **前后端交互**  
前端页面通过 axios 调用后端接口（例如 `http://localhost:3000/api/login`、`http://localhost:3000/api/register` 等）实现数据的交互和业务逻辑。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741966397772-28fcaae9-458b-4e42-8676-2c649cd078e5.png)

