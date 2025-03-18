---

> # 【Java】Java学生信息管理系统（源码）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
本系统旨在提供一个学生成绩管理系统，允许用户通过控制台菜单执行各种操作，包括添加、删除、修改和查询学生信息，按科目或总成绩排序学生，统计课程成绩的最高分和最低分，以及计算某班级某课程的优秀率和不及格率。



---

# 二、设计思路
#### 类设计
![](https://i-blog.csdnimg.cn/direct/c48b761c68ec4f9fa317a68bbc2b6f3c.png)

1. **StudentManagementSystem 类**
    - 负责管理学生信息，包含学生数据的加载、保存、添加、删除、修改、查询和统计等功能。
2. **Student 类**
    - 代表学生对象，包含学生的基本信息（学号、姓名、班级、语文成绩、数学成绩和英语成绩），以及计算总成绩和平均成绩的方法。

#### 主要方法和功能
1. **StudentManagementSystem 类**
    - `StudentManagementSystem()`: 构造函数，初始化学生列表并从文件加载学生信息。
    - `loadStudentsFromFile(String filename)`: 从指定文件加载学生信息并存入学生列表。
    - `saveStudentsToFile(String filename)`: 将学生列表中的信息保存到指定文件。
    - `addStudent(Student student)`: 添加新的学生信息到学生列表并保存到文件。
    - `deleteStudent(String studentId)`: 根据学生学号删除学生信息并保存到文件。
    - `updateStudent(Student updatedStudent)`: 根据学生学号更新学生信息并保存到文件。
    - `searchStudents(String keyword)`: 根据关键字（姓名、学号或班级）查询学生信息。
    - `sortStudentsBySubject(String subject)`: 根据指定科目（语文、数学或英语）的成绩对学生进行排序。
    - `sortStudentsByTotalScore()`: 根据学生的总成绩对学生进行排序。
    - `sortStudentsByAverageScore()`: 根据学生的平均成绩对学生进行排序。
    - `printCourseStatistics(String course)`: 打印指定课程的最高分和最低分的学生信息。
    - `printClassCourseRates(String className, String course)`: 打印指定班级在某课程的优秀率和不及格率。



![](https://i-blog.csdnimg.cn/direct/f4a68ab7f5274302b6c410880c3d05d8.png)

2. **Student 类**
    - `Student(String id, String name, String className, int chineseScore, int mathScore, int englishScore)`: 构造函数，初始化学生信息。
    - `getId()`, `getName()`, `getClassName()`, `getChineseScore()`, `getMathScore()`, `getEnglishScore()`, `getTotalScore()`, `getAverageScore()`: 各种 getter 方法，用于获取学生的信息。
    - `toString()`: 将学生信息转换为字符串格式，便于存储和输出。

#### 控制台菜单
+ 主方法 `main(String[] args)`:
    - 提供一个控制台菜单，用户可以选择不同的操作（添加、删除、修改、查询、排序和统计）。
    - 使用 `Scanner` 类读取用户输入，根据输入调用相应的方法执行操作。
    - 使用 `while (true)` 循环保持菜单运行，直到用户选择退出。



![](https://i-blog.csdnimg.cn/direct/2b09a673ae884490a6366da662439c57.png)

#### 文件操作
+ 学生信息存储在一个文本文件 `students.txt` 中，每行代表一个学生，信息以逗号分隔。
+ `loadStudentsFromFile` 和 `saveStudentsToFile` 方法分别负责从文件加载学生信息和将学生信息保存到文件。



![](https://i-blog.csdnimg.cn/direct/ed3aa03767a84b67b5c8b08a62a13a74.png)

### 代码块作用分析
1. **StudentManagementSystem 构造函数**
    - 初始化学生列表，并调用 `loadStudentsFromFile` 方法从文件加载学生信息。
2. **loadStudentsFromFile 方法**



    - 从指定的文本文件读取学生信息，将其转换为 `Student` 对象并添加到学生列表中。

```java
    // 从文件加载学生信息
    private void loadStudentsFromFile(String filename) {
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(",");
                students.add(new Student(parts[0], parts[1], parts[2], Integer.parseInt(parts[3]), Integer.parseInt(parts[4]), Integer.parseInt(parts[5])));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

3. **saveStudentsToFile 方法**
    - 将学生列表中的每个 `Student` 对象转换为字符串格式，并写入到指定的文本文件中。
4. **addStudent 方法**
    - 添加新的学生信息到学生列表中，并保存更新后的列表到文件。

```java
    // 添加学生信息
    public void addStudent(Student student) {
        students.add(student);
        saveStudentsToFile("students.txt");
    }

```



![](https://i-blog.csdnimg.cn/direct/7f6db29f0ea041f2b5fd629cc8a7011e.png)



5. **deleteStudent 方法**
    - 根据学生学号从学生列表中删除相应的学生，并保存更新后的列表到文件。

![](https://i-blog.csdnimg.cn/direct/7c3ff2282d6f418894d9d6cbf6d5808c.png)

6. **updateStudent 方法**
    - 根据学生学号更新学生信息，并保存更新后的列表到文件。  
![](https://i-blog.csdnimg.cn/direct/95762b51cfd842448e8d99633deff8cc.png)
7. **searchStudents 方法**
    - 根据关键字在学生列表中查询匹配的学生信息，并返回查询结果。

```java
    // 修改学生信息
    public void updateStudent(Student updatedStudent) {
        for (int i = 0; i < students.size(); i++) {
            if (students.get(i).getId().equals(updatedStudent.getId())) {
                students.set(i, updatedStudent);
                break;
            }
        }
        saveStudentsToFile("students.txt");
    }
```

![](https://i-blog.csdnimg.cn/direct/909413bb129548508fb75b681c31bb4a.png)



8. **sortStudentsBySubject 方法**
    - 根据指定科目的成绩对学生进行排序，并返回排序后的列表。

![](https://i-blog.csdnimg.cn/direct/33830369ad564492b3c892c319a43e9a.png)





![](https://i-blog.csdnimg.cn/direct/38c8cac4e393431b8daa4ce08b9229f5.png)



9. **sortStudentsByTotalScore 方法**
    - 根据学生的总成绩对学生进行排序，并返回排序后的列表。

![](https://i-blog.csdnimg.cn/direct/b624b898a4a94421885695ad56256453.png)



10. **sortStudentsByAverageScore 方法**
    - 根据学生的平均成绩对学生进行排序，并返回排序后的列表。

```java
    // 根据总成绩排序
    public List<Student> sortStudentsByTotalScore() {
        return students.stream()
                .sorted(Comparator.comparingInt(Student::getTotalScore).reversed())
                .collect(Collectors.toList());
    }
```



![](https://i-blog.csdnimg.cn/direct/344ad09a9bdf4f6e8f24ae47277e1deb.png)

11. **printCourseStatistics 方法**
    - 打印指定课程的最高分和最低分的学生信息。

![](https://i-blog.csdnimg.cn/direct/9144e76874734a0582e63062bf4698e8.png)



12. **printClassCourseRates 方法**
    - 打印指定班级在某课程的优秀率和不及格率。

```java
    public void printCourseStatistics(String course) {
        Optional<Student> maxStudent, minStudent;
        switch (course) {
            case "语文":
                maxStudent = students.stream().max(Comparator.comparingInt(Student::getChineseScore));
                minStudent = students.stream().min(Comparator.comparingInt(Student::getChineseScore));
                break;
            case "数学":
                maxStudent = students.stream().max(Comparator.comparingInt(Student::getMathScore));
                minStudent = students.stream().min(Comparator.comparingInt(Student::getMathScore));
                break;
            case "英语":
                maxStudent = students.stream().max(Comparator.comparingInt(Student::getEnglishScore));
                minStudent = students.stream().min(Comparator.comparingInt(Student::getEnglishScore));
                break;
            default:
                return;
        }
        if (maxStudent.isPresent() && minStudent.isPresent()) {
            System.out.println("最高分学生: " + maxStudent.get());
            System.out.println("最低分学生: " + minStudent.get());
        }
    }

```

13. **控制台菜单**
    - 提供用户交互界面，允许用户通过选择菜单项执行不同的操作。





本学生成绩管理系统通过文件读写、集合操作和控制台交互，实现了学生信息的管理和查询功能。通过合理的类设计和方法分工，代码结构清晰，功能实现明确，方便后续的扩展和维护。



![](https://i-blog.csdnimg.cn/direct/d586d6c6c726488eb04798833420b1cd.png)















---

