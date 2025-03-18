---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
# 
### 项目简介
本项目是设计一个前后端分离的web应用程序，前端采用Vue框架，后端采用Spring Boot框架、数据库采用关系型数据库。





### 项目启动
使用idea打开BackEnd和FrontEnd，后端需要jdk1.8，前端需要Vue脚手架和node.js才可运行

### 开发环境
| 名称 | 早期开发使用的版本 | 后续更新使用的版本 |
| --- | --- | --- |
| 操作系统 | macOS Monterey 12.3 | Windows11_22H2_22621.963 |
| 前端框架 | Vue 2.6.14 |  |
| 前端脚手架 | @vue/cli 4.5.15 | @vue/cli 5.0.8 |
| JavaScript运行环境 | Node.js 16.14.0 | Node.js 18.12.1 |
| 包管理工具 | npm 8.5.3 | npm 9.2.0 |
| 后端框架 | Spring Boot 2.6.3 | Spring Boot 2.7.6 |
| Java SE 开发工具包 | jkd-8u321(1.8) | corretto-jdk1.8.0_342 |
| 数据库 | MariaDB 10.6.4 |  |
| 开发软件 | IntelliJ IDEA 2021.3.3 | IntelliJ IDEA 2022.3.2 |
| 数据库管理工具 | Navicat Premium 16.0.8 | Navicat Premium 16.1.6 |
| 浏览器 | Google Chrome 100.0.4896.60 | Google Chrome 109.0.5414.120 |


## 项目功能
+ 登录、退出、注册
+ 消息查看、消息删除
+ 报名比赛
+ 消息发布
+ 评审比赛
+ 用户信息修改
+ 搜索比赛
+ 查看评审结果、比赛排名



## 项目所用技术
+ Json文件读写
+ 平均分配算法（自行设计，目前能用，比较简单，后续更新）
+ JWT
+ MyBatis-plus和MyBatis-plus-join链表查询插件
+ alibaba druid连接池
+ Spring Security登录鉴权
+ element-ui
+ axios
+ vuex
+ vue-router







测试账号：

| 用户组 | 账号（密码：123456） | 姓名 |
| :---: | :---: | :---: |
| 管理员 | admin（密码：admin） | 管理员 |
| 计算机设计（老师） | 13886961359 | 张瓜皮皮 |
| 计算机设计（老师） | 15023476163 | 球球慧子 |
| 计算机设计（老师） | 18453887612 | 小江云子 |
| 网页设计（老师） | 13866039800 | 德隆东墙 |
| 网页设计（老师） | 13027048577 | 五号六号 |
| 算法设计（老师） | 15949101110 | 舒服阿寿 |
| 算法设计（老师） | 15885569223 | 公叔幼安 |


