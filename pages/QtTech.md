# Qt工具箱

## 从零开始的Qt开发之路

里面大概会写一些和Qt相关的内容，也不说是从0开始，感觉Qt做东西和用 C#也差不了很多？也许吧，总之慢慢来，一步一个脚印，直到给它拿下。

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


