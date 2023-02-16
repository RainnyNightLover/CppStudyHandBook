
## gflags

gflags支持的类型有bool，int32，int64，uint64，double和string。可以说这些基本类型大体上满足了我们的需求。
```
DEFINE_bool: boolean
DEFINE_int32: 32-bit integer
DEFINE_int64: 64-bit integer
DEFINE_uint64: unsigned 64-bit integer
DEFINE_double: double
DEFINE_string: C++ string
```
比如上文中，我就定义了confPath, port, daemon三个命令行参数，回顾一下：
```
DEFINE_string(confPath, "../conf/setup.ini", "program configure file.");
DEFINE_int32(port, 9090, "program listen port");
DEFINE_bool(daemon, true, "run daemon mode");
```
稍微讲解一下：
第一个字段 confPath就是命令行里要输入的参数名，比如 –confPath=./love.ini
第二个字段”…/conf/setup.ini”，就是如果命令行里没指定这个参数，那默认值就是 …/conf/setup.ini
第三个字段”program configure file.”，就是这个参数的帮助说明信息，当用户输入 –hlep 的时候，会显示出来。

代码中使用这个变量
以前我们使用getopt_long函数来自己解析命令行参数的时候，都得内部定义一个变量来保存从命令行得到的值。后续就可以使用这个变量来完成相应的代码逻辑。那其实，DEFINE_string等“指令”就相当于定义了变量，只不过变量名多了个前缀 “FLAGS_“。即，我们可以在代码里面直接操作FLAGS_confPath，FLAGS_port，FLAGS_port，FLAGS_daemon这三个变量。

### 1 解析命令行参数
gflags是使用ParseCommandLineFlags这个方法来完成命令行参数的解析的。具体如下
```
gflags::ParseCommandLineFlags(&argc, &argv, true);
```
一目了然，唯一值得注意的就是第三个参数了。如果设置为true，gflags就会移除解析过的参数，只保留不是gflags定义的参数, 即argc, argv就会变了。否则使用false时, gflags还会保持这些参数继续留在argc，argv中。但是参数的顺序有可能会发生变化。
一般参数解析后，也没有保留的必要，设置为true就好了。

如果不好理解的话，没关系，来一段代码就明白什么意思了。
```
#include <iostream>
#include <gflags/gflags.h>

using namespace std;

DEFINE_string(confPath, "../conf/setup.ini", "program configure file.");
DEFINE_int32(port, 9090, "program listen port");
DEFINE_bool(daemon, true, "run daemon mode");

int main(int argc, char** argv) {

    for (int i = 0; i < argc; i++) {
        printf("argv[%d] = %s\n", i, argv[i]);
    }
    printf("---------------here--------------\n");
    
    gflags::SetVersionString("1.0.0.0");
    gflags::SetUsageMessage("Usage : ./demo ");
    gflags::ParseCommandLineFlags(&argc, &argv, true);
    
    for (int i = 0; i < argc; i++) {
        printf("argv[%d] = %s\n", i, argv[i]);
    }
    printf("---------------there--------------\n");
    
    cout << "confPath = " << FLAGS_confPath << endl;
    cout << "port = " << FLAGS_port << endl;
    
    if (FLAGS_daemon) {
        cout << "run background ..." << endl;
    } else {
        cout << "run foreground ..." << endl;
    }
    cout << "good luck and good bye!" << endl;
    gflags::ShutDownCommandLineFlags();
    
    return 0;
}
```
运行后，看一下true的情况：
```
[amcool@leoox build]$ ./demo --port=8888 --confPath=./happy.ini --daemon
argv[0] = ./demo
argv[1] = --port=8888
argv[2] = --confPath=./happy.ini
argv[3] = --daemon
---------------here--------------
argv[0] = ./demo
---------------there--------------
confPath = ./happy.ini
port = 8888
run background ...
good luck and good bye!
```
修改为false，在运行一下的情况：这里顺序没有发生变化，但是毕竟说的是可能变化。
```
[amcool@leoox build]$ ./demo --port=8888 --confPath=./happy.ini --daemon  
argv[0] = ./demo
argv[1] = --port=8888
argv[2] = --confPath=./happy.ini
argv[3] = --daemon
---------------here--------------
argv[0] = ./demo
argv[1] = --port=8888
argv[2] = --confPath=./happy.ini
argv[3] = --daemon
---------------there--------------
confPath = ./happy.ini
port = 8888
run background ...
good luck and good bye!
```

### 2 参数检查
按照以前的习惯，我们可以获取到所有参数的值后，再在代码里面进行判断这个参数是否是我们想要的。比如，我们需要端口是36800 到 36888之间的，那我们可以这样检查。
```
if (FLAGS_port < 36800 || FLAGS_port > 36888) {
    printf("port must [36800, 36888]\n");
    return -1;
}
```
当然gflags里面建议使用 RegisterFlagValidator 这个方法来做参数检查。参数不通过的时候，程序是启动失败的。
```
static bool ValidatePort(const char* flagname, gflags::int32 value) {
    if (value >= 36800 && value <= 36888) {
    printf("param(%s) = (%d) is valid!\n", flagname, value);
    return true;
    }

    printf("param(%s) = (%d) is invalid!\n", flagname, value);
    return false;
}
DEFINE_int32(port, 36810, "program listen port");
static const bool validPort = gflags::RegisterFlagValidator(&FLAGS_port, &ValidatePort);
```
运行一下，看看效果：
```
[amcool@leoox build]$ ./demo --port=36889 --confPath=./happy.ini --daemon
param(port) = (36889) is invalid!
param(port) = (36810) is valid!
ERROR: failed validation of new value '36889' for flag 'port'
```
### 其他函数作用

#### 帮助信息
gflags::SetUsageMessage函数用于设置命令行帮助信息。
```
// Usage message.
gflags::SetUsageMessage("command line brew\n"
                        "usage: caffe <command> <args>\n\n"
                        "commands:\n"
                        "  train           train or finetune a model\n"
                        "  test            score a model\n"
                        "  device_query    show GPU diagnostic information\n"
                        "  time            benchmark model execution time");
```
设置帮助信息后，当参数错误或加 -help选项是可以打印帮助信息
```
$ ./build/tools/caffe
    caffe: command line brew
    usage: caffe <command> <args>
    
    commands:
    train           train or finetune a model
    test            score a model
    device_query    show GPU diagnostic information
    time            benchmark model execution time
```
...
#### 版本信息
gflags::SetVersionString用于设置版本信息
```
// Set version
gflags::SetVersionString(AS_STRING(CAFFE_VERSION));
```
这里的CAFFE_VERSION是在Makefile中定义的

当命令行参数加 -version 时会打印版本信息
```
$ ./build/tools/caffe -version
    caffe version 1.0.0-rc3
```
–version：显示版本信息。

–help：显示所有帮助信息，包括自定义的帮助信息和gflags本身的帮助信息。

–helpshort：显示自定义的帮助信息。

#### 释放解析参数过程中分配的空间
-void ShutDownCommandLineFlags()释放解析参数过程中分配的空间


[gflags-命令行参数处理](https://blog.csdn.net/qq_39523365/article/details/110266121)

gflags-命令行参数处理
https://blog.csdn.net/red98/article/details/103727063