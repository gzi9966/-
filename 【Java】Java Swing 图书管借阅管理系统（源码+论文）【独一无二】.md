---

> # 【Java】Java Swing 图书管借阅管理系统（源码+论文）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
首先，GUI应用程序开发是这个实验的核心背景。GUI（Graphical User Interface）应用程序是一种通过可视化元素，如窗口、按钮、文本框等，与用户进行交互的应用程序。GUI应用程序的开发对于创建用户友好且易于使用的软件至关重要。在许多领域，如企业应用、桌面应用、教育软件等，GUI应用程序都有广泛的应用。因此，理解如何构建GUI应用程序是软件开发领域的一个重要方面。

其次，基于用户权限的访问控制是这个实验的另一个关键背景。在许多应用程序中，不同用户可能拥有不同的权限和访问级别。，在一个图书管理系统中，管理员可能具有对所有功能和数据的完全访问权限，而普通用户可能仅具有查看图书信息的权限。因此，为了确保数据的安全性和合规性，需要实现基于用户权限的访问控制。这可以通过在应用程序中动态控制用户界面上的功能按钮、限制特定用户的操作等方式来实现。

综上所述，这段代码的实验背景涵盖了GUI应用程序开发和基于用户权限的访问控制。通过开发一个简单的图书管理系统，这个实验旨在教授如何创建具有用户友好界面的应用程序，并演示如何根据用户的身份和权限控制他们在应用程序中的操作。这些知识和技能对于开发实际应用程序非常重要，因为它们可以应用于各种领域和行业，提高了软件开发人员的综合能力和竞争力。同时，这个实验还有助于学生理解和应用编程中的关键概念，如事件处理、数据库交互和用户界面设计，为他们的职业生涯奠定了坚实的基础。因此，这个实验背景具有广泛的教育和实践价值。



