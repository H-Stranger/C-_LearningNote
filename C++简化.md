# 引用



# 类和对象



## 封装

### 基本知识

**语法：** `class 类名{   访问权限： 属性  / 行为  };`

结构体



**设置权限**

1. public        公共权限  
2. protected 保护权限
3. private      私有权限



#### struct和class区别

struct默认成员**公有**

class默认成员**私有**



### 对象初始化和清理

#### 构造函数和析构函数

放在对象里面，必须实现的，程序每次调用完以后，系统会自动调用

**构造函数语法：**`类名(){}`

**析构函数语法：** `~类名(){}`

相同：

1. 没有返回值，不写void
2. 函数名与类名相同
3. 在对象创建和销毁时程序调用，只调用一次

不同：

前者可以重载，可以**有参数**

后者不可以重载，**无参数**



#### 构造函数的分类及调用

**构造类型**

- 有参构造和无参构造

- 普通构造和拷贝构造

拷贝构造：`Person(const Person &p)`



**调用方式**

- 括号法

```c++
Person p1(10);
Person p2(p1);
```

- 显示法

```c++
Person p1 = Person(10);
Person p2 = Person(p1);
```

- 隐式转换法

```c++
Person p1 = 10;//Person p1 = Person(10)
Person p2 = p1;//同理
```







![image-20240118215104834](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240118215104834.png)



![image-20240118220313156](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240118220313156.png)





#### 拷贝构造函数调用时机

1. 正常拷贝
2. 函数传参类
3. 函数返回类

```c++
//3. 以值方式返回局部对象
Person& doWork2()
{
    Person p1;
    cout << (int *)&p1 << endl;
    return p1;
}

void test03()
{
    Person p = doWork2();//p和p1不是同一个地址，p的内容是p1的拷贝
    cout << (int *)&p << endl;
}
```

理解：正常情况下，对象被返回以后函数会被销毁（即在栈中被销），在返回对象& 时，才是拷贝，因为类似于指针，不会让

如图所示，对象仅被析构一次，说明对象是**没有随着函数一起被销毁**，外部仍然可以调用对象的内部成员的

![image-20240119160054414](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119160054414.png)

**问题：**p1是被存放在哪的？

像下面这种情况被析构了两次

![image-20240119160615469](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119160615469.png)





**VS2022应该是进行了优化，就算是在dubug模式下同样优化这个步骤**





#### 构造函数调用规则

一般，编译器会自动提供无参构造和拷贝构造

- 用户自定义有参构造，编译器不再提供无参构造，但是会提供拷贝构造

- 用户自定义拷贝构造，则编译器不再提供其他构造



#### 深拷贝与浅拷贝

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新申请空间，进行拷贝操作

**需要重新再次理解**



#### 初始化列表

快速给类成员进行赋值操作

```c++
 ////传统方式初始化
    //Person(int a, int b, int c) {
    //    m_A = a;
    //    m_B = b;
    //    m_C = c;
    //}

    //初始化列表方式初始化
    Person(int a, int b, int c) :m_A(a), m_B(b), m_C(c) {}
    void PrintPerson() {
        cout << "mA:" << m_A << endl;
        cout << "mB:" << m_B << endl;
        cout << "mC:" << m_C << endl;
    }
```



#### 类对象作为类成员

```c++
class A
{}
class B
{
	A a;
}
```

创建对象时，A的构造函数先被调用，其次是B

销毁对象时，B的析构函数先被调用，其次是A



#### 静态成员

**静态成员变量**

特点

成员赋值特点：类内声明，类外初始化

访问方式

```
Person::m_B
```

访问权限

private修饰，则类外依然访问不到



**静态成员函数**

静态函数无法处理非静态变量

因为非静态变量属于某一个特点的对象

而静态属于所有对象，静态函数调用的时候，不知道处理哪一个对象的非静态变量

私有静态函数类外不能调用







### 对象模型和this指针

#### 成员变量和成员函数分开存储

空对象占用内存为1

![](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119104450530.png)

占用内存是4

静态变量不属于某个对象

函数则与变量存储时分开的，占用内存不一样



#### this指针概念

this指针指向的是**本对象**

返回对象本身*this





```c
Person PersonAddPerson(Person p)
{
    this->age += p.age;
    //返回对象本身
    return *this;
}//当写成这种方式，返回的对象本质上是拷贝
```

![image-20240119105441080](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119105441080.png)



```c
Person& PersonAddPerson(Person p)
{
   this->age += p.age;
   //返回对象本身
   return *this;
}//写成这种方式，则返回的是对象本身，不再是对象的拷贝
//Person& ref = p == Person const ref = *p
```



#### 空指针访问成员函数

创建类的空指针对象

```c++
Person *p = NULL
p->ShowClassNmae();//也可以这么用
//但是构造函数涉及到类的成员变量调用时会出现问题
```





#### const修饰成员函数

**常函数：**

