
## const常规用法

### 常量指针与指针常量

国内的很多教材、教程里常常提到常量指针和指针常量，很多公司面试的时候也会问到区别。乍一看好像很绕，其实也不难，下面的例子将具体的讲解一下。


char greeting[] = "Hello";
char* p = greeting;                 // non-const pointer, non-const data
const char* p = greeting;           // non-const pointer, const data
char* const p = greeting;           // const pointer, non-const data
const char* const p = greeting;     // const pointer, const data


对于上述的第一种情况，即如果被指物是常量的话，有些程序员会将关键字const写在类型之前，有些人会把它写在类型之后、星号之前。两种写法的意义相同，所以下列两个函数接受的参数类型是一样的:

void f1(const Widget* pw);
void f2(Widget const * pw);


参考
[C++为什么要尽可能地使用const(Effective C++)](https://blog.csdn.net/Bubbler_726/article/details/106338666)
