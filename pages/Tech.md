# 日常唠唠

## 没事就聊聊我踩过的那些坑，和一些笔记

少点比较，多些谦虚

会者不难✋✋✋

## 2022.4.4

### 无法打开源文件stdafx.h报错？

https://blog.csdn.net/h10666/article/details/105980636

解决方法如上

在vs2019中使用strcpy函数会报错，大意是说该函数不安全，建议使用"strcpy_s"函数
百度发现“strcpy_s"函数需要include “stdafx.h”,然后编译器就说无法打开源文件，解决方法是：

打开项目 ->项目属性（最后一个）-> C/C++ ->常规，在附加包含目录后面填入“$(ProjectDir)”(这里本来是空白)，确定之后删除 include “stdafx.h”

此时“strcpy_s”函数可以使用，具体原理不太明白，但确实可以

### 引用库为空？

如果引用过库为空，则需要在 属性 ->配置属性 ->常规 ->项目默认值 -> 公共语言运行时支持 ->公共语言运行时支持 /clr 则可以使用公共的库文件了


### TCP通信的三次握手

以我这里的NetServer_Qt.dll为例，展示TCP通信的三次握手
#### （0）NetMessage_Server_UDP|Ready|MacAddressList
#### （1）NetMessage_Server_UDP|Connect|ServerIP|ServerPort|MacAddress|RoomID|SeatID
#### （2）NetMessage_Server_TCP|Ready
#### （3）NetMessage_Client_UDP|Ready|MacAddress
#### （4）NetMessage_Client_TCP|Ready|ClientID|MacAddress
#### （5）NetMessage_Server_TCP|End
#### （6）NetMessage_Server_UDP|End
#### （7）NetMessage_Client_UDP|Abnormal|ClientID|MacAddress
#### （8）NetMessage_Server_UDP|Reconnect|ClientID
#### （9）NetMessage_Client_TCP|Reconnect|ClientID|MacAddress

Server端消息处理：
1.启动时发送 消息码（0）；每隔一段时间，检测存在未成功连接的客户端，发送 消息码（0）
2.接收到 消息码（3） 时，发送消息码（1）
3.接收到 消息码（4）或（9） 时，发送消息码（2），代表可进行TCP和UDP通信
4.退出时发送 消息码（5）和（6）

Client端消息处理：
1.启动时发送 消息码（3），每隔5秒发送一次
2.接收到 消息码（0）时，包含自己的MAC地址，发送 消息码（3）
3.接收到 消息码（1） 时，进行连接
4.连接成功时，发送 消息码（4）或（9）
5.接收到 消息码（2） 时，代表可进行TCP和UDP通信
6.接收到 消息码（5）或（6），断开连接，恢复初始化状态，每隔5秒发送 消息码（3）
 


### 制作和调用dll

#### Part 1 : 制作dll?

### 如何制作一个dll？

网上有很多教程，说的很明白了，我这里要从我踩过的这个坑引入。

我踩了个什么坑？先说下目标，我们主管要我把一个c++的类生成一个接口打包成dll并做两个demo，写个文档以供别人去调用，这是前提。

但是我在实际制作过程中却报错不允许使用链接规范，我思前想后很久，甚至我自己去做了一个很简单的加法dll，我自己是可以调用的，百思不得其解。原因是extern "C" _declspec(dllexport) 这个字段是不允许引用类内的方法的。

其实想想也很好理解，一个dll的接口，怎么可能是一个类内方法函数呢？也就是说怎么可能你都不需要声明，就可以直接把接口插到一个类内去引用？

#### 第一、我们通常可以制作一个专门的接口类，去调用类内方法，然后再通过这个extern "C" _declspec 语句来声明这个普通方法。这种方法比较方便，但是你毕竟不是调用了一个类，所以如果是内部连贯性较好的dll我建议不要用这种方法，毕竟说到底是没有一个完整的类的。

#### 第二、我们可以直接将整个类public关键字后加上"C" _declspec字段声明其为一个被引用的接口类，但是这种方法就是会遇到我之前说的那种问题，变量冲突，你总不能把那些东西全改了吧？你要是有那么严谨，你还来看我博客干吊呢？

还有一个问题就是，输入的变量不兼容。在我拿到手的这个c++项目文件，其实并不是严格的c++语言，而是一个公共语言库，也就是说经常会有用c#或者是别的语言的参数输入进去，但是这对于c++生成dll来说是无法容忍的，所以需要转变变量类型。

