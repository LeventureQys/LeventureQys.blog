# Qt工具箱

## 从零开始的Qt开发之路

里面大概会写一些和Qt相关的内容，也不说是从0开始，感觉Qt做东西和用 C#也差不了很多？也许吧，总之慢慢来，一步一个脚印，直到给它拿下。

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


