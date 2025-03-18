# 设计要求


本系统采用 Python 语言开发，前端基于 Python 实现图形用户界面，后端采用 MySQL 作为数据存储，两者通过自定义的数据库接口模块进行交互。整体设计遵循模块化和面向对象的思想。



1）数据库操作层

针对数据库的所有操作，统一封装在一个 DBHelp 类中。该类内部使用 pymysql 模块提供与 MySQL 的连接，采取单例模式（通过类方法 get_instance）保证整个系统只创建一个数据库连接，避免资源浪费。DBHelp 类提供了查询、插入、更新、删除、借阅归还、续借以及退还申请等业务逻辑方法，所有 SQL 语句均在该层中实现，同时定义了 commit 与 rollback 方法以确保数据一致性。



2）数据模型和数据表设计

  数据库中主要包含用户表（user）、书籍表（book）、借阅信息表（borrow_info）和退还申请表（ask_return）。各表设计中充分考虑了实际业务需求，例如用户表中包含登录及注册时间、删除标记等信息；书籍表中记录库存和借出数量；借阅信息表保存借阅开始时间、预计归还时间以及归还状态；退还申请表用于存储用户的退还原因与处理状态。并通过适当的字段类型和约束确保数据有效性。



3）前端界面设计  

 构建各窗口模块，主要包括登录、注册、图书浏览、图书借阅归还、订单管理、个人信息管理等子系统。各个界面模块与数据库层解耦，所有数据操作均通过调用 DBHelp 模块提供的接口实现。前端界面通过 QSS 样式表统一风格，采用布局管理器（如 QVBoxLayout、QHBoxLayout、QGridLayout）构建响应式界面，同时利用信号与槽机制实现各窗口间数据传递和异步交互。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742301229822-1db6e8bc-dd7e-4690-90ab-fd69bb4baad3.png)

# 运行结果
登录

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300153758-395d79e8-8f4e-4f89-89a5-2ee084d484bb.png)

注册

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300338113-64478691-df78-43e8-b9d8-4b78d7cb58b6.png)

借阅信息

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300538848-1302e615-2758-4062-98a7-da410f951d6b.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742301234040-c77d631c-7fc9-4dee-9228-aa599963032f.png)

主页

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300381690-c3974ca9-8f15-4648-874d-050c3ce5ffb9.png)

图书管理

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300498722-c0327294-d133-4e09-9dfe-37ffce587f65.png)

系统设置

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300511951-fddf6a22-7301-4ccc-8d29-95e123b07597.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742301236798-c75f1a05-9d1a-4a50-a6b5-5936c27acf2b.png)

关于

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742300518859-1b38dc02-5395-47ec-9e98-373dd9d98dd7.png)



# 代码展示
登出信息

```python

from PyQt5.QtGui import QIcon
from PyQt5.QtWidgets import QMainWindow, QMessageBox, QWidget
from ui.main_window import Ui_MainWindow
from util.common_util import ROLE_MAP, APP_ICON, SYS_STYLE
from view.home_window import HomeWindow
from view.book_manage_window import BookManageWindow
from view.borrow_info_window import BorrowInfoWindow
from view.setting_window import SettingWindow


# noinspection PyCallByClass
class MainWindow(Ui_MainWindow, QMainWindow):

    def __init__(self,login=None, username=None, role=None):
        super(MainWindow, self).__init__()
        self.setupUi(self)
        self.is_change_user = False
        self.username = username
        self.login_win = login
        self.role = ROLE_MAP.get(str(role))
        self.init_slot()
        self.init_ui()

    def init_ui(self):
        self.pushButton.setProperty('class', 'Aqua')
        self.pushButton.setMinimumWidth(60)
        self.listWidget.setProperty('class', 'Normal')
        self.setStyleSheet(SYS_STYLE)
        self.setWindowIcon(QIcon(APP_ICON))
        self.setWindowTitle('图书管理系统-Version 1.0.0.0 Beta')
        self.listWidget.setCurrentRow(0)
        self.current_username_label.setText(self.username)
        self.current_role_label.setText(self.role)
        self.stackedWidget.removeWidget(self.page)
        self.stackedWidget.removeWidget(self.page_2)
        self.stackedWidget.addWidget(HomeWindow())
        self.stackedWidget.addWidget(BorrowInfoWindow(user_role=self.role, username=self.username))
        self.stackedWidget.addWidget(BookManageWindow(self.role, self.username))
        self.stackedWidget.addWidget(SettingWindow())
        self.stackedWidget.addWidget(QWidget())

    def init_slot(self):
        self.listWidget.currentItemChanged.connect(self.item_changed)
        self.pushButton.clicked.connect(self.log_out)

    def item_changed(self):
        self.stackedWidget.setCurrentIndex(self.listWidget.currentRow())

    def log_out(self):
        self.is_change_user = True
        self.close()

    def closeEvent(self, event):
        if self.is_change_user:
            reply = QMessageBox.question(self, '消息', '确定退出当前账号吗?',
                                         QMessageBox.Yes | QMessageBox.No, QMessageBox.No)
        else:
            reply = QMessageBox.question(self, '消息', '确定退出系统吗?',
                                         QMessageBox.Yes | QMessageBox.No, QMessageBox.No)
        if reply == QMessageBox.Yes:
            event.accept()
            if self.is_change_user:
                self.login_win.show()
        else:
            event.ignore()
            self.is_change_user = False

```

