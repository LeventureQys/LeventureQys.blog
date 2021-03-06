# Qt C++ 工具箱

## 从零开始的Qt开发之路

里面大概会写一些和Qt相关的内容，也不说是从0开始，感觉Qt做东西和用 C#也差不了很多？也许吧，总之慢慢来，一步一个脚印，直到给它拿下。

## 2022.5.13 

### 关于ifdef预编译的问题

一般情况下，源程序中所有的行都参加编译。但是有时希望对其中一部分内容只在满足一定条件才进行编译，也就是对一部分内容指定编译的条件，这就是“条件编译”。有时，希望当满足某条件时对一组语句进行编译，而当条件不满足时则编译另一组语句。 
条件编译命令最常见的形式为： 

	#ifdef 标识符 
	程序段1 
	#else 
	程序段2 
	#endif

往往可以用于一些公司的发布版本代码的修改，比如我想这个版本和那个版本，通过一些参数或者变量来实现，这就需要最好就通过ifdef来实现，这样可以省很多编译资源，而不用用户全部编译完成之后再进行相应的比对。这些指令只检查使用 #define 定义的标识符是否存在，而不检查在 C 或 C++ 源代码中声明的标识符。

[C++中#if #ifdef 的作用](https://www.cnblogs.com/renyuan/archive/2013/05/22/3092362.html)

## 2022.5.12 

## template 模板

有时候我们可能会写一大堆同名函数，这个时候就需要用到template函数模板

比如我们常用的swap交换两个数字的函数，交换数据的双方可能是int，double,float,string等等，总不能同名函数一直这么写下去吧，所以template应运而生。

例如：
	void swap(int&a , int& b) {
	    int temp = a;
	    a =  b;
	    b = temp;
	}

首先我们把函数模板的声明形式摆在这：

	template <class identifier> function_declaration;
	template <typename identifier> function_declaration;

就比如前面提到的swap函数，我们来给它指定一个模板

	//method.h
	template<typename T> void swap(T& t1, T& t2);

	#include "method.cpp"
	
	template<typename  T> void swap(T& t1, T& t2) {
	    T tmpT;
	    tmpT = t1;
	    t1 = t2;
	    t2 = tmpT;
	}
这样就是一个模板的声明和定义了，那模板该如何进行实例化呢？

范例：

	int num1 = 1, num2 = 2;
	swap<int>(num1, num2);
	printf("num1:%d, num2:%d\n", num1, num2); 

尖括号中可以写一些需要返回的数据类型，然后直接向函数中输入已被定义的参数即可。

#### 类模板
 
考虑我们写一个简单的栈的类，这个栈可以支持int类型，long类型，string类型等等，不利用类模板，我们就要写三个以上的stack类，其中代码基本一样，通过类模板，我们可以定义一个简单的栈模板，再根据需要实例化为int栈，long栈，string栈。

	//statck.h
	template <class T> class Stack {
	    public:
		Stack();
		~Stack();
		void push(T t);
		T pop();
		bool isEmpty();
	    private:
		T *m_pT;        
		int m_maxSize;
		int m_size;
	};
	
	//stack.cpp
	template <class  T>  Stack<T>::Stack(){
	   m_maxSize = 100;      
	   m_size = 0;
	   m_pT = new T[m_maxSize];
	}
	template <class T>  Stack<T>::~Stack() {
	   delete [] m_pT ;
	}

	template <class T> void Stack<T>::push(T t) {
	    m_size++;
	    m_pT[m_size - 1] = t;

	}
	template <class T> T Stack<T>::pop() {
	    T t = m_pT[m_size - 1];
	    m_size--;
	    return t;
	}
	template <class T> bool Stack<T>::isEmpty() {
	    return m_size == 0;
	}

至于其他的，包括模板参数，模板专门化等，详情看文章[C++模板template用法总结](https://blog.csdn.net/qq_35637562/article/details/55194097)


### C++回调函数概要


可以举个实例来看看

	#include <iostream>

	class ProgramA {
	 public:
	  void FunA1() { printf("I'am ProgramA.FunA1() and be called..\n"); }

	  static void FunA2() { printf("I'am ProgramA.FunA2() and be called..\n"); }
	};

	class ProgramB {
	 public:
	  void FunB1(void (*callback)()) {
	    printf("I'am ProgramB.FunB1() and be called..\n");
	    callback();
	  }
	};

	int main(int argc, char **argv) {
	  ProgramA PA;
	  PA.FunA1();

	  ProgramB PB;
	  PB.FunB1(ProgramA::FunA2);
	}
	
上述代码中，也就是说，在一个函数里面写一个typedef (*callback) 则代表向这个函数里面传入了一个 函数的指针，然后就可以在这个函数里面 调用这个函数

比如上面这个FunB1里面，传入一个callback()的区域函数指针，然后在函数体内可以调用这个callback()函数

在实际调用中，我们向FunB1中传入Program类中FunA2函数进去，然后再FunB1的函数实现中，实际上调用的就是FunA2函数了

结果如下：

	I'am ProgramB.FunA1() and be called..
	I'am ProgramB.FunB1() and be called..
	I'am ProgramB.FunA2() and be called..

要注意一些问题，就是回调函数在类内、类外、静态函数、非静态函数之间的调用关系，详情见下方文章

[关于C++ 回调函数(callback) 精简且实用](https://blog.csdn.net/zhoupian/article/details/119495949)

这里简单摘抄一下：为了避免 static 函数不能访问非static 成员变量或函数，会严重限制回调函数可以实现的功能。而且在programB中FunB1还使用 programA的类型，也就我预先还要知道回调函数所属的类定义，当programB想独立封装时就不好用了。

一个比较常用的方法就是：可以把非static的回调函数 包装为另一个static函数，这种方式也是一种应用比较广的方法。

	#include <iostream>

	class ProgramA {
	 public:
	  void FunA1() { printf("I'am ProgramA.FunA1() and be called..\n"); }

	  void FunA2() { printf("I'am ProgramA.FunA2() and be called..\n"); }

	  static void FunA2Wrapper(void *context) {
	    printf("I'am ProgramA.FunA2Wrapper() and be called..\n");
	    ((ProgramA *)context)->FunA2();  // 此处调用的FunA2()是context的函数, 不是this->FunA2()
	  }
	};

	class ProgramB {
	 public:
	  void FunB1(void (ProgramA::*callback)(), void *context) {
	    printf("I'am ProgramB.FunB1() and be called..\n");
	    ((ProgramA *)context->*callback)();
	  }

	  void FunB2(void (*callback)(void *), void *context) {
	    printf("I'am ProgramB.FunB2() and be called..\n");
	    callback(context);
	  }
	};

	int main(int argc, char **argv) {
	  ProgramA PA;
	  PA.FunA1();

	  ProgramB PB;
	  PB.FunB1(&ProgramA::FunA2, &PA);  // 此处都要加&

	  printf("\n");
	  PB.FunB2(ProgramA::FunA2Wrapper, &PA);
	}


但是C++其实有自带的转换宏，不需要程序员去这样写方法，太麻烦，而且也没什么含义。

std::funtion和std::bind的使用

std::funtion和std::bind可以登场了。

std::function是一种通用、多态的函数封装。std::function的实例可以对任何可以调用的目标实体进行存储、复制、和调用操作，这些目标实体包括普通函数、Lambda表达式、函数指针、以及其它函数对象等[1]。

std::bind()函数的意义就像它的函数名一样，是用来绑定函数调用的某些参数的[2]。

这里直接上代码，看std::funtion和std::bind如何在回调中使用。

	#include <iostream>

	#include <functional> // fucntion/bind

	class ProgramA {
	 public:
	  void FunA1() { printf("I'am ProgramA.FunA1() and be called..\n"); }

	  void FunA2() { printf("I'am ProgramA.FunA2() and be called..\n"); }

	  static void FunA3() { printf("I'am ProgramA.FunA3() and be called..\n"); }
	};

	class ProgramB {
	  typedef std::function<void ()> CallbackFun;
	 public:
	   void FunB1(CallbackFun callback) {
	    printf("I'am ProgramB.FunB2() and be called..\n");
	    callback();
	  }
	};

	void normFun() { printf("I'am normFun() and be called..\n"); }

	int main(int argc, char **argv) {
	  ProgramA PA;
	  PA.FunA1();

	  printf("\n");
	  ProgramB PB;
	  PB.FunB1(normFun);
	  printf("\n");
	  PB.FunB1(ProgramA::FunA3);
	  printf("\n");
	  PB.FunB1(std::bind(&ProgramA::FunA2, &PA));
	}
std::funtion支持直接传入函数地址，或者通过std::bind指定。

简而言之，std::funtion是定义函数类型(输入、输出)，std::bind是绑定特定的函数（具体的要调用的函数）。

相比于wrapper方法，这个方式要更直接、简洁很多。
	


### Qt 服务端中 设置心跳断连的实例

[Qt实现简易心跳包机制](https://zhuanlan.zhihu.com/p/452352978)


## 2022.5.11

int char*、string 和char

直接用(int)进行强制转换可能会导致一些不可预料的错误，需要使用到atoi函数[C++：string类型、char *类型，int类型之间的转换：atoi()，stoi()，c_str()](https://blog.csdn.net/Doutd_y/article/details/82316296)

## 2022.5.10

## 定义问题

# 注意，千万注意

已经定义过的关键字，就不要再定义了

比如

	char* serverip = "0";
	int port = 4443;
	char* otherUserName = "0";
	char* otherUserSeat = "0";
	char* currentUserName = "0";
	char* currentUserSeat = "0";

	if (true) {
		char* serverip = "192.168.110.122";
		char* port = 4443;
		char* otherUserName = "11";
		char* otherUserSeat = "s1";
		char* currentUserName = "22";
		char* currentUserSeat = "s2";
		char* qDebug() << "true";
	}

这可能会导致一些问题，使得程序无法进入到这个分支里面去，出现严重错误！！！

## 注意格式转换问题

如果你拿到一个QString类型，先转到char* 再转回QString，可能会缺失一部分内容，导致报错

这个websoup什么的exe，如果你给定的ip 连接错误或者连接失败，会持续的更换线程ID，同时会不停地更换输入法，导致使用失败

## 睡眠真的很重要

关于我在多屏幕之间的坐标转换这个问题上犯了蠢，geometry这个方法设定窗体坐标位置，注意是 x y w h

所以第一个参数不应该带有主桌面的x坐标，因为主窗体第一个坐标永远是0 , 0 !我之前想了半天这个问题，为什么我锁定了主屏幕，结果窗口出现在副屏幕上

	this->setGeometry(screenRect.x() - wholeFrm.width, screenRect.y(), wholeFrm.width, wholeFrm.height);

这显然是错误的，因为screenRect.x() = 0 ，所以该窗体永远只会显示到主屏幕的左边去！\

正确做法范例：

	//获取主屏幕索引
	int mainScreenID = desktop->primaryScreen();
	//获得主屏幕宽高
	QRect screenRect = desktop->screenGeometry(mainScreenID);
	//将窗体挪到对应位置去
	this->setGeometry(screenRect.width() - wholeFrm.width, screenRect.y(), wholeFrm.width, wholeFrm.height);

[ 在Qt下处理多屏幕程序方案 ](http://www.360doc.com/content/22/0408/14/496343_1025424454.shtml)

## 2022.5.9 

### 在VS中 Qt的release模式下进行调试

[C++ Release下进行调试](https://blog.csdn.net/qq_32867925/article/details/108560317)

### 报错 This application failed to start because it could not find or load the Qt platform plugin"windows" in ""

缺少了platforms的支持，找到msvc文件夹中的platforms文件夹，粘贴到 Release文件夹中就行了

### VS工程中常用路径宏定义表示$(SolutionDir) $(ProjectDir) $(ProjectName) $(Configuration) 

一个关于VS工程中常用路径宏定义表示的说明 [VS工程中常用路径宏定义表示$(SolutionDir) $(ProjectDir) $(ProjectName) $(Configuration) （good解释的很清楚）](https://blog.csdn.net/qq_23097791/article/details/95986237)

关于TestDemo中给定的默认的引用路径为:$(SolutionDir)$(Configuration)\LBD_VideoMeeting.lib;%(AdditionalDependencies)

### 关于如何向c++编译生成的exe中设定参数这回事

一般是通过命令行，具体如何实现请参考

详情请参考:[C/C++生成的exe文件如何传参数到main中](https://blog.csdn.net/weixin_41282397/article/details/83629210)

### 关于如何在qt中添加点击事件

博客 [Qt QLable 响应单击点击事件](https://blog.csdn.net/usister/article/details/76098620) 中写的比较清楚了，注意一点就是，eventFilter是继承过来的方法，所以不能直接取用，需要添加一个override参数，例如：

	public slots:
		bool eventFilter(QObject *, QEvent *) override;


## 2022.5.7

一个关于如何将widget变成一个像商用软件那样 不可拖拽大小、没有开启、关闭等按钮的固定窗口[Qt::FramelessWindowHint无边框化，移动，大小调整](https://blog.csdn.net/gongzhengyu/article/details/105879471)插入此业务代码的时候需要注意到就是，这个Direction不是Qt自带的，而是一个Enum，需要注意以下这个点



一些和窗口控件有关的、选中标红的方案:[QT的基本控件的焦点定位及切换](https://blog.csdn.net/Rebiy12/article/details/100148087)

## 2022.5.6

### 将窗口设置为全屏化

[将指定句柄窗口设置为全屏化的方案](https://blog.csdn.net/yp18792574062/article/details/115336270)

但是qt里应该有自带的，我稍微研究下

### qt中 按钮状态的切换

可以设置一下按钮按下去就陷下去，再按一下回弹的方法，就是哪个clicked(bool)方法，调用案例如下：

首先在ui初始化的时候需要对按钮进行设置：ui.btn_Audio->setCheckable(true);

	void LBD_VideoMeeting_SingalMeet::on_btn_Audio_clicked(bool isSilent)
	{
		if (isSilent) {
			qDebug() << "isSilent turns into TURE" << endl;
		}
		else {
			qDebug() << "isSilent turns into FALSE" << endl;
		}
	}


## 2022.5.6

第一个项目，可视化对讲 关于窗口句柄，其实理解上是没有问题的，为什么5.5找了一天问题呢？其实不是我自己的问题，是Release里面少了个exe文件，但是我并不知道这回事。其中关于访问网络有两个文件时必须的，一个是MediasoupWebSocket.exe，另一个是CLBDVideoMeeting.ini，这两个文件是被LBD_ViedeoMeeting引用了的，但是这里并未提及。

获得


## 2022.5.5

将句柄转成int类型，示例：QString var = QString("%1%2%3").arg("param1").arg(QString::fromLocal8bit(char*串)).arg("param2");

因为陶工给的lib是Release版的，所以这里我们也可以用Release版打开，但是这里有个问题，就是在vs中打开一个qt的工程文件，打开debug版是没有问题的，但是release版不行，原因是生成Release版的时候，它并没能正常的生成一个plugins文件，少了dll所以会报错。

解决方案见[QT-This application failed to start because it could not find or load the Qt platform plugin "windows"](https://www.cnblogs.com/happyamyhope/p/7485716.html)

※ 重要内容

如何在QT中获得窗口&控件的句柄：[获得窗口句柄](https://blog.csdn.net/qq_41951658/article/details/106907800)

第一次开始进行Qt的界面开发，第一个项目：LBD_VideoMeetingTest，有个问题，就是运行会报错，提示无法解析的外部符号。注：目前为止遇到的所有无法解析外部符号的问题都是由无法调用起来.lib文件造成的，要多加注意这点。因为是隐式调用，而且lib文件的调用是直接设置到工程文件里面去的而不是通过#pragma调用，具体设置方式是  右键工程文件->属性->链接器->附加依赖项->输入具体的.lib文件路径(相对或者绝对都行)包含文件名，如$(SolutionDir)$(Configuration)\LBD_VideoMeeting.lib;%(AdditionalDependencies)，这时就相当于用了一个#pragma声明引用了该.lib文件，使用对应的.h头文件，即可直接调用dll




## 2022.4.18 

看到个挺全的教程文档，当个参考

[qt教程](https://blog.csdn.net/p1279030826/article/details/106546752?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165027409816782094855075%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165027409816782094855075&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-106546752.142^v9^pc_search_result_cache,157^v4^control&utm_term=qt&spm=1018.2226.3001.4187)

### QString::Comapre 函数

之前遇到一个坑点就是关于QString::Compare函数，我以为这个函数是用作比对两个字符串是否相同的，但是并不是，它是用作两个字符串比大小的，具体实例如下：


QString::compare("ab","ab");  //值为0；
 
QString::compare（“ab”，“df");  //值为<0;
 
QString::compare("df","ab");         //值为>0;

反正很傻逼，我开始的时候怎么判都潘不对，如果要判断两个字符串是否相同，则需要用QString::compare(a,b) == 0;

### qt绑定数据库 遇到的一些问题

首先向工具箱中添加两个实例，一个是如何在qt的tableView中展示数据库，一个是如何连接数据库

注意使用构造函数的时候你声明的定义，如果你自定义了构造函数，而且参数非缺省，则你在任何地方都不应该出现如QInser Inser;这种语句，否则可能会报错。

另外,就是在使用执行命令的query类的指令时，一定要进行初始化，比如QSqlQuery query = QSqlQuery(db)

## 2022.4.16 数据库?

就是有个问题，你这个数据库连接了对吧，你建立了一个query对象，他就可以执行你的代码吗？理论上说是你的query直接和你当前的databse绑定了，但是并不一定，最好是直接在建立之后进行一下初始化

比如: 在.h文化中初始化 QSqlQuery query = QSqlQuery(db);

然后在cpp中进行绑定 QSqlQuery query(db);

在执行中要注意一点，就是第一行并不是默认的，要先query.next()

举个例子

    while(query.next())     //遍历数据库查找数据，先next，否则
    
    {
    
        name = query.value(1).toString();
	
        sex = query.value(2).toString();
	
        stu_number = query.value(3).toString();

        qDebug() << name;
	
        qDebug() << sex;
	
        qDebug() << stu_number;

    }


## 2022.4.15 

## Driver not loaded?

真草泥马了，这问题是真的傻逼，我的QT 可能跟我的 数据库mysql装的不是一个位数的，mysql是32位的，其实我的qt用的编译器也是32位的，但是系统却不是32位的可能，所以对mysql数据库的连接connector 不能直接用mysql给定的，具体原理我也不知道，总之网上的教程很多其实都没说到点子上，反正我自己的问题并没有解决，而在我一段时间的思考之后，我才意识到可能是我的connector也下错了，而是要下个32位的connector

因为我的mysql版本是5.5 ， 所以这个connector的版本可以下个旧一点的，比如这个 [Windows (x86, 32-bit), ZIP Archive](https://downloads.mysql.com/archives/c-c/) 下个这个就行

## QtDesigner 中 窗口没有工具栏？

因为Widgets类本身就是没有 工具栏 状态栏等等，也没法用Action，只有MainWindow类才继承了这些功能，如果需要用这些的话只能再新建一个MainWindow。

如果需要就新建一个MainWindow类，然后将Widget中所有组件复制过去这样

### driver not loaded? 

转移lib和dll 的方法都试过了，唯一的问题就是数据库和qt的版本之间不兼容，32位的数据库

然后数据库连接还是可能出错，不要用127.0.0.1做本地，按理说是没问题的，但还是报错

### qt 中文报错？

如果是qt5以上版本，将

#if _MSC_VER >= 1600

#pragma execution_character_set("utf-8")

#endif

插入对应msvc目录下的qglobal.h文件中即可解决。

来自博客 [qt中文乱码解决方案](https://blog.csdn.net/weixin_42258743/article/details/119731075)

## 2022.4.14 路径取进程名 || 一些参数类型转换问题 ||QTimer 控件的问题

#### QTimer使用的问题

其实QTimer这个控件的使用方法和C#中的timer控件差别并不大，但是要注意，QTimer控件指向的方法一般是通过connect指向槽函数

我整晚遇到的问题就是，我的connect函数 触发的槽函数 没有在头文件中定义，它并不报错，但是触发器connect并不执行，这就是很傻逼的地方这次犯了，下次记得找回场子来

#### 窗口模态打开的问题

qt中窗口有两种打开方式，一种是->show()，一种事->exec()，前者打开之后就无了，后者打开之后会锁定父窗口

其实在这里打开对话框之后就相当于事进入了一个while()循环，直到它关闭，为什么这么说呢？可以打个比方

int ret = dlgTableSize->exec(); //打开一个窗口，进入等到

if(ret == QDialog::Accepted) //如果点击窗口的确认，则触发此if

{
...
}

delete dlgTableSize; //用完记得清除窗口，直接delete就行，或者close

#### 子窗口如何操作主窗口?

声明一个子窗口的方法：instance = new QWDialogLocate(this); //加一个this指针声明当前窗口是其子窗口的父窗口

可以通过一行语句获得父窗口对象:MainWindow *parWind = (MainWindow*)parentWidget();

这样就可以得到一个指向父窗口的指针



#### 由路径取进程名：

/*内存安全的版本*/

char *get_filename(char *fullpath)

{

	char *ptr = NULL;
	
	int len = strlen(fullpath);
	
 
	if (fullpath == NULL)
	
		return NULL;
 
	ptr = fullpath + len;
 
	while (ptr != fullpath && *ptr != '\') // 注1
	
		ptr--;
 
	if (*ptr == '/')
	
		ptr++;
 
	return ptr;	
}

注1:注意一个问题，这里的*ptr != '/' 其实是有点问题的，因为常规给定的路径不会是这样，而是\ 两种斜杠不同，当然这都取决于你输入的路径如何

#### C++参数类型转换 

其实c++写的dll哪里还需要什么类型转换啊？，主要是说一下在dll里面的类型转换

我这个Firewall代码本身的代码其实不是纯c++，而是c#和c++的混合编程，也就是说其内部有一个带string类型的接口，在托管C++代码中将char*类型转换成 System::String 类型的方法如下:

String^ apppath = System::Runtime::InteropServices::Marshal::PtrToStringAnsi((IntPtr)strAppPath);

源代码中其实是有一个自带const wchar_t*指针的接口，但是这个接口的问题就是：我们不可能要求外部调用者也用这样的类型，所以必须得转置一下。

System::String 转制 const wchar_t*类型的方法 如下：

wchar_t* chrAppPath = (wchar_t*)(Marshal::StringToHGlobalUni(strAppPath)).ToPointer();


详情见项目[Firewall.dll 带demo](https://github.com/LeventureQys/Firewall)

## 2022.4.13 C#和QT链接之后的一些问题 || 关于链接库 

#### C#和QT之间链接出现的一些问题

首先一个问题就是我在c#中使用的dataset类型，在c++中是不存在的，所以我如果是在c#中写的那些函数需要返回一个dataset的话，那这个方法其实是不可用捏。

然后说说托管的事，如果你想要从dll中引出一个类，那么你就需要在这个c++的项目中声明一个托管的类来承载这个类中的内容，需要用到ref关键字

比如:ref class Dataoperator 然后如果需要声明一个数值类型比如int bool等，则可以直接如System::Boolean blnToolBroadcastFlagA; 这样声明，如果你需要声明的是一个类，那么则需要在其后加上一个^，如：System::String^ strPath;

另外注意，如果你是托管类中的声明，如果只有声明接口但是却没有写任何实现，则会报错LNK2020 无法解析xxx，这是个很坑的点，因为并没有很明确的写清楚错误原因。

#### 格式转换的问题

现在我这个c#转c++/clr 已经成功了，现在要做的就是把c++打包成一个纯c++的dll，做法不难，但需要进行对接，这时候格式就需要进行转换。

就拿我之前写的防火墙距离，比如我现在有一个方法bool CFirewall::AddApplication(System::String^ strAppPath, System::String^ strAppName) 我现在需要将其打包成一个纯c++的接口，我该怎么做？

首先我们可以先写一个接口类，我就称其为Caller.cpp，在其中写一个对外的接口DLL_FIREWALL bool AddApp(char* strAppPath, char* strAppName) (DLL_FIREWALL 是一个宏，你知道是extern "C" _declspec (dllexport)就可以了)

DLL_FIREWALL bool AddApp(char* strAppPath, char* strAppName) //接收的参数是char*类型

{

	System::String^ apppath = gcnew System::String(strAppPath); //将char*类型通过构造函数转换成System::String类型
	
	System::String^ appname = gcnew System::String(strAppName);
	

	CFirewall cfer;
	
	bool isok = cfer.AddApplication(apppath, appname); //然后再调用其函数即可
	
	if (isok) {
	
		printf("应用已成功添加");
		
	}
	
	else {
	
		printf("应用添加失败");
		
	}
	
	getchar();
	
	return true;
}

#### c#转c++遇到的一些问题

之前我觉得可能c++中不好使用C#的类，但是其实我错了，还是能有好办法的只是我懂得太少了，现在我在这里简单写一下步骤和我踩过的坑。

#### 1.首先需要一个c#的项目和c++的项目

注意这个c#的项目必须是类库，而且这个c++的项目必须是clr空项目(当然你要是那种普通的c++项目也行，但是那个/clr的设置会是你过不去的坎)。注意，这里让你建立的clr空项目并不是让你直接用的，因为它/clr并不是一个qt兼容的设置所以这个c++项目只能做一个中间态的兼容类，到时候转换成 c++的dll，再让qt去调用。

#### 2.建好项目之后，需要修改两个设置
![MFC和/clr选项](https://cdn.jsdelivr.net/gh/hnkjdaxzzq/img@9d35c75dc7449673c3acd39e0db7fd8ac6e680bb/2022/04/13/5ae57262b25b275c765fe843568ebd5f.png "MFC和/clr选项")


![系统->子系统](https://cdn.jsdelivr.net/gh/hnkjdaxzzq/img@0404b852fa7b98f436b9ab4fcc9699052ccddc42/2022/04/13/3e6ecda67c76b6d790489a59ba2c6413.png "系统->子系统")

#### 3.修改完两个设置之后，就可以开始写了，需要在头文件中输入

#using "D:\Workshop\ClassLibrary1\Debug\ClassLibrary1.dll"

对其进行引用

在源文件中 使用其命名空间 如 using namespace LeventureDesign;

然后实例化一个类即可

LeventureDesign::PublicClass^ pc = gcnew LeventureDesign::PublicClass();

注意由于这个是托管类，需要^符号进行标注

#### 4.使用

这个类应该就可以使用了，不管是静态方法还是类方法，都是可以用的了。

## 2022.4.12 .ui文件打开失败 || 文件操作 || 导入数据库功能

### 数据库功能

就在qt中使用数据库功能需要在.pro文件内添加一个QT+=sql的参数，但是我在vs里没有找到添加这个参数的接口，我一开始以为是默认添加的，其实不是，是在创建项目的时候就添加上了，如果后期想要加上，则需要添加参数，具体步骤：VS"Qt"菜单-"Qt Project Settings"-"Qt Modules"-勾选SQL library

### .ui文件打开失败:

 右键.ui文件->打开方式->添加.. ->找到msvc2015\Bin目录下的desinger.exe 确定->将其设为默认，再打开，就可以用了

### 文件操作:

写在前面，因为在qt中编码多数是有问题的，所以需要解决一下Unicode的识别问题，在main函数下写上:

QTextCodec *codec = QTextCodec::codecForName("UTF-8");

QTextCodec::setCodecForLocale(codec);


一般再QT中操作文件需要先实例化一个QFile对象，比如我现在可以写一个方法：

bool QtFirst::openTextByIODevice(const QString & aFileName)

{

	QFile aFile(aFileName);
	
	if (!aFile.exists()) //假如文件不存在
	
	{
	
		return false;
		
	}
	
	if (!aFile.open(QIODevice::ReadOnly | QIODevice::Text)) { //如果打开方式不是只读或是文本文件
	
		return false;
		
	}
	
	aFile.close();//关闭文件
	
	return true;
	
}

放置一个按钮pushButton，点击后可以选择文件，并调用上面这个方法

void QtFirst::on_pushButton_clicked() { //注意，如果需要一个点击的槽函数，可以以on_控件名称_clicked()的形式写一个函数，这样就点击会触发该槽函数了

	QString curPath = QDir::currentPath();
	
	QString dlgTitle = "打开一个文件";
	
	QString filter = "文本文件(*.txt *.docx *.doc);;所有文件(*.*)";

	//通过这个getOpenFileName方法打开一个选择文件的窗口，输入的参数有父窗口指针this，窗口标题dlgtitle，一个默认目录，和一个筛选器，选择特定的文件格式

	QString aFileName = QFileDialog::getOpenFileName(this, dlgTitle, curPath, filter); 

	if (aFileName.isEmpty()) {
	
		return;
		
	}
	
	ui->textEditDevice->setPlainText(aFile.readAll()); //读取这个文件aFile,并将其中的内容写入这个textEdit中去
	
	openTextByIODevice(aFileName);
	
}
以上就是打开一个文件的方法，主要是先通过getOpenFileName方法操作一个选择文件的窗体，然后返回一个文件路径，然后再操作其文件。

至于保存文件，则同样的，与上面的不同的是，打开文件的窗口控件变成了getSaveFileName，读取也变成了readall也变成了写入write

 注意，写入时不能直接写入QString，要将QString转换成QByteArray，比如QString str = ui->textEdit->toPlainText(); QByteArray strBytes=str.toUtf8();
 
 这样才能写入一个文件aFile.write(strBytes,strBytes.length());


## 2022.4.11 简单说下vs+qt的配置问题

说实在的，qt自带的那个creator倒也不是不能用，就是单纯的不好用，所以一般的来说，我们会把这个qt绑定在vs上使用，一个是vs的在作为编译器这方面远远比qt creator好用

首先说下qt的版本，我这里用的qt版本是5.10.1版本，vs在工作端用的是vs2015，在学生端采用的是vs2019，但是基本上差不了很远，这里简单说说2015端的配置方法

第一步，安装好qt和vs。可以确认一下是否可以新建项目什么的，确认无误之后可以开始进一步的操作

第二步，安装插件。如果是在vs2015，则可以在工具->扩展与更新->联机 中 搜索qt，第一个就是了

第三步，选择版本。在上方工具栏中出现了一个QT VS Tools，点击之后有一个QT Versions，进入之后进入Qt->Versions->add new Qt version 找到qt的安装目录，转到msvc2015->bin目录下的 qmake.exe

第四步，新建一个项目。现在应该已经可以新建一个项目了，但是还没法运行，因为会提示缺少库文件。这个时候就需要右键项目->属性->vc++目录->常规->包含目录 添加一个msvc2015\include 

第五步，到这里应该差不多就可以了，但是还要注意一点，就是QT VS Tools->General->Qt/MSBuild->Path to Qt/MSBuild files 这条，默认可能是空，需要自己填，我这里填的是C:\Users\Administrator\AppData\Local\QtMsBuild，具体情况具体分析



## 2022.4.10

### 中间休了两天，实在是上班上的头都有点晕了。

从文件操作开始，到摄像头控件的使用




通常这个 receiver需要代表的是一个窗口类，而不是某个特定的控件

比如我们写一个函数，它的作用是让一个label的text变成正宗大肥猪
方法就这么写：
void MainWindow::textChange(){

    ui->label->setText(" 正宗大肥猪 ");
    
}

而我们的connect就需要这么写：

connect(ui->pushButton,SIGNAL(clicked()),this,SLOT(textChange()));

就写在mainwindow.cpp中，当然直接卸载mainwindow.h中也是可以的

另外有个问题，就是这个qt连接vs，我现在还没搞明白，主要是创建了一个新的项目之后，没有哪个ui.h文件，我也不知道为什么

## 2022.4.8

### 从零开始，Qt开发

#### 第一步，部署Qt环境

因为公司用的是vs,而我前面的老哥用的是qt5.10，所以我也用qt5.10

安装方案如下：（VS2015 - Qt5.10）[https://www.likecs.com/show-204623088.html]

我用的是戴工老哥的安装包，按照博客中的方法安装，插件没法很好地兼容，会提示有的文件不存在，所以我选择了使用另外一个插件，就可以使用了

🤗在qt中可以直接在ui设计界面设定一个connect

先说下什么是信号与槽，其实直白点说就是触发器和接收器，翻译问题让这两个令人有点难以理解

信号:Signal，槽:Slots

不知道谁翻译的，确实难懂

总之说下普遍的连接方法吧： 

QObject:connect(sender,SIGAL(signal()),receiver,SLOT(slot());

这个QObject:: 可以省略，就是connect(sender,SIGAL(signal()),receiver,SLOT(slot());


