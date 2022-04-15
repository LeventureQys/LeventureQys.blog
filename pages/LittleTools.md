# 一个工具箱，一些常见的算法模块 以及 遇到的问题

压力面前保持优雅

### qt连接数据库范例



 QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
 
    db.setHostName("localhost");         //连接数据库主机名 注意本地可以不用127.0.0.1，否则容易报错
    
    db.setPort(3306);                            //连接数据库端口号
    
    db.setDatabaseName("db_papermaker");    //连接数据库名
    
    db.setUserName("root");                //数据库用户名
    
    db.setPassword("1234");            //数据库密码
    
    db.open();
    
    if (!db.open()) 
    
    {
    
        qDebug() << "不能连接" << "connect to mysql error" << db.lastError().text();
	
        return;
	
    }
    
    else
    
    {
    
        qDebug() << "连接成功" << "connect to mysql OK";
	
    }

### qt 中文报错？

如果是qt5以上版本，将

#if _MSC_VER >= 1600

#pragma execution_character_set("utf-8")

#endif

插入对应msvc目录下的qglobal.h文件中即可解决。

来自博客 [qt中文乱码解决方案](https://blog.csdn.net/weixin_42258743/article/details/119731075)

### 2022.4.15 一些QT常见的参数

QFile 读写文件的函数:

QFileDialog::getOpenFileNmae : 打开一个窗体，可以在其中选取文件

范例:
	QString curPath = QDir::currentPath(); // 默认为当前路径
	
	QString dlgTitle = "打开一个文件";
	
	QString filter = "文本文件(*.txt *.docx);;音乐文件(*.mp3 *.wav *wma);;所有文件(*.*)";  // 写这个filter千万注意格式，中间是空格，不然读不出
	
	QString aFilename = QFileDialog::getOpenFileName(this, dlgTitle, curPath, filter); //父窗口 标题 默认路径 分离器

widget 的函数:

setWindowFlag():设置窗体的标记

setWindowType: 一些比较基本的属性，比如有无标题栏等等

setWindowState:令窗体最大化最小化等窗口设置

setWindowModality: 用于设置窗口的模态，只对窗口类型有效

setWindowOpacity: 设置窗体透明度

### 2022.4.14

#### Qt C++中窗口常见事件

closeEvent() :窗口关闭时触发

showEvent():显示窗口时触发

paintEvent():窗口绘制事件

mousePressEvent()：鼠标点击事件

mouseMoveEvent(): 鼠标移动事件

mouseReleaseEvent():鼠标释放事件

keyPressEvent():键盘按下事件

keyReleaseEvent():键盘释放事件

范例：
void QWDialogLocate::closeEvent(QCloseEvent *event)
{...}

这个event 有两个事件，一是event->accept 代表可以关闭，方法结束时的默认状态

event->ignore(); 不关闭，需要设置

比如可以放一个窗口，来决定你是否需要关闭窗口，根据按钮的情况决定event->的情况即可。

#### C++ 从路径中提取文件名

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