主页面

```python
# -*- coding: utf-8 -*-
from PyQt5 import QtCore, QtGui, QtWidgets

class Ui_Form(object):
    def setupUi(self, Form):
        Form.setObjectName("Form")
        Form.resize(253, 142)
        Form.setStyleSheet("background-color: rgb(255, 255, 255);")
        self.gridLayout = QtWidgets.QGridLayout(Form)
        self.gridLayout.setObjectName("gridLayout")
        self.verticalLayout = QtWidgets.QVBoxLayout()
        self.verticalLayout.setObjectName("verticalLayout")
        self.label = QtWidgets.QLabel(Form)
        self.label.setObjectName("label")
        self.verticalLayout.addWidget(self.label)
        self.horizontalLayout = QtWidgets.QHBoxLayout()
        self.horizontalLayout.setObjectName("horizontalLayout")
        self.label_2 = QtWidgets.QLabel(Form)
        self.label_2.setObjectName("label_2")
        self.horizontalLayout.addWidget(self.label_2)
        self.username_lineEdit = QtWidgets.QLineEdit(Form)
        self.username_lineEdit.setObjectName("username_lineEdit")
        self.horizontalLayout.addWidget(self.username_lineEdit)
        self.verticalLayout.addLayout(self.horizontalLayout)
        self.horizontalLayout_2 = QtWidgets.QHBoxLayout()
        self.horizontalLayout_2.setObjectName("horizontalLayout_2")
        self.label_3 = QtWidgets.QLabel(Form)
        self.label_3.setObjectName("label_3")
        self.horizontalLayout_2.addWidget(self.label_3)
        self.password_lineEdit = QtWidgets.QLineEdit(Form)
        self.password_lineEdit.setEchoMode(QtWidgets.QLineEdit.PasswordEchoOnEdit)
        self.password_lineEdit.setObjectName("password_lineEdit")
        self.horizontalLayout_2.addWidget(self.password_lineEdit)
        self.verticalLayout.addLayout(self.horizontalLayout_2)
        self.horizontalLayout_3 = QtWidgets.QHBoxLayout()
        self.horizontalLayout_3.setObjectName("horizontalLayout_3")
        self.register_pushButton = QtWidgets.QPushButton(Form)
        self.register_pushButton.setObjectName("register_pushButton")
        self.horizontalLayout_3.addWidget(self.register_pushButton)
        self.login_pushButton = QtWidgets.QPushButton(Form)
        self.login_pushButton.setObjectName("login_pushButton")
        self.horizontalLayout_3.addWidget(self.login_pushButton)
        self.verticalLayout.addLayout(self.horizontalLayout_3)
        self.gridLayout.addLayout(self.verticalLayout, 0, 0, 1, 1)

        self.retranslateUi(Form)
        QtCore.QMetaObject.connectSlotsByName(Form)

    def retranslateUi(self, Form):
        _translate = QtCore.QCoreApplication.translate
        Form.setWindowTitle(_translate("Form", "Form"))
        self.label.setText(_translate("Form", "图书管理系统"))
        self.label_2.setText(_translate("Form", "用户:"))
        self.username_lineEdit.setText(_translate("Form", "admin"))
        self.label_3.setText(_translate("Form", "密码:"))
        self.password_lineEdit.setText(_translate("Form", "admin"))
        self.register_pushButton.setText(_translate("Form", "注册"))
        self.login_pushButton.setText(_translate("Form", "登录"))


```

其他代码略...



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742301241227-f2f570c5-0969-45e5-bdb2-3c73f7a18a76.png)

