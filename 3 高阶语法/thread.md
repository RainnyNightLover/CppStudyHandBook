
## thead

### join()与detach()
在声明一个std::thread对象之后，都可以使用detach和join函数来启动被调线程，区别在于两者是否阻塞主调线程。

（1）当使用join()函数时，主调线程阻塞，等待被调线程终止，然后主调线程回收被调线程资源，并继续运行；

（2）当使用detach()函数时，主调线程继续运行，被调线程驻留后台运行，主调线程无法再取得该被调线程的控制权。当主调线程结束时，由运行时库负责清理与被调线程相关的资源。

#### detach()
detach调用之后，目标线程就成为了守护线程，驻留后台运行，与之关联的std::thread对象失去对目标线程的关联，无法再通过std::thread对象取得该线程的控制权。当线程主函数执行完之后，线程就结束了，运行时库负责清理与该线程相关的资源。

当一个thread对象到达生命期终点而关联线程还没有结束时，则thread对象取消与线程之间的关联，目标线程线程则变为分离线程继续运行。

当调用join函数时，调用线程阻塞等待目标线程终止，然后回收目标线程的资源。

detach是使主线程不用等待子线程可以继续往下执行，但即使主线程终止了，子线程也不一定终止。

```
#include <iostream>
#include <thread>
using namespace std;

void func(){
    for (int i = -10; i > -20; i--)
    {
        cout << "from func():" << i << endl;
    }
}

//主线程
int main(){
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    thread t(func);	//子线程
    t.detach();		//分离子线程
    return 0;
}
```
运行结果:子进程还没来得及运行就已经结束了
```
#include <iostream>
#include <thread>
using namespace std;

void func(){
    for (int i = -10; i > -20; i--){
        cout << "from func():" << i << endl;
        }
    }
//主线程
int main(){
    thread t(func);	//子线程
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    t.detach();		//分离子线程
    return 0;
}
```
抓了一个子线程运行的截图，但是数字还没有来的及打印输出程序就已经结束了

detach()函数是子线程的分离函数，当调用该函数后，线程就被分离到后台运行，主线程不需要等待该线程结束才结束

#### join()
join()函数是一个等待线程完成函数，主线程需要等待子线程运行结束了才可以结束

（1）谁调用了这个函数？调用了这个函数的线程对象，一定要等这个线程对象的方法（在构造时传入的方法）执行完毕后（或者理解为这个线程的活干完了！），这个join()函数才能得到返回。

（2）在什么线程环境下调用了这个函数？上面说了必须要等线程方法执行完毕后才能返回，那必然是阻塞调用线程的，也就是说如果一个线程对象在一个线程环境调用了这个函数，那么这个线程环境就会被阻塞，直到这个线程对象在构造时传入的方法执行完毕后，才能继续往下走，另外如果线程对象在调用join()函数之前，就已经做完了自己的事情（在构造时传入的方法执行完毕），那么这个函数不会阻塞线程环境，线程环境正常执行。
```
#include <iostream>
#include <thread>
using namespace std;

void func(){
    for (int i = -10; i > -20; i--){
        cout << "from func():" << i << endl;
    }
}

//主线程
int main(){
    thread t(func);	//子线程
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    cout << "mian()" << endl;
    t.join();		//等待子线程结束后才进入主线程
    return 0;
}
```

参考
[C++11 std::thread detach()与join()用法总结](https://blog.csdn.net/weixin_44862644/article/details/115765250)
