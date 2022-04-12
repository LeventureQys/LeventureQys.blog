# Qt工具箱

## 从零开始的Qt开发之路

里面大概会写一些和Qt相关的内容，也不说是从0开始，感觉Qt做东西和用 C#也差不了很多？也许吧，总之慢慢来，一步一个脚印，直到给它拿下。

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


