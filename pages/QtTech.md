# Qt C++ 工具箱

## 从零开始的Qt开发之路

里面大概会写一些和Qt相关的内容，也不说是从0开始，感觉Qt做东西和用 C#也差不了很多？也许吧，总之慢慢来，一步一个脚印，直到给它拿下。

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


