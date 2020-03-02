> 每天对自己说一句：早安！

  * [一、基本运算](#一基本运算)
    * [除：](#除)
    * [整除](#整除)
  * [二、if-else](#二if-else)
  * [三、循环](#三循环)
    * [1.for循环](#1for循环)
    * [2.while循环](#2while循环)
    * [3.break、continue](#3breakcontinue)
  * [四、字符串](#四字符串)
    * [1.字符串转换数字](#1字符串转换数字)
    * [2.单三引号（''''''）、和双三引号（""""""）](#2单三引号和双三引号)
    * [3.字符串下标用法](#3字符串下标用法)
    * [4.字符串格式化](#4字符串格式化)
  * [五、列表、元组、字典](#五列表元组字典)
    * [1.列表](#1列表)
    * [2.元组()](#2元组)
    * [3.字典{}](#3字典)
  * [六、函数](#六函数)
  * [七、类和对象](#七类和对象)
    * [1.语法格式](#1语法格式)
    * [2.继承](#2继承)
  * [八、文件操作](#八文件操作)
  * [九、导包](#九导包)
  * [十、第三方库](#十第三方库)
  * [十一、小练习](#十一小练习)



## 一、基本运算
> print("hello world!")
> print为打印语句

### 除：
`print(15/4)`

### 整除
`print(15//4)`

## 二、if-else
> 注意冒号及elif这种写法
```
score = 90
if score > 80:
    print('A')
elif score > 60:
    print('b')
else:
    print('c')
```

## 三、循环

### 1.for循环
> for循环 注意range(i,n,t)，从i开始，到n结束，步长t
```python
for i in range(5):
    print(i)
```

> for循环获取下标

```python
for index, i in enumerate(range(5)):
    print(f'{index}:{i}')
```

### 2.while循环
> 注意else的用法
```
n = 1
while n < 10:
    print(n)
    n = n + 1
else:
    print("循环结束")
```

### 3.break、continue
```
# break跳出循环 (True首字母是大写)
while True:
    s = input('输入0退出')
    if s == '0':
        break
    print('输入的是：', s)
    
# continue开始下次循环
for s in 'python':
    if s == 'y':
        continue
    print(s)
```

## 四、字符串

> 单引号、双引号都表示字符串
### 1.字符串转换数字
n = int('12')

### 2.单三引号（''''''）、和双三引号（""""""）
> 可以对字符串换行

```
print("""
123
456
""")
```

### 3.字符串下标用法
> 可以使用负数，表示从后面开始，-1开始
```
s = '思无邪'
print(s[0])
print(s[-1])
# 从下标0取到1
print(s[0 : 2])
# 每隔两个取一个值
print(s[0 : 3 : 2])
```

### 4.字符串格式化
```
user_1 = '张三'
user_2 = '李四'
print(f'{user_1}、{user_2}')
print('{}、{}'.format(user_1, user_2))
```

## 五、列表、元组、字典

### 1.列表
```
# 列表
my_list = [1, 2, 'a', 1.3]
print(my_list[2])
# append追加、insert插入
my_list.append('aaa')
print(my_list)
my_list.insert(0, 100)
print(my_list)
# extend 扩充列表(参数为一个序列)
my_list = [1, 2]
my_list.extend("sss")
my_list.extend([33, 44])
# 运行结果为[1, 2, 's', 's', 's']
# 运行结果为[1, 2, 33, 44]
print(my_list)

# 删除元素pop(), 可填入下标，默认删除最后一个
my_list.pop()
print(my_list)

# remove删除指定元素, 只删一个
my_list.remove('s')
print(my_list)
```

### 2.元组()
> 不可变得列表
`my_list` = (1, 2, 'a')

### 3.字典{}
> 键值对key-value
```
user = {
    'name' : 'tom',
    'age' : 18
}
print(user)
print(user['age'])
# 直接赋值来增加元素
```

## 六、函数
```
# 函数 
def sum(n, m):
    s = 0
    while n <= m:
        s += n
        n += 1
    return s

# 1+2+...+100
print(sum(1, 100))
```

## 七、类和对象

### 1.语法格式
```
class Person:
    def __init__(self, name, sex, birthday):
        self.name = name
        self.sex = sex
        self.birthday = birthday

    def say(self, word):
        print(f'{self.name}说："{word}"')

zhang_san = Person('张三', '男', '20200101')
zhang_san.say("你好")
```

### 2.继承
> 1) 子类会通过继承得到所有父类的方法，那么如果多个父类中有相同的方法名，排在前面的父类同名方法会“遮蔽”排在后面的父类同名方法
> 2)
```
class Animal:
    def dog(self):
        print ('我有一只狗！他叫%s'%self.dog_name)
 
class Child:
    def son(self):
        print ('我有两个儿子！')
 
# 定义Myself类，继承Animal类和Child类
class Myself(Animal,Child):
    def name(self):
        print ('我叫小明！')
 
M = Myself()    # 创建Myself对象
M.dog_name = '大黄'   # 通过Myself对象添加dog_name类变量
M.dog() # 调用Myself对象的dog()方法,打印 我有一只狗！他叫大黄
M.son() # 调用Myself对象的son()方法,打印 我有两个儿子！
M.name()    # 调用Myself对象的name()方法,打印 我叫小明！
```

## 八、文件操作
```
f = open('测试.txt', encoding='utf-8')
s = f.read()
print(s)
f.close()

# 写文件
f = open('测试.txt', mode='w', encoding='utf-8')
s = f.write('加油吧\n')
s = f.write('陌生人')
f.close()
```

## 九、导包
```
# import导入多个包，逗号分隔
import random, time

# 导入整个包的部分功能函数
from ... import ...
```

## 十、第三方库
```
# 第三方库
# 安装命令，在终端输入如：
# pip install jieba
# pip install wordcloud

# pip的默认源在国外，一般会超时失败，需要把pip更换为国内的镜像
# pip install jieba --index https://pypi.tuna.tsinghua.edu.cn/simple/
# pip install wordcloud --index https://pypi.tuna.tsinghua.edu.cn/simple/

import jieba
# 能够将一段中文文本分割成中文词语的序列
s = jieba.lcut('能够将一段中文文本分割成中文词语的序列')
print(s)

# 第三方库wordcloud词云
import jieba
from wordcloud import WordCloud

s = '''
?????
可是啊
总有那风吹不散的认真
!!
总有大雨
......
也不能抹去的泪痕
有一天
太阳会升起在某个清晨
一道彩虹两个人
？？
'''
# 分词
cut_list = jieba.lcut(s)
# 用空格重新拼接成一个字符串
new_str = ' '.join(cut_list)
# 生成云词对象
word_cloud = WordCloud(font_path='msyh.ttc').generate(new_str)
# 保存到图片
word_cloud.to_file('图片.png')
```

## 十一、小练习
**学生信息管理模拟**
```
def show_menu():
    # 显示操作菜单界面
    print('''
    **************************
    欢迎使用【学生管理系统】v1.0
    1.显示所有学生信息
    2.新建学生信息
    3.查询学生信息
    4.修改学生信息
    5.删除学生信息
    0.退出系统
    ***************************
    ''')
# 1.显示所有学生信息
def show_all(data):
    for student in data:
        print(student)

# 2.新建学生信息
def creat_student():
    id = input('输入编号：')
    name = input('输入名字：')
    student = {
        'id': id,
        'name': name
    }
    student_data.append(student)

# 3.查询学生信息
def find_student():
    name = input('查询学生名字:')
    for student in student_data:
        if(student['name'] == name):
            print(student)
            return
    else:
        print('查无此人')

# 4.修改学生信息
def modify_student():
    get_name = input('查询学生的名字：')
    for student in student_data:
        if(student['name'] == get_name):
            print(student)
            student['id'] = input('输入编号：')
            student['name'] = input('输入名字：')
            return
    else:
        print('查无此人')

# 5.删除学生信息
def remove_student():
    name = input("查询学生的名字：")
    for student in student_data:
        if(student['name'] == name):
            print(student)
            student_data.remove(student)
            return
    else:
        print('查无此人')

# 所有的学生数据，用一个列表
student_data = [
    {
        'id': 1001,
        'name': '张三'
    },
    {
        'id': 1002,
        'name': '李四'
    }
]

while True:
    show_menu()
    op = input('请输入序号：')
    if op == '1':
        show_all(student_data)
    elif op == '2':
        creat_student()
    elif op == '3':
        find_student()
    elif op == '4':
        modify_student()
    elif op == '5':
        remove_student()
    elif op == '0':
        print(0)
        break

```
