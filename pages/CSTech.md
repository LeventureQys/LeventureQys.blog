# 日常唠唠

## 没事就聊聊我在c#上踩过的那些坑，和一些笔记

少点比较，多些谦虚

会者不难✋✋✋

原博:[轩先生大冒险](https://leventureqys.github.io/)

## 2022.4.19 

### datagridview 修改表头

dataGridView1.Columns[0].HeaderCell.Value = "编号";


## 2022.4.14

### 关于c#调用dll的方式，如何传char*类型

在我写的这个Firewall的这个项目里，有三个类，其他的东西先不管，暂时只讨论我在c#中调用dll遇到的问题

先说接口，一共两个，一个是bool FwStatus(void) 没什么好说的，检查当前防火墙是否开启的接口，如果开启返回true，未开启返回false,

另一个是

bool AddApp(char* strAppPath,char* strAppName) 

功能：将一个应用程序添加至防火墙例外

参数说明:

StrAppPath:该应用的路径，如C:\\Program Files (x86)\\LgSoftWAN\\LancooCNSC\\Teacher\\LBD.Frame.NetVoice.SIPServer.exe

两个方法在c#中的调用方法如此:

        [DllImport("Firewall.dll", CallingConvention = CallingConvention.Cdecl, CharSet = CharSet.Auto)]
        
        private static extern bool AddApp([MarshalAs(UnmanagedType.LPStr)]string strAppPath, [MarshalAs(UnmanagedType.LPStr)]string strAppName);

注意，因为AddApp中需要传入char*类型，但是在c#中并没有char*类型，如果直接向dll中传入string会导致乱码，因为char* 是单字节类型，而string是单双字节混用类型。在此需要在输入的string类型前加上[MarshalAs(UnmanagedType.LPStr)]进行修饰具体含义参考 [MarshalAs()UnmanagedType.LPStr](https://blog.csdn.net/qq_18975227/article/details/109818550)

在c++调用中则要注意这个.h文件，我之前没有修改这个.h文件所以会报错，而且不能说你输入一个const wchar_t* 类型，这个怎么说呢，这个是一个ASCII码的形式，是乱码也正常，我之前改成char*类型但是我忘了改.h文件了，其实并没有想象中那么复杂。





        [DllImport("Firewall.dll", CallingConvention = CallingConvention.Cdecl, CharSet = CharSet.Auto)]
        
        private static extern bool FwStatus();

详情见项目[Firewall.dll 带demo](https://github.com/LeventureQys/Firewall)

## 2022.4.13

### 未能找到类型或命名空间名的情况

具体链接 : [ C# 未能找到类型或命名空间名称的四种情况 ](https://blog.csdn.net/li_gege/article/details/103416210?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)

可能是由.NET版本不对导致的

## 2022.4.8

### 浅谈一下c#和c++的区别
C#和C ++的区别详细说明

#### 1、编译区别

C＃代码首先会被编译为CLR（公共语言运行库），然后由.NET框架解析。

C ++代码将会直接被编译为机器代码。

#### 2、内存管理的不同

C＃是在虚拟机上执行，会自动进行内存管理。

C ++需要手动组织管理内存。

#### 3、指针使用的区别

C＃不支持使用指针，但可以使用一些不安全的类和方法；

C ++允许使用指针。不用指针的C#比C++更安全。指针引起的内存泄漏啥的问题还是蛮费脑的。

#### 4、系统环境（平台）的区别

C＃主要用于Windows环境；

C ++是为基于Unix的系统设计的，但现在可以用于任何平台。

#### 5、应用上的区别

C＃的应用涉及基于Web的桌面和移动应用程序；

C ++的唯一目的就是创建独立的控制台应用程序。

#### 6、速度上的区别

C ++代码更快，因为它不使用重型库；

C＃较慢，因为它会产生开销并使用了类似于java这样的重型库。


### c#文件操作

#### 基本文件操作

参考博客 ： （最全的c#文件操作）[https://www.cnblogs.com/dongh/p/15429149.html]

(C#选择文件的对话框控件)[https://blog.csdn.net/zdyueguanyun/article/details/8622583]

        public string OpenFile()        
        {
            string strFileName = "";            
            OpenFileDialog ofd = new OpenFileDialog();            
            ofd.Filter = "Excel文件(*.xls;*.xlsx)|*.xls;*.xlsx|word文档(*.doc;*.docx)|*.doc;*.docx|所有文件|*.*";            
            ofd.ValidateNames = true; // 验证用户输入是否是一个有效的Windows文件           
            ofd.CheckFileExists = true; //验证路径的有效性           
            ofd.CheckPathExists = true;//验证路径的有效性           
            if (ofd.ShowDialog() == DialogResult.OK) //用户点击确认按钮，发送确认消息           
            {            
                strFileName = ofd.FileName;//获取在文件对话框中选定的路径或者字符串                
            }            
            return strFileName;           
        }        
        public string OpenFolder()        
        {        
            string sPath = "";            
            FolderBrowserDialog folder = new FolderBrowserDialog();            
            folder.Description = "选择文件所在文件夹目录";  //定义在对话框上显示的文            
            if (folder.ShowDialog() == DialogResult.OK)           
            {           
                sPath = folder.SelectedPath;                
            }           
            return sPath;            
        }

另外我突发奇想，觉得自己有点像个傻逼，就是从数据库中读取出来dataGridView之后，还可以用

dataGridView1.Columns[0].HeaderText = "工号/学号";

这样的指令去修改抬头，这样就不会显得很难看了

记得，要么在核心里写try catch，要么就每次都做一个空检测，总不能让报错成为常态吧。

## 2022.4.5

### dataset的使用

#### 从数据库中提取一个dataset

我们常会在c#中操作dataset来取数据库中的成员，我们希望让数据库成员以一个图表的形式绑定成一体，然后再从中取出我们需要的一些数据，那么我们该如何操作呢？

举个例子，我这里写了个从数据库中取出一个dataset的例子：

        public DataSet GetSqlDataset(string sql) //返回一个dataset，用于静态处理数据库，通常用于单纯的查询，这个字符串sql就是我们需要查询的对象
        
        {
        
        public MySqlConnection connection = new MySqlConnection(connString);
        
            try
            
            {
            
                if (connection.State == ConnectionState.Closed)
                
                    connection.Open();
                    

                MySqlDataAdapter sqlda = new MySqlDataAdapter(sql, connection);  //先要通过MySqlDataAdapter方法获得一个sqlAadpter
                
                DataSet dts = new DataSet(); //我们需要返回的dataset
                
                sqlda.Fill(dts, "lwx");  //注意这个Fill函数，第二个参数代表是表名，需要提前声明，如果不声明，可能就没法使用了，Dont know why,将来知道了可能回来更新？
                
                return dts;
                
            }
            
            catch (Exception ex)
            
            {
            
                MessageBox.Show(ex.ToString());
                
                return null;
                
            }
            
        }
        
       try catch是个编程的好习惯
       
根据上面的方法，我们就能返回得到一个从数据库中查询出来的dataset，我们可以做什么呢？

首先是一个个东西给它提取出来试试，比如我们这个dataset

![](https://cdn.jsdelivr.net/gh/hnkjdaxzzq/img@c77a8b5197dc4ba1feab5a4320b888d0e595b826/2022/04/05/9a5ff6e7786d698dc4c2a6dc4811fc86.png)

这个表是我们从数据库中摘下来的，其中最简单的做法就是直接从中获取数据

举个最简单的例子：string name = dt.Tables[0].Rows[0][1].ToString();

可以返回一个name = 何凌霄哈

当然更重要的用处自然是从数据库中把数据全都放到dataGridView中去，也就是表格控件。

我们首先要在窗口拖一个dataGridView进去，然后这样初始化它就行了


dataGridView1.DataSource = dt;

dataGridView1.DataMember = "lwx";

这样这个控件就默认显示的你的数据库的内容了

### c#中窗口间传参

窗口间传参其实很简单，首先我们要在该窗口的构造函数中向其中传入一个窗口的参数

比如，我们要从A窗口，传参数到B窗口

那么我们在B窗口的构造函数，原来是这样的:
public B(){

 InitializeComponent();
 
 }
 
 我们改写后，首先需要在类内定义一个接收参数的A类型
 在构造函数上方如此定义:
 private A a1 = null;
 
 构造函数这么写:
 
 public B(A F1)   //F1就是从外部传入的窗口代称
 {
  this.a1 = F1; //将窗口赋给a1
   InitializeComponent();
 }
 
这样我们就可以调用这个窗口的方法了，但是注意只能调用方法或者参数，控件怎么操作我还需要研究一下

当然也有个最土最笨的犯法，那就是做一个公用类，然后里面全都是一些静态值静态方法，这种东西写点简单的东西还行，但是写一些比较复杂的，就非常麻烦了

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
