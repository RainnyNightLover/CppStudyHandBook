
## =delete

### 禁止调用

C++11中，当我们定义一个类的成员函数时，如果后面使用"=delete"去修饰，那么就表示这个函数被定义为deleted，也就意味着这个成员函数不能再被调用，否则就会出错。
```
#include <cstdio>


class TestClass{
    public:
    int func(int data)=delete;
};


int main(void){

    TestClass obj;
    obj.func(100);

    return 0;
}
```
编译时直接报错，如下，
```
Scanning dependencies of target cppStudy
gmake[3]: Leaving directory '/Users/yangxing/CLionProjects/cppStudy/cmake-build-debug'
gmake[3]: Entering directory '/Users/yangxing/CLionProjects/cppStudy/cmake-build-debug'
[ 33%] Building CXX object CMakeFiles/cppStudy.dir/main.cpp.o
/Users/yangxing/CLionProjects/cppStudy/main.cpp:15:9: error: attempt to use a deleted function
obj.func(100);
^
/Users/yangxing/CLionProjects/cppStudy/main.cpp:7:9: note: 'func' has been explicitly marked deleted here
int func(int data)=delete;
^
1 error generated.
gmake[3]: *** [CMakeFiles/cppStudy.dir/build.make:63: CMakeFiles/cppStudy.dir/main.cpp.o] Error 1
gmake[3]: Leaving directory '/Users/yangxing/CLionProjects/cppStudy/cmake-build-debug'
gmake[2]: *** [CMakeFiles/Makefile2:76: CMakeFiles/cppStudy.dir/all] Error 2
gmake[2]: Leaving directory '/Users/yangxing/CLionProjects/cppStudy/cmake-build-debug'
gmake[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/cppStudy.dir/rule] Error 2
gmake[1]: Leaving directory '/Users/yangxing/CLionProjects/cppStudy/cmake-build-debug'
gmake: *** [Makefile:118: cppStudy] Error 2
```
在C++11之前，当我们希望一个类不能被拷贝，就会把构造函数定义为private，但是在C++11里就不需要这样做了，只需要在构造函数后面加上=delete来修饰下就可以了。

### 巧妙用法-防止隐式类型转换
这里说个=delete的巧妙用法，在C++里会有很多隐式类型转换，如下代码，
```
#include <cstdio>

class TestClass{
public:
    void func(int data) { printf("data: %d\n", data); }
};


int main(void){

    TestClass obj;
    obj.func(100);
    obj.func(100.0);

    return 0;
}
```
输出如下，

当我们把100.0传给obj.func()时，发生了隐式类型转换，由double转为了int，有时我们不希望发生这样的转换，我们就是希望传进来的参数和规定的类型一致，那么此时可以使用=delete来达到这个目的，如下，
```
#include <cstdio>

class TestClass{
public:
    void func(int data) { printf("data: %d\n", data); }
    void func(double data)=delete;
};


int main(void) {

    TestClass obj;
    obj.func(100);
    obj.func(100.0);

    return 0;
}
```
我们把参数类型是double的重载函数加上=delete进行修饰，表示这个函数被删除，那么用户就不能使用这个函数了，这样再编译就会出错，


参考

[C++11中=delete的巧妙用法](https://blog.csdn.net/whahu1989/article/details/90648536)
