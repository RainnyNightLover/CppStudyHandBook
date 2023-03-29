# 01 default

## 1 构造函数

### 定义
构造函数是特殊的成员函数，需要注意的是，构造函数虽然名称叫构造，但是构造函数的主要任务并不是开空间创建对象，而是初始化对象。

其特征如下：

函数名与类名相同
无返回值
对象实例化时编译器自动调用对应的构造函数
构造函数可以重载
如果类中没有显式定义构造函数，则C++编译器会自动生成一个无参的默认构造函数，一旦用户显式定义编译器将不再生成
无参的构造函数和全缺省的构造函数都称为默认构造函数，并且默认构造函数只能有一个。
### 示例代码
说明，构造函数。
```cpp
#include <iostream>

using namespace std;

class Date {
private:
    int _year;
    int _month;
    int _day;

public:
    Date(int year = 2022, int month = 9, int day = 27) // 构造函数推荐使用缺省
    {
        cout << "构造函数调用" << endl;
        _year = year;
        _month = month;
        _day = day;
    }

//    Date();

    Date(int year, int month) // 构造函数推荐使用缺省
    {
        cout << "构造函数调用" << endl;
        _year = year;
        _month = month;
        _day = 10;
    }

    void Print() {
        cout << _year << "/" << _month << "/" << _day << endl;
    }
};

int main() {
    // 构造函数调用方式场合
    Date d1;
    d1.Print();

    Date d2(2022, 1, 15);
    d2.Print();

    return 0;
}

```   

```cpp
#include <iostream>

using namespace std;

class Date {
private:
    int _year;
    int _month;
    int _day;

public:

    Date() = default;

    Date(int year, int month) // 构造函数推荐使用缺省
    {
        cout << "构造函数调用" << endl;
        _year = year;
        _month = month;
        _day = 10;
    }

    void Print() {
        cout << _year << "/" << _month << "/" << _day << endl;
    }
};

int main() {
    // 构造函数调用方式场合
    Date d1;
    d1.Print();

    return 0;
}
```  

## 2 析构函数

### 定义
析构函数是特殊的成员函数，其特征如下：

析构函数名是在类名前加上字符 ~。
无参数无返回值类型。
一个类只能有一个析构函数。若未显式定义，系统会自动生成默认的析构函数。注意：析构函数不能重载
对象生命周期结束时，C++编译系统系统自动调用析构函数
对于内置类型析构函数不会做处理，析构函数只对自定义类型做处理
如果类中没有申请资源时，析构函数可以不写，直接使用编译器生成的默认析构函数。像malloc、new这种申请的空间，就需要写析构函数。
### 示例代码
说明，析构函数。
```cpp
#include <iostream>
using namespace std;
class Date
{
public:
    Date(int year = 1, int month = 1, int day = 1)
    {
        _year = year;
        _month = month;
        _day = day;
    }

    void Print()
    {
        cout << _year << "-" << _month << "-" << _day << endl;
    }

    ~Date()
    {
        // Date类没有资源需要清理，所以不实现析构函数是可以的
    }
private:
    int _year;
    int _month;
    int _day;
};

class Stack
{
public:
    Stack(int capacity = 4)
    {
        _a = (int*)malloc(sizeof(int) * capacity);
        if (_a == nullptr)
        {
            cout << "malloc fail\n" << endl;
            exit(-1);
        }

        _top = 0;
        _capacity = capacity;
    }

    void push()
    {}

    ~Stack()
    {
        cout << "~Stack\n" << endl;
        free(_a);
        _a = nullptr;
        _top = _capacity = 0;
    }

private:
    int* _a;
    int _top;
    int _capacity;
};
int main()
{
    Date d1;
    d1.Print();

    // 析构函数先s2后s1
    Stack s1(20);
    Stack s2(30);

    return 0;
}
```

## 3 拷贝构造函数

### 定义
拷贝构造函数也是特殊的成员函数，其特征如下：