#### 然后我这里简单写一下制作dll时容易出问题的点:

#### 第一，要在Stdafx.h中将引用的标准库文件先声明好，然后将用到的库文件放入对应的文件夹

#### 第二，项目属性 -> 常规 ->MFC的使用->在共享DLL中使用MFC

#### 第三，注意一点，如果你需要将一个工程文件改成dll，那么它不能是一个单独的类的成员函数，只能是一个普通函数，否则就会报错，说不允许使用链接规范

#### 第四，注意如果是以c的方式转换dll extern "C" _declspec(dllexport)，如果输入的参数不是C自带的参数，比如vector 或者是System::String这种类型，可能会不兼容，只能输入char*或者是一些C自带的类型
在使用的时候可能需要向里面输入一些C的参数类型，所以在使用接口前必须进行转化，这也是为什么在调用接口的时候会大层套小层的，必须保证接口调用的正确性，否则dll没法识别输入参数


生成的时候，如果全局变量不明确，有可能是你的宏命令没有从.h文件传到.cpp文件中去，所以需要你手动将宏命令文件传到cpp中去
一种char*类型转String类型的方法：
System::String^ apppath = gcnew System::String(strAppPath);
System::String^ appname = gcnew System::String(strAppName);

### 如何调用？

我们从怎么制作一个dll中可以窥见，调用dll的方式也分为两种，一种是调用其中的类，一种是调用其中的方法

隐式地调用dll，如果是这样调用dll的话，就是所谓静态加载的方式，这样比较简单，需要.h.lib.dll三件套

假设我们需要调用的dll为testdll.dll，且有同名.h和.lib文件


显示链接是应用程序在执行过程中随时可以加载DLL文件，也可以随时卸载DLL文件，这是隐式链接所无法做到的，所以显示连接具有更好的灵活性，对于解释语言更为合适

首先我们要知道两种调用方式，以及各自的问题：
直接将整个类导出，或者导出全局方法。可以直接导出一个类的dll，带所有的参数和构造函数等等，但是这样做的话就必须要把整个类中的参数确定，也就是说我这里所用的所有符号都只能是C++的或者是C#的，而我到手的代码往往有可能是多语言的混合版本，拿到这种代码的时候就可能只能修改函数的参数，或者修改其

导出一个全局方法，就是再写一个cpp，不带命名空间和类标识，这样就可以通过 extern "C" _declspec (dllexport)表示来进行导出，作为一个方法接口供调用

首先，拿到一个空白的类，我们要进行一些库文件的设置
包含目录：对应include目录
库目录：对应lib的目录

链接器-> 输入 附加依赖项 输入需要用到的依赖项.lib

然后说一下生成dll常用的一个宏命令
举个例子
#ifndef _DLL_FIREWALL
#define DLL_FIREWALL extern "C" _declspec(dllexport)
#define DLL_FIREWALL_PURE _declspec(dllexport)
#else
#define DLL_FIREWALL extern "C" _declspec(dllimport)
#define DLL_FIREWALL_PURE _declspec(dllimport)

也就是说，同样是使用DLL_FIREWALL这个关键字，如果是在dll内调用，则会转换成上面两种形式，如果是在dll外调用，则会转换成下面两种形式

那么我们在具体声明的时候就可以用一下形式声明函数了：
#### DLL_FIREWALL bool AddApp();

在dll外调用同理，这里不过多赘述了


## 2022.4.4

### 博客的制作

简单说下这个博客是怎么制作的。

在这个页面里其实有比较详细的介绍[三分钟在Github上搭建个人博客](https://zhuanlan.zhihu.com/p/28321740)

之后就是编辑问题，其实这个博客的编辑直接在这个指定的.md 文件里进行编辑即可，在[Minimal](https://github.com/LeventureQys/minimal)中有关于在md中的文字编辑格式的问题，这个格式的编辑不像那种word的文本编辑，其实还是要用一些标识符去标识格式的，有点麻烦，不过谁让这又免费又不要米呢

然后就是左侧的导航栏的设置问题，这个设置可以参考一下我的设置:

theme: jekyll-theme-minimal
title: 轩先生大冒险
description:  一个有点菜的轮子发明家
logo : /asset/elieen.jpg

另外github是自带图床的，所以图片啥的不一定要转成图床再上传，直接传到github上即可
