
## int指针与char指针对比

这里对于指针及采用特定引导符引导之后的含义做一些验证。

所有不带特殊引导符的打印，都是打印该变量中的真实存储的值，因此指针变量打印的就是指针中存储的指向的那个变量的地址；*p则是递进了一步，打印了那个地址中的值；&p则是抽象了一步，打印了本指针内存的地址；

### int* 指针

演示程序
```cpp
#include<iostream>
using namespace std;
int main(){
    int a = 300;
    int* p = &a; // 定义的是 int* 指针类型的变量p
    int** pp = &p; // 定义的是 int** 指向指针类型的指针变量pp
    
    cout << *p << endl; // *为解引用符，取出p所指向的内存地址内储存的变量值
    cout << p << endl; // 变量a在内存中的储存地址
    cout << &p << endl; // 指针变量p在内存中的储存地址
    cout << pp << endl; // 指向指针的指针变量中存储的值，即p的内存地址；
    cout << *pp << endl; // 可以递归抽象，p的地址中存储的值，即a的内存地址；
}
```
输出
```
300
0x7ffee73b0b9c
0x7ffee73b0b90
0x7ffee73b0b9c
0x7ffee73b0b90
```

### char* 类型

演示程序
```cpp
#include<iostream>
#include<string>
using namespace std;
int main(){
  char a = 'a';
  char* p = &a; // 定义的是 char* 指针类型的变量p
  cout << *p << endl; 
  cout << p << endl;
}
```
输出
```
a
a�%��
```
同样的代码，为何当指针变成 char* 类型时无法打印出指针p的内容（也就是 char a 在内存中的储存地址）？

这是因为在 C++ 中，operator <<在解析 char* 类型的变量时会将其解释为 C string 类型，然后在打印的时候打印出 char 类型的结果而不是其地址。
如果想要得到 char 类型变量在内存中储存的地址，需要对指针进行类型的转换，将 char* 类型的指针转换为 void* 类型的指针。

演示程序
```cpp
#include<iostream>
#include<string>
using namespace std;
int main(){
  char a = 'a';
  char* p = &a; // 定义的是 char* 指针类型的变量p
  cout << *p << endl; 
  cout << (void*)p << endl;
}
```
输出
```
a
0x7ffee0ba0b9f
```