拷贝构造函数是构造函数的一个重载形式。
拷贝构造函数的参数只有一个且必须是类类型对象的引用，使用传值方式编译器直接报错，引发无穷递归调用（因为传值传参是对实参进行拷贝然后赋给形参，我们写的是拷贝构造函数，结果还要调用拷贝构造函数，所以就会套娃）。
### 示例代码
说明，拷贝构造函数。
```cpp
#include <iostream>

using namespace std;

class Date
{
public:
    // 构造函数
    Date(int year = 1, int month = 1, int day = 1)
    {
        _year = year;
        _month = month;
        _day = day;
    }

    // 拷贝构造函数
    // 如果这块不用引用，而使用传值拷贝的话，会一层一层调用这个拷贝构造函数，套娃了，我穷无尽递归
    // 使用引用的话，就不需要再调用拷贝构造函数，就不会套娃
    // 最好加上const
    Date(const Date& d)
    {
        _year = d._year;
        _month = d._month;
        _day = d._day;
    }

    void Print()
    {
        cout << _year << "-" << _month << "-" << _day << endl;
    }

    ~Date()
    {
        // Date类没有资源需要清理，所以不实现析构函数是可以的
    }
private:
    int _year;
    int _month;
    int _day;
};

int main()
{
    Date d1(2022, 1, 15);

    // 拷贝复制
    Date d2(d1);
    d2.Print();

    return 0;
}
```
若未显式定义，编译器会生成默认的拷贝构造函数。 默认的拷贝构造函数对象按内存存储按字节序完成拷贝，这种拷贝叫做浅拷贝，或者值拷贝。
浅拷贝可能会引发程序崩溃。例如，栈不写拷贝构造函数程序会崩溃，因为默认的拷贝构造函数是逐字节拷贝，会导致将指针拷贝过来，两个对象中的指针指向同一个内存。
```cpp
#include <iostream>

using namespace std;

class Stack
{
public:
    Stack(int capacity = 4)
    {
        _a = (int*)malloc(sizeof(int) * capacity);
        if (_a == nullptr)
        {
            cout << "malloc fail\n" << endl;
            exit(-1);
        }

        _top = 0;
        _capacity = capacity;
    }

    void push()
    {}

    ~Stack()
    {
        free(_a);
        _a = nullptr;
        _top = _capacity = 0;
    }

private:
    int* _a;
    int _top;
    int _capacity;
};

int main()
{
    /* 栈不写拷贝构造函数程序会崩溃，因为默认的拷贝构造函数是逐字节拷贝，会导致将指针拷贝过来，两个对象中的指针指向同一个内存
//    Stack s1(10);
//    Stack s2(s1);
    */
    Stack s1(10);
    Stack s2(s1);
    return 0;
}
```


## 4 运算符重载

### 定义
C++为了增强代码的可读性引入了运算符重载，运算符重载是具有特殊函数名的函数，也具有其返回值类型，函数名字以及参数列表，其返回值类型与参数列表与普通的函数类似。
函数名字为：关键字operator后面接需要重载的运算符符号。
函数原型：返回值类型 operator操作符(参数列表)

注意：

不能通过连接其他符号来创建新的操作符：比如operator@
重载操作符必须有一个类类型参数
用于内置类型的运算符，其含义不能改变，例如：内置的整型+，不能改变其含义
作为类成员函数重载时，其形参看起来比操作数数目少1，因为成员函数的第一个参数为隐藏的this
.* :: sizeof ?: . 注意以上5个运算符不能重载。
### 示例代码
说明，析构函数。
```cpp
#include <iostream>

using namespace std;

class Date
{
public:
    // 构造函数
    Date(int year = 1, int month = 1, int day = 1)
    {
        _year = year;
        _month = month;
        _day = day;
    }

    void Print()
    {
        cout << _year << "-" << _month << "-" << _day << endl;
    }

    // 运算符重载函数写在类内，d1和d2比较大小应该是d1.operator(d2)，
    // 写在类内成员函数会生成d1对象的this指针，所以要修改重载函数，原先的写法相当于d1重复写了

    // bool operator>(const Date& d1, const Date& d2)  原先的写法

    /*bool operator>(const Date& d2)
    {
        if (this->_year > d2._year)
        {
            return true;
        }
        else if (this->_year == d2._year && this->_month > d2._month)
        {
            return true;
        }
        else if (this->_year == d2._year && this->_month == d2._month && this->_day > d2._day)
        {
            return true;
        }
        else
        {
            return false;
        }
    }*/

    // 实际上就是按照上面的代码在执行的，但是this指针默认生成的，我们一般不写出来
    // 如下的话就是_year <==> this->_year , d是d2的别名
    bool operator>(const Date& d)
    {
        if (_year > d._year)
        {
            return true;
        }
        else if (_year == d._year && _month > d._month)
        {
            return true;
        }
        else if (_year == d._year && _month == d._month && _day > d._day)
        {
            return true;
        }
        else
        {
            return false;
        }
    }



private:
    int _year;
    int _month;
    int _day;
};

/* 运算符重载写在类外面，会出现不能访问私有成员变量的问题，所以写在类内
// 函数名：operator+操作符，
// 返回类型：看操作符运算后返回的值
// 参数： 操作符有几个操作数，他就有几个参数
bool operator>(const Date& d1, const Date& d2)
{
	if (d1._year > d2._year)
	{
		return true;
	}
	else if (d1._year == d2._year && d1._month > d2._month)
	{
		return true;
	}
	else if (d1._year == d2._year && d1._month == d2._month && d2._day > d2._day)
	{
		return true;
	}
	else
	{
		return false;
	}
}
*/

int main()
{
    Date d1(2022, 2, 3);
    Date d2(2022, 8, 23);
    Date d3(2022, 9, 3);


    // 写在类内的调用方法
    d1 > d2; // 先在类中寻找运算符重载函数，找不到再到全局寻找
    d1.operator>(d2); // 在类中找到运算符重载函数这样调用

    cout << (d1 > d2) << endl;

    /* 重载函数 写在类外 的调用方法
    // 加括号是因为运算符的优先级问题
    cout << (d1 > d2) << endl; // 会调用函数operator>()
    // 上下是等价的
    cout << operator>(d1, d2) << endl;
    */

    return 0;
}
```

