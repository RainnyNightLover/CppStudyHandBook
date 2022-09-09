## 5 char* 与char[]

c++代码里头经常见到char * 与char []的写法，这两种写法都可以表示一个字符串。比如：

```cpp
void charcode() {
    char* a = "c1";
    char b[] = "c2";
    printf("a=%s, b=%s", a, b);
}
```
输出
```cpp
a=c1, b=c2
```

char * 这种方式表示常量指针，char[] 这种表示指针常量！

### char *的方式

我们看看char* a =“c1” 这行代码到底执行了哪些操作： 1.声明了一个char*的标量，也就是声明了一个指向char的指针。 2.在内存的文字常量区，开辟了一个空间存储字符串常量"c1"。
3.返回这个区域的地址，并且这个地址作为值给了字符指针变量a。

最终的结果就是：指针变量a指向了一个字符串常量"c1"。

因为”c1“是在常量区，所以其内容是不可以修改的。如果我们试图修改a指向的内存区域值，程序会崩溃

```cpp
void charcode() {
char* a = "c1";
*(a+1) = '2';
cout<<a<<endl;
}
```
此时代码会报错崩溃
```cpp
/bin/sh: line 1: 48910 Bus error: 10
.......
```
但是a是个指针变量，其指向的地址是可以修改的，因此如下代码可以正常运行

```cpp
void charcode() {
    char* a = "c1";
    a = "c2";
    printf("a=%s", a);
}
```
输出为`：

```cpp
a=c2
```

### char[] 的方式

下面我们再来看看char[]的方式

说起char[]，得从C语言说起。string本质上是个类，而C语言是面向过程的编程方式，里面没有class，因此C语言中的string其实是个字符数组，也就是char[]。例如

```cpp
char str[6] = {"hello"};
cout<<str<<endl;
cout<<strlen(str)<<endl;
cout<<sizeof(str)<<endl;
```

上面定义了一个有6个元素的数组，元素类型为字符char，存储的字符为"h e l l o \0"。加上\0的目标，是代表空格符，在字符串末尾加上"\0”, 表示字符串结束，堵到这个位置就会停下来，不然会一直读下去。

上面的代码输出为

```cpp
hello
```

其中，strlen是去掉"\0"以外的长度，sizeof则包括"\0"。

为什么cout <<str能读取到字符串hello？

因为在C语言中规定，数组名就代表数组所在内存位置的首地址，也就是str[0]的地址，即str=&str[0]，因此读取str的时候其实就是在访问hello中h的地址。

C语言中操作字符串是通过它在内存中的存储单元的首地址进行的，这是字符串的本质。

```cpp
char str[6] = {"hello"};
```

这行代码实现了2个操作：

1.声明了一个char类型的数组str。

2.给str数组赋值，即将"hello"中的每一个字符分别赋值给数组中的每一个元素并存储在栈上，数组位置不够的字符以"\0"填充。

结合上面的分析，我们不难发现：

str指向的数组地址是不能发生变化的。

比如我们如果这么操作

```cpp
char str[6] = {"hello"};
str = "world";
```

第二行在IDE里会报错并提示：表达式必须是可修改的左值。

但是，对应的数组里面的内容是可以修改的：

```cpp
void charcode() {
    char str[6] = {"hello"};
    str[0] = 'H';
    cout<<str<<endl;
}
```

上述代码就可以正常运行，并输出结果为"Hello"。

4.char *更严谨的写法

事实上，char *p = "hello"; 这种写法是不严谨的，比如在IDE运行的时候，会报如下的warning信息：

```cpp
conversion from string literal to 'char *' is deprecated [-Wc++11-compat-deprecated-writable-strings]
char *p = "hello";
^
1 warning generated.
```

原因也很好理解：因为*p指向的是一个常量，一旦strcpy(p,”world”)就坏了，此时会视图往只读区写入一个新值，程序就会崩溃！所以我们应该还是按照类型相同赋值的原则来写：

```cpp
const char *p = "hello";
```

这样就不会出现问题。

### 小结

1.char *p = "hello"，p指针指向的值是可以变化的，但是p只能指向（字符串）常量，并且其指向的内存范围里内容不能发生改变。规范写法为const char *p或者char const *p，p为一个常量指针。 2.char
a[]这种写法，a的内存地址即指向不能变，但是内存里的内容能发生变化，等同于char *const a，为一个指针常量。 3.const在*前：表示const修饰的为所申明的类型，为常量指针 4.const在*
后：表示const修饰的为指针，为指针常量

## 参考

[char * 与char []区别总结](https://blog.csdn.net/bitcarmanlee/article/details/124166842)