![](https://i-blog.csdnimg.cn/direct/4018a33fd3aa4a0aa0b4b16cb7b25cda.png)



---

# 二、设计思路
Login.vue是登录页面，该页面需要用户输入账号（手机号或邮箱）、密码和验证码，前端将填写的信息发送到后端，后端将数据与数据库进行匹配，如果匹配成功，成功则返回一个token给前端（程晓旭，2002）。前端再将token传给后端，后端解密后将用户数据传给前端，并存储在Vuex里面，最后跳转到首页。登录功能流程如图3-13所示。  
![](https://i-blog.csdnimg.cn/direct/967a3d231ff34b96a98eb69d7c08a6bd.png)  
![](https://i-blog.csdnimg.cn/direct/d831fe81a6d44260a502d2a2d7397226.png)

Register.vue是注册页面，该页面分为三步，各个步骤的详细情况如下：  
（1）注册第一步  
第一步需要输入姓名、手机号、邮箱、密码（需要满足6-16位，且至少包含一位数字和字母），点击下一步后，前端将手机号和邮箱发送到后端，后端在数据库查询手机号和邮箱是否存在，如果存在则提示并清除对应输入框的内容；   
（2）注册第二步  
第二步需要输入学校、地址、选择性别和生日；   
（3）注册第三步  
第三步选择身份，身份只能是学生或者老师，选择学生则直接显示出提交按钮。选择老师会弹出加入组或是创建组，选择加入组会弹出一个窗口，窗口提示输入一个五位数的编码，将编码传入后端匹配数据库，匹配成功即加入成功，匹配失败会提示并弹出创建组的窗口；选择创建组会提示输入组名称和组编码，前端验证完成后，由后端将数据存入数据库。最后前端关闭所有弹出窗口，将隐藏的提交按钮显示出来，用户点击提交，后端校验全部数据，返回注册成功与否的结果。  
注册模块流程如图3-14所示。  
![](https://i-blog.csdnimg.cn/direct/6efcc99c98164295bcaa394cd0d40a30.png)  
消息功能属于UserInfo.vue文件其中的一部分。该功能需要登录后才可使用，右上角的消息角标展示有多少条未读消息，点击后打开消息“抽屉”，展示所有消息的标题、发送时间、发布人和已读未读状态。点击消息列表中的一个，程序将跳转至内容页面，将详细内容、发布人、发布时间展示到页面上；点击删除已读可删除所有已读的消息。消息块流程如图3-15所示。  
在消息模块中存在查看详情、删除选中和删除已读这三个子功能。  
（1）查看消息详情  
    查看消息需要点击消息列表中的任意一条消息，前端将消息id传入后端，后端将查询到的结果返回前端，由前端跳转到详情页面，并将数据展示；  
（2）删除选中消息  
该功能首先需要点击左侧多选框进行选择，然后点击删除选中消息按钮，前端将选中的消息id发送给后端，由后端将这些消息从数据库中删除，并返回删除成功或失败的结果，前端展示出来；  
（3）删除已读消息  
点击删除已读消息按钮后，由后端将该用户所有已读的消息进行删除，并返回删除成功或失败；  
![](https://i-blog.csdnimg.cn/direct/9ee87457daa7493cbd3176fc0ed9d5ff.png)  
![](https://i-blog.csdnimg.cn/direct/be29936ac27d4e04a60bede89982fd08.png)

![](https://i-blog.csdnimg.cn/direct/b2a0cb2981144af78b3c5260c576f2c3.png)  
    Header/index.vue文件其中一部分是搜索功能，而搜索出来的结果将由Search.vue页面展示出来。该功能需要在顶部的搜索框输入搜索内容，前端将数据发送给后端，后端通过查询数据获取查询结果，由前端展示。搜索模块流程如图3-16所示。  
    ![](https://i-blog.csdnimg.cn/direct/f957b64dd10441499e3e20df4778ae98.png)

Manage/index.vue是管理比赛功能的主页面。该功能需要老师用户登录后才可使用，页面拥有添加比赛子功能；展示用户所属组的全部比赛，并且每一行根据数据不同属性拥有不同子的功能：发送消息、修改比赛、删除比赛和评审比赛。另外该页面还有发送消息的子功能，需要输入标题和内容后发送，接收消息的对象是该比赛的所有报名者。管理比赛模块流程如图3-17所示。  
![](https://i-blog.csdnimg.cn/direct/1001c8763e694f8eae7bae61ce598ee9.png)

![](https://i-blog.csdnimg.cn/direct/5b2c6f4534fb47b3aaa0593dc9f2f52a.png)  
![](https://i-blog.csdnimg.cn/direct/3811a399209640049db6665b9b6bb908.png)  
![](https://i-blog.csdnimg.cn/direct/29b96a4e2a2e4d1d9094010bd3b2a474.png)

![](https://i-blog.csdnimg.cn/direct/155387edd2bc4cdb8d489a908128227b.png)

![](https://i-blog.csdnimg.cn/direct/31d1c0874f84421aaffc897008292db3.png)



![](https://i-blog.csdnimg.cn/direct/2de2c2de7a8d4bee9198d389b3061115.png)  
![](https://i-blog.csdnimg.cn/direct/251ea3ba3c2c463082a0a7ad84a9a5dd.png)



![](https://i-blog.csdnimg.cn/direct/b03bf59707f34911aeae8cd959b2b6f7.png)

![](https://i-blog.csdnimg.cn/direct/13491bae88554bf2b4ae0caa298ce29f.png)



![](https://i-blog.csdnimg.cn/direct/f2b29ce498b24d0abcc9eb21e9864e67.png)





---

