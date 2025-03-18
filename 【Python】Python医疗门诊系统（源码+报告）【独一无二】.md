

---

> # Python医疗门诊系统（源码+报告）【独一无二】
> @[TOC](目录)
>

---



---

# 1. 设计目的
模拟门诊患者就诊的全流程。根据医院的不同角色设计不同的类，根据类创建对象，实现门诊患者从挂号、候诊、就诊、诊间缴费、检验检查、处方、取药。在程序中，如果处方中没有药品，则无需到药房取药。

程序模拟医院门诊的流程，包括患者挂号、候诊、就诊、诊间缴费、检验检查、处方获取和药物取药等操作。不同的类分别表示医院、患者、处方、接待员、医生、收费员、检验员和药师，并具有各自的方法来模拟相关操作。程序通过控制台交互，允许用户选择不同的操作，直到退出为止。其中，药品信息从文件中读取以支持更灵活的处方管理。



---

# 2. 设计思路
1. Hospital类：表示医院，用于注册患者。  
 数据成员： patient_registry ，用于存储已注册的患者。  
 方法成员：  
**init**(self) ：构造函数，初始化 patient_registry 为空列表。  
register_patient(self, patient) ：将患者添加到 patient_registry 列表中。
2. Patient类：表示患者，用于模拟患者的不同操作。  
 数据成员： name ，表示患者的姓名。  
 方法成员：  
   __init__(self, name) ：构造函数，初始化患者的姓名。  
wait(self) ：模拟患者候诊的操作。  
see_doctor(self) ：模拟患者就诊的操作。  
pay_fee(self) ：模拟患者诊间缴费的操作。  
get_prescription(self, prescription) ：接收处方并模拟患者获得处方的操作。  
get_medicine(self, prescription) ：模拟患者取药的操作。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033945463-7355a60c-c1ca-4b01-92ac-efd9ec101a1a.png)

3. Prescription类：表示处方，包含一个药品列表。  
 数据成员： medicines ，一个包含药品名称的列表。  
 方法成员：  
__init__(self, medicines) ：构造函数，初始化药品列表。  
has_medicines(self) ：检查药品列表是否为空，返回布尔值。
4. Receptionist类：表示接待员，用于处理挂号操作。  
 方法成员：  
register_patient(self, hospital) ：接收患者信息并将患者注册到医院的患者列表中。
5. Doctor类：表示医生，用于模拟医生的诊断操作。  
 方法成员：  
diagnose(self, patient) ：接收患者信息并模拟医生诊断的操作。
6. Cashier类：表示收费员，用于模拟收费操作。  
 方法成员：  
collect_fee(self, patient) ：接收患者信息并模拟收取诊间费用的操作。
7. LabTechnician类：表示检验员，用于模拟检验操作。  
 方法成员：  
conduct_tests(self, patient) ：接收患者信息并模拟检验操作，包括打印检验结果。
8. Pharmacist类：表示药师，用于模拟提供药物和用药咨询。  
 方法成员：  
dispense_medicine(self, patient, prescription) ：接收患者信息和处方，模拟提供药物和用药咨询的操作。
9. read_medicines_from_file函数：用于从文件中读取药品列表并返回列表。  
 参数： filename ，药品列表文件的文件名。  
 返回值：药品名称的列表。

---

# 3. 功能截图
![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033948884-20006080-87a9-499c-b491-927ce3654284.png)

## 3.1 患者挂号
![](https://i-blog.csdnimg.cn/blog_migrate/a08a7821002e6a501144d3e81a163be4.png)

## 3.2 医生就诊
![](https://i-blog.csdnimg.cn/blog_migrate/e20439a0f357d71a8536dbd4d802e45b.png)

## 3.3 分配处方
![](https://i-blog.csdnimg.cn/blog_migrate/a784a656e7af81457ec725aabcc84682.png)

## 3.4 用药咨询
![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033952875-734bf3ba-1a3b-45fd-a303-62e5724a6dd3.png)

![](https://i-blog.csdnimg.cn/blog_migrate/2ae63057781a3a83ed9997dcd91f58c4.png)

---

# 4. 部分代码


```python
def main():
    hospital = Hospital()
    receptionist = Receptionist()

    while True:
        print("**" * 20)
        print("请选择操作：")
        print("1. 挂号")
        print("2. 开始就诊")
        print("3. 退出")
        print("**" * 20)
        choice = input("请输入选项：")

        if choice == '1':
            receptionist.register_patient(hospital)
        elif choice == '2':
            for patient in hospital.patient_registry:
                patient.wait()
                doctor = Doctor()
                doctor.diagnose(patient)
                patient.pay_fee()
                lab_technician = LabTechnician()
                lab_technician.conduct_tests(patient)

                # 从文件中读取药品列表
                medicines = read_medicines_from_file("medicines.txt")
                prescription = Prescription(medicines)

                patient.get_prescription(prescription)
                cashier = Cashier()
                cashier.collect_fee(patient)
                pharmacist = Pharmacist()
                pharmacist.dispense_medicine(patient, prescription)
                patient.get_medicine(prescription)
        elif choice == '3':
            break
        else:
            print("无效选项，请重新输入。")

```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742033959000-bdbaca4a-5c94-4e0c-bf01-e1951d535214.png)