### 赋值运算符重载
赋值运算符重载格式
参数类型：const T&，传递引用可以提高传参效率
返回值类型：T&，返回引用可以提高返回的效率，有返回值目的是为了支持连续赋值
检测是否自己给自己赋值
返回*this ：要符合连续赋值的含义
一个类如果没有显式定义赋值运算符，编译器会自己生成一个
默认生成的赋值运算符和拷贝构造函数做的事情完全类似
内置类型成员会完成字节序值拷贝
自定义类型成员变量，会调用它的operator=
### 示例代码
说明，析构函数。
```cpp
class Date
{
public:
	// 构造函数
	Date(int year = 1, int month = 1, int day = 1)
	{
		_year = year;
		_month = month;
		_day = day;
	}

	// 拷贝构造函数
	Date(const Date& d)
	{
		cout << "const Date&" << endl; // 测试用，不能正常拷贝的
	}
	void Print()
	{
		cout << _year << "-" << _month << "-" << _day << endl;
	}

	// 赋值运算符重载
	// 
	// 因为this是指向一个对象的指针，出了作用域这个对象还在，所以这里可以使用引用做返回值
	// 如果以Date作为返回值，那么会调用拷贝构造函数创建一个形参然后作为返回值，所以使用引用做返回值更好
	// 传引用返回的就是调用重载的对象
	Date& operator=(const Date& d)
	{
		// this是当前对象的指针，&d是要赋值给当前对象的对象的地址，
		// 判断相等的话说明是当前对象赋值给自己，所以可以不用执行赋值过程,提高效率
		if (this != &d)
		{
			_year = d._year;
			_month = d._month;
			_day = d._day;
		}

		// 所以this是存在的
		return *this; // 如果不传引用作为返回值，那么会先调用拷贝构造函数拷贝一份*this对象，然后作为返回值返回
	}

private:
	int _year;
	int _month;
	int _day;
};


int main()
{
	Date d1(2022, 1, 1);
	Date d2(2022, 1, 2);
	Date d3(2022, 1, 3);

	// 一个已经存在的对象拷贝初始化一个马上创建实例化的对象
	Date d4(d1); // 拷贝构造

	Date d5 = d1; // 这个是拷贝构造，两个已经存在的对象赋值才是赋值重载

	// 赋值运算符重载
	d1 = d3;
	d1.Print();

	// i = j = k = 1 这个式子的语法是正确的，也就是说赋值是具有返回值的
	d1 = d3 = d2;
	d1.Print();

	return 0;
}

```

## 5 取地址及const取地址操作符重载
事实上，取地址及const取地址操作符重载，这两种默认函数需要我们显式实现的情况少之又少，编译器已经基本帮我们准备好了。所以我们简单了解一下即可。
### 示例代码
说明，取地址操作符重载。
```cpp
class Date
{
public:
	Date* operator&()    //取地址操作符重载
	{
		return this;
	}
 
	const Date* operator&()const    //const取地址操作符重载
	{
		return this;
	}
private:
	int _year; // 年
	int _month; // 月
	int _day; // 日
};
```

### reference

[C++_类中的6个默认成员函数](https://blog.csdn.net/weixin_43853178/article/details/127216070)