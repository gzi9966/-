---

> # 【Java】Java学生成绩管理系统（源码+论文）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
1. 用户友好的总界面：系统提供了一个用户友好的总界面，包括主菜单，方便用户了解和选择系统的各项功能。这个总界面是用户与系统交互的切入点，使用户可以轻松地访问各个功能。
2. 学生信息管理：系统使用了类和对象的概念来管理学生信息。每个学生对象包括学号、姓名以及四门课程的成绩（数学、英语、科学和历史）。这种对象化的设计使得系统可以高效地存储和操作学生信息，确保数据的一致性和完整性。
3. 添加学生：用户可以通过系统添加学生信息，包括学号和姓名。这个功能确保了学生信息的录入是方便的，同时也允许系统扩展学生数据。
4. 查询成绩：用户可以根据学生的学号查询其成绩信息。这个功能提供了对学生成绩的快速访问，帮助用户了解学生的表现。
5. 修改成绩：用户可以根据学生的学号修改其数学、英语、科学和历史成绩。这个功能允许用户更新学生成绩，确保成绩信息的准确性。
6. 删除学生：用户可以根据学生的学号删除学生信息。这个功能使得系统能够维护学生信息的完整性，同时也提供了一种学生退出或其他原因需要删除学生信息的方式。

---

# 二、设计思路
1. Student 类: 这个类用于表示学生对象。它包含学生的姓名和成绩属性，以及相应的 getter 和 setter 方法，以允许访问和修改这些属性。此类还重写了 toString() 方法，以便更容易地在用户界面中显示学生信息。
2. GradeBook 类: GradeBook 类用于管理学生对象的列表。它提供了一系列方法来执行以下操作：  
addStudent(): 用于将新的学生对象添加到列表中。  
findStudent(): 根据学生姓名查找学生对象。  
deleteStudent(): 根据学生姓名删除学生对象。  
modifyStudentScore(): 根据学生姓名修改学生的成绩。  
displayAllStudents(): 用于显示所有学生的成绩信息。  
sortStudentsByScoreAscending(): 将学生按成绩升序排序。  
sortStudentsByScoreDescending(): 将学生按成绩降序排序。

```java

class Student {
   private String name;
   private double score;

   public Student(String name, double score) {
       this.name = name;
       this.score = score;
   }

   public String getName() {
       return name;
   }

   public double getScore() {
       return score;
   }

   public void setScore(double score) {
       this.score = score;
   }

   @Override
   public String toString() {
       return "Name: " + name + ", Score: " + score;
   }
}
```

3. GradeManagementSystem 类: 这是主程序入口。它包含一个主循环，用于显示用户界面、接受用户输入，并根据用户选择调用 GradeBook 类的方法执行相应的操作。主要功能包括添加、查询、删除、修改、显示和排序成绩，以及退出系统。



![](https://i-blog.csdnimg.cn/direct/ea5339066cf7405da924cca1e6e0f17e.png)



 数据流程

1. 用户通过主程序 GradeManagementSystem 输入命令和操作，选择要执行的操作。

```java
System.out.println("成绩管理系统");
System.out.println("1. 添加成绩");
System.out.println("2. 查询成绩");
System.out.println("3. 删除成绩");
System.out.println("4. 修改成绩");
System.out.println("5. 显示所有成绩");
System.out.println("6. 按成绩升序排序");
System.out.println("7. 按成绩降序排序");
System.out.println("8. 退出系统");
System.out.print("请选择操作（1-8）：");
```

2. 主程序根据用户选择，调用 GradeBook 类的相应方法来执行操作。例如，当用户选择添加学生成绩时，主程序将调用 addStudent() 方法。
3. GradeBook 类执行相应的操作，操作学生对象的列表。例如，当添加学生成绩时，它将创建一个新的 Student 对象并将其添加到列表中。
4. 用户界面模块负责向用户显示结果，包括成功消息、查询结果、错误消息等。
5. 用户可以在主菜单中选择不同的操作，或选择退出系统以结束程序。

> **代码量较大，此处不一 一 展示**
>



## 用户界面
用户界面是与用户交互的关键组件。它在控制台上提供了一个简单的文本界面，用户可以使用键盘输入命令并查看操作结果。用户界面包括以下元素：  
主菜单：显示可用的操作选项，以数字标识每个选项。



![](https://i-blog.csdnimg.cn/direct/48bd656715c449868bc9697084c20334.png)

输入界面：用于接收用户输入，例如学生姓名、成绩等。  
![](https://i-blog.csdnimg.cn/direct/59e45379e06d4fcaa5d68e22f1eb5841.png)  
![](https://i-blog.csdnimg.cn/direct/da5c3f1721224cefa5680632b6c3d5c2.png)  
![](https://i-blog.csdnimg.cn/direct/59cd9c41bef447d48cc3066e0695f8b5.png)

结果显示界面：用于显示操作结果、查询结果、成功消息和错误提示。

![](https://i-blog.csdnimg.cn/direct/958f43bea716437c883b193b080a8760.png)

![](https://i-blog.csdnimg.cn/direct/4c6d747dc89c47d7b1c626e721104e36.png)  
![](https://i-blog.csdnimg.cn/direct/7e398274d27f4452a6b5ad5116f9491f.png)  
![](https://i-blog.csdnimg.cn/direct/d55e274f63c64a10bdf100aff3c812e0.png)



错误处理：用户界面模块负责处理输入错误和异常情况，并向用户提供友好的错误消息和建议。  
![](https://i-blog.csdnimg.cn/direct/0c1b0629f3b643208bcfcc061aa86b19.png)

![](https://i-blog.csdnimg.cn/direct/c252518f1c074586827f28efc8a765ac.png)

![](https://i-blog.csdnimg.cn/direct/0f91aab71b5840d7b65a25b460f33160.png)



---