* 成员函数后加const后我们称为这个函数为**常函数**
* 常函数内不可以修改成员属性
* 成员属性声明时加关键字mutable后，在常函数中依然可以修改

**常对象：**

* 声明对象前加const称该对象为常对象
* 常对象只能调用常函数



**常函数**

**this指针的本质**

![image-20240119130605133](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119130605133.png)

![image-20240119130648678](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119130648678.png)

**常对象**

m_B前加了mutable

![image-20240119130900686](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119130900686.png)

![](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119131157956.png)





### 友元

关键字：friend

- 全局函数做友元

全局函数可以访问类的私有成员

- 类做友元

类也可以和函数一样先在前面声明然后在后面实现具体

类可以访问该类的私有成员

- 成员函数做友元

好朋友类的成员函数







### 运算符重载

 语法：`类型 operator+（）`

与函数相同





左移运算符

![image-20240119212439365](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119212439365.png)

全局只能有一个，所以cout用&

![image-20240119212628979](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119212628979.png)

链式追加的思想

传参名可以改

































































































## 继承





![image-20240119215532493](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240119215532493.png)

#### 继承方式



![clip_image002](D:\C++\资料\讲义\assets\clip_image002.png)



![image-20240120115529050](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120115529050.png)





#### 继承中的对象模型

父类中的私有成员也被继承，子类无法访问

![](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120120815719.png)



![image-20240120120842123](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120120842123.png)



![image-20240120122435166](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120122435166.png)





#### 继承中的构造和析构顺序

构造：先父类然后子类

析构：先子类然后父类

![image-20240120135257197](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120135257197.png)



####  继承同名成员处理方式



![image-20240120135705317](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120135705317.png)





![image-20240120135635758](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120135635758.png)

![image-20240120135925894](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120135925894.png)

子类只要有同名的父类所有同名函数被隐藏，必须加作用域



#### 继承同名静态成员处理方式

**成员变量**

![image-20240120140503568](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120140503568.png)

也可以直接用父类继续访问





**成员函数**

![image-20240120140716382](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120140716382.png)



如果碰到子类与父类同名函数还是必须加作用域，如果子类没有同名函数可以通过子类直接访问

![image-20240120161731062](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120161731062.png)

就算父类定义的是公有，但是子类继承重写函数以后，依然没有定义重写函数是公有，编译器还是默认私有





#### 多继承语法

多个父类

语法：` class 子类 ：继承方式 父类1 ， 继承方式 父类2...`

多继承如果碰到多个父类同时含有同名变量或函数，则可能出现混乱，需要特殊处理，实际开发不建议使用

**需要加作用域**

 





#### 菱形继承

![image-20240120142156686](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120142156686.png)



通过virtual关键字，虚继承，解决资源浪费

![image-20240120154109203](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120154109203.png)

通过指针指向的偏移量来指向共同的变量





# 多态

静态多态

动态多态







多态原理

函数调用    p54需要强化理解

![image-20240120164218596](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120164218596.png)

加了virtual以后类对象开始占有内存

父类virtual加了以后，类对象占有内存为4，其中存储着指针，指向vftable里面的函数

![image-20240120164720456](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120164720456.png)



子类没有重写时，子类存储的指针指向的父类的函数

![image-20240120164914298](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120164914298.png)

重写了以后，情况如下

![image-20240120165146170](C:\Users\hxd15\AppData\Roaming\Typora\typora-user-images\image-20240120165146170.png)































































---



# STL





## 容器



### vector

#### 构造函数

```C++
using namespace std;
#include<iostream>
#include<algorithm>
#include<vector>

void printVector(vector<int>& v)
{
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

void test01()
{
	vector <int> v1;//无参构造
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	//拷贝 长度
	vector <int>v2(v1.begin(), v1.end());
	printVector(v2);

	//10个100拷贝
	vector <int>v3(10, 100);
	printVector(v3);

	//拷贝
	vector<int> v4(v3);
	printVector(v4);
}

//构造函数
int main()
{
	test01();
	
	return 0;
}
```



#### 赋值

```C++
void test01()
{
	vector<int> v1;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}

	//直接赋值
	vector<int> v2;
	v2 = v1;
	printVector(v2);

	//assign(前闭后开)
	vector<int> v3;
	v3.assign(v1.begin(), v1.end());
	printVector(v3);

	//assign重复拷贝
	vector <int> v4;
	v4.assign(10, 8);
	printVector(v4);
}

int main()
{
	test01();
	return 0;
}
```



#### 容量和大小

```C++
void test01()
{
	vector<int> v1;
	if (v1.empty())
		cout << "v1为空" << endl;
	
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);
	if (v1.empty())
		cout << "v1为空" << endl;
	else
	{
		cout<< "v1的容量 = " << v1.capacity()<<endl;
		cout << "v1的长度 = " << v1.size() << endl;  
	}
	
	v1.resize(15);
	printVector(v1);

	v1.resize(10);
	printVector(v1);

	v1.resize(15, 5);
	printVector(v1);
}

int main()
{
	test01();
	return 0;
}
```