![](https://i-blog.csdnimg.cn/direct/75889dd90e5b447e912d566a8243b802.png)



![](https://i-blog.csdnimg.cn/direct/627aa8ed133f4222900f75e6f1449d67.png)

![](https://i-blog.csdnimg.cn/direct/50bb57dd3c4447178eda179384200ee6.png)

![](https://i-blog.csdnimg.cn/direct/66d5365119bc4667a78d74a5f7f61a50.png)

---

# 二、设计思路
## 登录界面
1. 导入必要的包和类：  
通过import语句导入了一些Swing和数据库相关的类，这些类用于创建GUI界面和处理数据库连接、查询等操作。
2. Login类继承自JFrame类：  
Login类是一个窗口界面，用于用户输入用户名和密码进行登录验证。
3. 构造方法Login():  
在构造方法中，设置了窗口标题、大小、关闭操作等窗口属性。  
创建了一个包含用户名、密码标签和文本框、登录按钮和取消按钮的面板，并使用GridLayout布局管理器来布置这些组件。  
添加了登录按钮和取消按钮的动作监听器，以便在按钮点击时执行相应的操作。  
将所有组件添加到窗口中，并设置窗口居中可见。
4. actionPerformed方法：  
actionPerformed方法是登录按钮的点击事件处理方法。  
从用户名文本框和密码输入框中获取用户输入的用户名和密码。  
调用validateLogin()方法来验证用户名和密码是否在数据库中匹配。  
如果匹配成功，会显示登录成功的消息框，并关闭当前登录窗口。  
同时，将用户名存储到GlobalVar类的静态变量中，以便其他部分的程序可以访问。  
如果验证失败，会显示用户名或密码错误的消息框。
5. validateLogin()方法：  
validateLogin()方法用于验证用户名和密码是否匹配。  
首先创建一个数据库连接，然后使用PreparedStatement执行SQL查询语句，以查找匹配的用户名和密码。  
如果查询结果包含记录，表示验证成功，返回该记录的id；否则，返回null表示验证失败。
6. main方法：  
main方法是程序的入口点，通过SwingUtilities.invokeLater()方法在事件分发线程中创建Login实例，确保Swing组件在正确的线程中创建和运行。



![](https://i-blog.csdnimg.cn/direct/2f845504599e413aa64887fac5cfb1c2.png)



总体来说，这段代码实现了一个简单的登录界面，用户可以输入用户名和密码，然后进行登录验证。验证通过后，登录窗口关闭，并将用户名存储到全局变量中，然后打开另一个窗口。需要注意的是，代码中存在一些不够优雅的部分，重复的代码块和硬编码的方式，可以进一步改进代码以提高可维护性和可读性。

## 修改密码
1. 导入必要的包和类：  
通过import语句导入了一些Swing和数据库相关的类，这些类用于创建GUI界面和处理数据库连接、查询等操作。
2. UpdatePassword类继承自JFrame类：  
UpdatePassword类是一个窗口界面，用于用户输入原密码和新密码，然后进行密码修改操作。
3. 构造方法UpdatePassword():  
在构造方法中，设置了窗口标题、大小、关闭操作等窗口属性。  
创建了一个包含用户名、密码标签和文本框、登录按钮和取消按钮的面板，并使用GridLayout布局管理器来布置这些组件。  
添加了登录按钮和取消按钮的动作监听器，以便在按钮点击时执行相应的操作。  
将所有组件添加到窗口中，并设置窗口居中可见。
4. actionPerformed方法：  
actionPerformed方法是登录按钮的点击事件处理方法。  
从用户名文本框和密码输入框中获取用户输入的用户名和密码。  
调用check()方法来检查原密码和新密码是否一致，如果一致，继续下一步，否则显示错误消息。  
如果密码一致，则调用validateLogin()方法来更新数据库中的密码，并显示成功或失败的消息框。  
在成功或失败后，关闭当前登录窗口，并在事件分发线程中创建一个新的ShowMain实例。
5. validateLogin()方法：  
validateLogin()方法用于更新数据库中的用户密码。  
首先创建一个数据库连接，然后使用PreparedStatement执行SQL语句来更新用户密码。  
更新成功后，返回true；否则，返回false。
6. check()方法：  
check()方法用于检查原密码和新密码是否一致。  
如果一致，返回true；否则，返回false。



7. main方法：  
main方法是程序的入口点，通过SwingUtilities.invokeLater()方法在事件分发线程中创建UpdatePassword实例，确保Swing组件在正确的线程中创建和运行。

总体来说，这段代码实现了一个简单的密码修改界面，用户输入原密码和新密码后，会将新密码更新到数据库中。需要注意的是，代码中存在一些不够优雅的部分，重复的代码块和硬编码的方式。可以进一步改进代码以提高可维护性和可读性。

## 借书界面
1. 导入必要的包和类：  
通过import语句导入了一些Swing、数据库和日期处理相关的类，用于创建GUI界面、处理数据库连接、查询以及日期格式化等操作。
2. Borrow类继承自JFrame类：  
Borrow类是一个窗口界面，用于实现图书借阅操作，包括查询图书信息、读者信息，以及借阅图书的功能。
3. 构造方法Borrow():  
在构造方法中，设置了窗口标题、大小、关闭操作等窗口属性。  
创建了包括查询图书编号、读者编号、查询按钮、文本区域和确定、取消按钮的各个面板，并使用布局管理器将它们组织在一起。  
添加了查询按钮、确定按钮和取消按钮的动作监听器，以便在按钮点击时执行相应的操作。  
使用JScrollPane包装文本区域，以支持滚动查看文本。
4. actionPerformed方法：  
actionPerformed方法是各个按钮的点击事件处理方法。  
查询按钮的点击事件会调用selectBorrow()方法，用于查询图书和读者信息，并显示在文本区域中。  
确定按钮的点击事件会调用add()方法，用于借阅图书，并在成功借阅后显示成功消息框，并关闭当前窗口。  
取消按钮的点击事件会关闭当前窗口。
5. selectBorrow()方法：  
selectBorrow()方法用于查询图书和读者信息，并显示在文本区域中。  
通过用户输入的图书编号和读者编号，查询相应的图书和读者信息，并统计读者的借阅次数和最近借阅时间。  
使用SimpleDateFormat格式化最近借阅时间，并将结果显示在文本区域中。



6. add()方法：  
add()方法用于借阅图书。  
根据用户输入的图书编号和读者编号，插入借阅记录到数据库中，同时设置借阅日期和应还日期，并标记为未归还。  
如果插入成功，返回true；否则，返回false。
7. main方法：  
main方法是程序的入口点，通过SwingUtilities.invokeLater()方法在事件分发线程中创建Borrow实例，确保Swing组件在正确的线程中创建和运行。

总体来说，这段代码实现了一个简单的图书借阅界面，用户可以查询图书和读者信息，并进行图书借阅操作。需要注意的是，代码中存在一些不够优雅的部分，重复的代码块和硬编码的方式，可以进一步改进代码以提高可维护性和可读性。此外，代码还应该考虑一些错误处理和边界情况，以提高程序的健壮性。

## 书籍查询
1. 导入必要的包和类：  
通过import语句导入了一些Swing、数据库相关的类，用于创建GUI界面和处理数据库连接、查询等操作。
2. BookQuery类继承自JFrame类：  
BookQuery类是一个窗口界面，用于实现图书查询功能，用户可以根据图书名称、作者、出版社和出版时间等条件查询图书信息。
3. 构造方法BookQuery():  
在构造方法中，设置了窗口标题、大小、关闭操作等窗口属性。  
创建了包括图书名称、作者、出版社、出版时间文本框和查询按钮、结果表格的各个组件，并使用FlowLayout布局管理器将它们组织在一起。  
创建了一个空的DefaultTableModel模型来表示表格数据。  
使用JScrollPane包装结果表格，以支持滚动查看结果。
4. actionPerformed方法：  
actionPerformed方法是查询按钮的点击事件处理方法。  
查询按钮的点击事件会调用queryBooks()方法，用于根据用户输入的条件构建SQL查询语句，执行查询并将查询结果显示在结果表格中。



5. queryBooks()方法：  
queryBooks()方法用于执行图书查询操作。  
根据用户输入的图书名称、作者、出版社和出版时间等条件构建SQL查询语句。  
使用PreparedStatement执行SQL查询，将查询结果遍历并加入到DefaultTableModel模型中，最后将模型设置到结果表格中显示查询结果。  
![](https://i-blog.csdnimg.cn/direct/837dc678c39d48da9ede813462d6cb3d.png)
6. main方法：  
main方法是程序的入口点，通过SwingUtilities.invokeLater()方法在事件分发线程中创建BookQuery实例，确保Swing组件在正确的线程中创建和运行。

总体来说，这段代码实现了一个图书查询界面，用户可以根据不同的查询条件查询图书信息，并将查询结果显示在表格中。需要注意的是，代码中使用了PreparedStatement来构建SQL查询语句，从而防止SQL注入攻击。然而，代码仍然可以进一步改进，添加错误处理和更友好的用户界面等功能。

## 读者信息
代码设计是一个Java Swing应用程序，用于管理读者信息的界面。它包括了读者信息的添加、更新和删除功能，分为三个子界面：AddReaderOperle（添加读者信息）、UpdateReaderOperle（更新读者信息）和DelReaderOperle（删除读者信息）。



1. ReaderOperate 类：  
这是主界面类，用于创建读者管理的主界面。  
它包含了添加、更新和删除读者信息的按钮，点击这些按钮会打开对应的子界面。  
构造方法中设置了窗口的标题、大小、默认关闭操作等。  
通过按钮的事件监听器，点击按钮会打开对应的子界面，并关闭主界面。





```java
package book;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 读者管理界面类
 */
public class ReaderOperate extends JFrame {
    private JTextField readerIdField;
    private JTextField readerNameField;
    private JTextField readerTypeField;
    private JTextField sexField;
    private JTextField maxNumField;
    private JTextField daysNumField;

    /**
     * 构造方法，创建读者管理界面
     */
    public ReaderOperate(String windowTitle) {
        setTitle(windowTitle);
        setSize(400, 450);
        setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);

        // 创建按钮面板
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));

        JButton addButton = new JButton("添加读者");
        JButton updateButton = new JButton("更新读者");
        JButton deleteButton = new JButton("删除读者");

        addButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                // 关闭当前读者管理窗口
                dispose();
                new AddReaderOperle();
            }
        });

        updateButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                dispose();
                new UpdateReaderOperle();
            }
        });

        deleteButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                dispose();
                new DelReaderOperle();
            }
        });

        buttonPanel.add(addButton);
        buttonPanel.add(updateButton);
        buttonPanel.add(deleteButton);

        add(buttonPanel, BorderLayout.NORTH);
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(10, 2, 10, 10));
 // 略.....
 // 略.....
 // 略.....
```

![](https://i-blog.csdnimg.cn/direct/4b7312be017d4cf7a0ef95962d5678b8.png)

2. AddReaderOperle 类：  
这是添加读者信息的界面类。  
它包含了读者编号、读者姓名、读者类别、读者性别、可借数量和可借天数等字段的输入框。  
用户可以输入读者信息并点击 "确定" 按钮来保存信息。  
构造方法中设置了窗口的标题、大小、默认关闭操作等。  
通过 "确定" 按钮的事件监听器，点击按钮会调用 saveReader() 方法来保存读者信息。
3. UpdateReaderOperle 类：  
这是更新读者信息的界面类。  
它包含了读者编号的输入框，用户可以输入要更新的读者编号，并点击 "查询" 按钮查找读者信息。  
一旦找到对应的读者信息，界面会填充读者的其他信息（姓名、类别、性别、可借数量和可借天数）。  
用户可以修改这些信息并点击 "确定" 按钮来更新读者信息。  
构造方法中设置了窗口的标题、大小、默认关闭操作等。  
通过 "查询" 按钮的事件监听器，点击按钮会调用 searchReader() 方法来查询读者信息，然后通过 "确定" 按钮的事件监听器调用 updateReader() 方法来更新读者信息。





4. DelReaderOperle 类：  
这是删除读者信息的界面类，与更新界面类似。  
它包含了读者编号的输入框，用户可以输入要删除的读者编号，并点击 "查询" 按钮查找读者信息。  
一旦找到对应的读者信息，界面会填充读者的其他信息（姓名、类别、性别、可借数量和可借天数）。  
用户可以点击 "确定" 按钮来删除读者信息。  
构造方法中设置了窗口的标题、大小、默认关闭操作等。  
通过 "查询" 按钮的事件监听器，点击按钮会调用 searchReader() 方法来查询读者信息，然后通过 "确定" 按钮的事件监听器调用 updateReader() 方法来删除读者信息。

总体来说，这段代码实现了一个简单的读者信息管理系统，包括添加、更新和删除功能。每个功能都有对应的界面，用户可以通过界面来操作读者信息。需要注意的是，代码中使用了Swing库来创建图形用户界面，以及与数据库交互的功能。同时，代码中也包含了一些事件监听器来处理用户的操作。

## 归还书籍
这段代码是一个Java Swing应用程序，用于图书归还的管理。它的功能包括查询借阅信息、归还图书和取消操作。下面是代码的功能和结构分析：

1. Back 类：  
这是主界面类，用于创建图书归还管理的主界面。  
主要包括一个文本框用于输入图书编号（searchBookIdField）和一个文本框用于输入读者编号（searchReaderIdField），以及查询按钮（searchButton）、确定按钮（addButton）、取消按钮（cancelButton）和一个文本区域用于显示查询结果（resultTextArea）。  
构造方法中设置了窗口的标题、大小、默认关闭操作等。





```java
package book;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Back extends JFrame {
    private JTextField searchBookIdField; // 用于输入要查询的图书编号的文本框
    private JTextField searchReaderIdField; // 用于输入要查询的读者编号的文本框
    private JButton searchButton; // 查询按钮
    private JButton addButton; // 确定按钮
    private JButton cancelButton; // 取消按钮

    // 用于显示数据的组件
    private JTextArea resultTextArea;

    public Back(String windowTitle) {
        setTitle(windowTitle);
        setSize(600, 450);
        setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);

        // 主面板，使用BorderLayout
        JPanel mainPanel = new JPanel(new BorderLayout());

        // 顶部面板，用于查询图书
        JPanel topPanel = new JPanel(new FlowLayout());

        JLabel searchBookIdLabel = new JLabel("图书编号:");
        searchBookIdField = new JTextField(10);


        JLabel searchReaderIdLabel = new JLabel("读者编号:");
        searchReaderIdField = new JTextField(10);

        searchButton = new JButton("查询");
        addButton = new JButton("确定");
        cancelButton = new JButton("取消");

        topPanel.add(searchBookIdLabel);
        topPanel.add(searchBookIdField);
        topPanel.add(searchReaderIdLabel);
        topPanel.add(searchReaderIdField);
        topPanel.add(searchButton);
        topPanel.add(addButton);
        topPanel.add(cancelButton);

        // 用于显示数据的文本区域
        resultTextArea = new JTextArea(10, 30);
        resultTextArea.setEditable(false); // 设置为不可编辑

        // 添加滚动条
        JScrollPane scrollPane = new JScrollPane(resultTextArea);

        // 将顶部面板和文本区域添加到主面板
        mainPanel.add(topPanel, BorderLayout.NORTH);
        mainPanel.add(scrollPane, BorderLayout.CENTER);

        // 查询按钮的事件监听器
        searchButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                // 调用方法查询图书并填充信息
                try {
                    selectBorrow();
                } catch (SQLException | ClassNotFoundException ex) {
                    throw new RuntimeException(ex);
                }
            }
        });

      // 略.....
 // 略.....
 // 略.....

```

![](https://i-blog.csdnimg.cn/direct/079940d94e72491b99c93ce4e8f6b998.png)

2. selectBorrow 方法：  
这个方法用于查询借阅信息并显示在文本区域中。  
它首先获取用户输入的图书编号和读者编号，然后调用 BookSelect.selectBookById 和 ReaderSelect.selectReaderById 方法来查询对应的图书和读者信息。  
还统计了读者的借阅次数。  
然后，将查询到的图书信息、读者信息、当前日期等信息格式化后，显示在文本区域中。
3. add 方法：  
这个方法用于执行图书归还操作。  
它首先检查指定的读者是否借阅了指定的图书（通过调用 IfBorrowBack.hasBorrowed 方法来判断）。  
如果读者借阅了图书，就会将借阅记录中的 if_back 标记为 "是"，并设置归还日期为当前日期。  
归还操作完成后，方法返回 true 表示归还成功，否则返回 false 表示没有归还成功。
4. 查询按钮、确定按钮和取消按钮的事件监听器：  
查询按钮的事件监听器会调用 selectBorrow 方法来查询借阅信息并显示在文本区域中。  
确定按钮的事件监听器会调用 add 方法来执行归还操作，如果成功则显示归还成功的消息，并关闭当前窗口，然后打开一个新的窗口（BB 类的实例）。  
取消按钮的事件监听器用于关闭当前窗口。

总体来说，这段代码实现了一个简单的图书归还管理系统，用户可以通过输入图书编号和读者编号来查询借阅信息，并执行图书归还操作。借阅信息会显示在文本区域中，归还成功后会弹出消息提示。需要注意的是，代码中使用了Swing库来创建图形用户界面，以及与数据库交互的功能。





## 主界面
这段代码是一个Java Swing应用程序，实现了一个主页面窗口，用户可以根据不同的权限访问不同的子页面。以下是代码的功能和结构分析：

1. ShowMain 类：  
这是主页面的类，用于创建主页面的窗口。  
窗口的标题为 "主页面"，初始大小为400x300，设定了关闭窗口时的默认操作为退出应用程序。

```java
package book;


import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class ShowMain extends JFrame {
    private JButton button1;

    public ShowMain(String username) throws SQLException, ClassNotFoundException {
        setTitle("主页面");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // 使用GridBagLayout布局管理器
        setLayout(new GridBagLayout());

        // 创建 GridBagConstraints 对象
        GridBagConstraints gbc = new GridBagConstraints();

        // 设置顶部按钮行的高度为整个页面的十分之一
        gbc.weighty = 0.1;

        // 创建一个面板来包含顶部的按钮
        JPanel topPanel = new JPanel(new GridLayout(1, 4, 10, 0));
        button1 = new JButton("数据管理");
        JButton button2 = new JButton("借阅管理");
        JButton button3 = new JButton("查询管理");


        // 根据用户权限设置按钮可用性
        if (!isAdminUser(username)) {
            button1.setEnabled(false);
        }

        button1.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                openPage("A");
            }
        });

        button2.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                openPage("B");
            }
        });

        button3.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                openPage("C");
            }
        });

        button4.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                openPage("D");
            }
        });

        topPanel.add(button1);
        topPanel.add(button2);
        topPanel.add(button3);
        topPanel.add(button4);

        // 添加顶部按钮行到页面
        gbc.gridx = 0;
        gbc.gridy = 0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        add(topPanel, gbc);

        // 创建一个面板来包含主内容
        JPanel mainContentPanel = new JPanel();
        gbc.gridx = 0;
        gbc.gridy = 1;
        gbc.fill = GridBagConstraints.BOTH;
        gbc.weightx = 1.0; // 让主内容面板水平充满整个宽度
        gbc.weighty = 0.9; // 让主内容面板垂直充满整个剩余高度
        add(mainContentPanel, gbc);

        setLocationRelativeTo(null);
        setVisible(true);
    }
 // 略.....
 // 略.....
 // 略.....
}

```







2. 布局管理和界面元素：  
使用了GridBagLayout布局管理器来管理主页面的布局，它允许更灵活地控制组件的位置和大小。  
创建了一个GridBagConstraints对象gbc来控制组件的位置和大小。  
顶部按钮行使用JPanel包含四个按钮，按钮之间有10像素的水平间距。这些按钮分别用于数据管理、借阅管理、查询管理和系统管理。
3. 用户权限控制：  
在构造函数中，根据用户的权限来设置按钮的可用性。用户权限通过isAdminUser方法查询数据库中tb_user表的is_admin字段来确定是否是管理员用户。  
如果用户不是管理员用户，数据管理按钮（button1）将被禁用，即不可点击。
4. 按钮事件监听器：  
每个按钮都有一个事件监听器，当用户点击按钮时，会执行相应的操作。  
openPage 方法用于根据按钮的名称打开相应的子页面。具体的子页面包括数据管理、借阅管理、查询管理和密码更新页面。  
每个子页面的窗口会在打开之前关闭当前主页面窗口。
5. isAdminUser 方法：  
该方法用于查询用户是否是管理员用户。  
它通过执行SQL查询语句从数据库中检索用户的is_admin字段值，并返回一个布尔值，表示用户是否为管理员。
6. main 方法：  
main 方法用于启动整个应用程序。  
在SwingUtilities.invokeLater中，创建了一个ShowMain实例，这是应用程序的入口点。  
如果出现异常，会抛出RuntimeException。

总的来说，这段代码实现了一个主页面，根据用户的权限来决定哪些功能按钮可用，点击按钮会打开不同的子页面。这是一个简单的权限管理和导航功能的应用程序。

![](https://i-blog.csdnimg.cn/direct/2bcf771abad5455b93c271e0765d946d.png)















---

# 三、可视化分析
![](https://i-blog.csdnimg.cn/direct/a70abd41ad304628b981c80800f38e4f.png)

![](https://i-blog.csdnimg.cn/direct/ccdf7936fcce438e94509460f404f5f8.png)



![](https://i-blog.csdnimg.cn/direct/55911cefcb7140268686b5008b4b27db.png)





![](https://i-blog.csdnimg.cn/direct/ab1aa84f2f494ca6891bfcab38a203c4.png)



![](https://i-blog.csdnimg.cn/direct/13764dca009540ec8fb341835b28126e.png)





![](https://i-blog.csdnimg.cn/direct/1b37e3d8079746298fd30a66e7652510.png)



![](https://i-blog.csdnimg.cn/direct/27ad3f42b3444b9bac07381ef1b327df.png)



![](https://i-blog.csdnimg.cn/direct/220a6552b586452fad8b6d447b162e8d.png)



![](https://i-blog.csdnimg.cn/direct/74dc27ddde7349dc87284fc82e8de572.png)



![](https://i-blog.csdnimg.cn/direct/d8c16555e14c4aecbb83e5e2b3d89e9a.png)



![](https://i-blog.csdnimg.cn/direct/e6a8909362a249068532fba14e72f0ff.png)



















![](https://i-blog.csdnimg.cn/direct/f3dbf2070c3c49d2a50ed2d4a51e69a8.png)





---