#### 插入和删除

```C++
void test01()
{
	vector<int> v1;
	for (int i = 0; i < 15; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	v1.pop_back();
	v1.pop_back();
	v1.pop_back();
	v1.pop_back();
	printVector(v1);

	v1.insert(v1.begin() + 3, 100);
	printVector(v1);
	v1.erase(v1.begin() + 3);
	printVector(v1);

	v1.insert(v1.begin() + 4, 5, 100);
	printVector(v1);
	v1.erase(v1.begin() + 4, v1.begin() + 9);
	printVector(v1);

	v1.clear();
	printVector(v1);

	cout << "结束" << endl;
}

int main()
{
	test01();
	return 0;
}
```



#### 数据存取

```C++
void test01()
{
	vector<int> v1;
	for (int i = 10; i > 0; i--)
	{
		v1.push_back(i);
	}

	for (int i = 0; i < 10; i++)
	{
		cout << v1[i] << " ";
	}
	cout << endl;

	for (int i = 0; i < 10; i++)
	{
		cout << v1.at(i) << " ";
	}
	cout << endl;

	cout << "首元素为" << v1.front() << endl;
	cout << "末元素为" << v1.back() << endl;
}

int main()
{
	test01();
	return 0;
}
```



#### 互换空间

```C++
//互换容器
void test01()
{
	vector<int> v1, v2;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	for (int i = 0; i < 20; i++)
	{
		v2.push_back(i);
	}

	printVector(v1);
	cout << "v1空间 = " << v1.capacity() << endl;
	cout << "v1长度 = " << v1.size() << endl;
	printVector(v2);


	v1.swap(v2);

	printVector(v1);
	cout << "v1空间 = " << v1.capacity() << endl;
	cout << "v1长度 = " << v1.size() << endl;
	printVector(v2);
	//互换过程包括容器空间和长度
}


//容器收缩
void test02()
{
	vector<int> v1;
	for (int i = 0; i < 100; i++)
	{
		v1.push_back(i);
	}
	cout<< "v1空间 = " << v1.capacity() << endl;
	cout<< "v1长度 = " << v1.size() << endl;
	
	v1.resize(10);//长度收缩至10  但 空间不变 
	cout << "v1空间 = " << v1.capacity() << endl;
	cout << "v1长度 = " << v1.size() << endl;

	//v1.swap(v1);   不改变
	vector<int>(v1).swap(v1);
	cout << "v1空间 = " << v1.capacity() << endl;
	cout << "v1长度 = " << v1.size() << endl;
}

int main()
{
	test01();
	test02();
	return 0;
}
```



互换空间的底层原理需要注意，匿名内部类的使用，互换空间代码简析

![image-20240406193636318](C:\Users\hxd15\Desktop\Repository\GithubRepository\assets\image-20240406193636318.png)





#### 预留空间

```C++
void test01()
{
	vector<int> v1;
	//v1.reserve(100000);   前后的区别
	int count = 0;
	int* p = NULL;
	for (int i = 0; i < 100000; i++)
	{
		v1.push_back(i);
		//每次扩容，首地址都会发生改变，因为扩容是重新寻找一块空间进行开辟
		if (p != &v1[0])
		{
			count++;
			p = &v1[0];
		}
	}
	cout << "v1空间 = " << v1.capacity() << endl;
	cout << "v1长度 = " << v1.size() << endl;
	cout << "扩容次数: " << count << endl;
}

int main()
{
	test01();
	return 0;
}
```



#### 总结

函数集合

```C++
// 构造
vector<T> v;                  //采用模板实现类实现，默认构造函数
vector(v.begin(), v.end());   //将v[begin(), end())区间中的元素拷贝给本身。
vector(n, elem);              //构造函数将n个elem拷贝给本身。
vector(const vector &vec);    //拷贝构造函数。
```

```C++
// 赋值
vector& operator=(const vector &vec);//重载等号操作符
assign(beg, end);     //将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);      //将n个elem拷贝赋值给本身。
```

```C++
//容量和大小
empty();                    //判断容器是否为空
capacity();                 //容器的容量
size();                     //返回容器中元素的个数
resize(int num);            //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。
resize(int num, elem);      //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。

//如果容器变短，则末尾超出容器长度的元素被删除
```

```C++
// 插入和删除
push_back(ele);                                          //尾部插入元素ele
pop_back();                                              //删除最后一个元素
insert(const_iterator pos, ele        //迭代器指向位置pos插入元素ele
insert(const_iterator pos, int count,ele);//迭代器指向位置pos插入count个元素ele
erase(const_iterator pos);         //删除迭代器指向的元素
erase(const_iterator start, const_iterator end);
//删除迭代器从start到end之间的元素
clear();                         //删除容器中所有元素
```

```C++
//  数据存取
at(int idx);
operator[];
front();
back();
```

```C++
// 互换空间
swap(vec);
//收缩空间
vector<int> (v1).swap(v1);

//预留空间
reserve(int )
```

