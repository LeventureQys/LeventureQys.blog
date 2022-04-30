# 一个工具箱，一些常见的业务代码 以及 遇到的问题

压力面前保持优雅

### c#中向dataset中插入一列，并将其放到第一列的方法实例

DataSet ds = SQlHelper.GetDataTable(Con, sb.ToString());

            ds.Tables[0].Columns.Add("openid", System.Type.GetType("System.String"));
	    
            ds.Tables[0].Columns["openid"].SetOrdinal(0);//此列放置的顺序
	    
            ds.Tables[0].Columns["openid"].Caption=Session["Openid"].ToString();//.SetOrdinal(0);


### c#打开文件输入到文本框的一个小实例：

        private void btn_Search_Click(object sender, EventArgs e)
	
        {
	
            OpenFileDialog dialog = new OpenFileDialog();
	    
            dialog.Multiselect = false;
	    
            dialog.Title = "请选择一张图片";
	    
            dialog.Filter = "图片文件|*.jpg,*.jpeg,*.png,*.bmp";
	    
            if(dialog.ShowDialog() == System.Windows.Forms.DialogResult.OK) //打开一个对话框，而且当你点击确认之后则:
	    
            {
	    
                String path = dialog.FileName; //得到一个当前文件的文件名
		
                txt_Path1.Text = path; //将其输入到txt_Path下
		
            }
	    
        }

### qt 连接数据库实例，展示一个DataOperate的构造函数

 qno = ""; //试题编号
 
        qpic = ""; // 题目图片路径
	
        //QString qcontent; //试题文字
	
        difficulty = -1; //
	
        Cid = -1;
	
        BelongId = -1;
	
        Answer = "";

        db = QSqlDatabase::addDatabase("QMYSQL");

        //QSqlDatabase db;
	
        //if (QSqlDatabase::contains("qt_sql_default_connection"))
	
        //    db = QSqlDatabase::database("qt_sql_default_connection");
	
        //else
	
        //    db = QSqlDatabase::addDatabase("QMYSQL");
      
        db.setHostName("localhost");         //连接数据库主机名
	
        db.setPort(3306);                            //连接数据库端口号
	
        db.setDatabaseName("db_papermaker");    //连接数据库名
	
        db.setUserName("root");                //数据库用户名
	
        db.setPassword("1234");            //数据库密码
	
        db.open();

       
        if (!db.open()) //判断当前db是否已经连接成功
	
        {
	
            qDebug() << "不能连接" << "connect to mysql error" << db.lastError().text();
	    
            return;
	    
        }
	
        else
	
        {
            qDebug() << "连接成功" << "connect to mysql OK";
	    
            QSqlQuery query = QSqlQuery(db); //一个执行sql语句的实例，会将存储的内容放到其中
	    
           bool  value = query.exec("select * from tb_user");
	   
            if (value) {
	    
                qDebug() << "query 连接成功" << "query connect to mysql OK";
		
            }
	    
            //query.exec("select * from tb_user");
	    
          /*  query.next();
	  
            qDebug() << query.value(1).toString();*/
           
        }

            
         }
    



### qt中向tableView中添加数据库中的数据

        dtop.model->setQuery(QString("select * from tb_questiondata"));
	
        //列名  
	
        dtop.model->setHeaderData(0, Qt::Horizontal, tr("试题编号"));
	
        dtop.model->setHeaderData(1, Qt::Horizontal, tr("试题路径"));
	
        /*model->setHeaderData(2, Qt::Horizontal, tr("试题内容"));*/
	
       dtop.model->setHeaderData(2, Qt::Horizontal, tr("试题难度"));
       
        dtop.model->setHeaderData(3, Qt::Horizontal, tr("试题章节"));
	
        dtop.model->setHeaderData(4, Qt::Horizontal, tr("试题类型"));
	
        dtop.model->setHeaderData(5, Qt::Horizontal, tr("试题答案"));
	
        ui.tableView->setModel(dtop.model);//数据放置到tableView中
	
        ui.tableView->verticalHeader()->hide();//不显示序号
	


### qt中异常处理:

enum Except { EXCEP_ZERO, EXCEP_ONE };

QT_TRY{

...

}

QT_CATCH(Except ex){

if(ex == EXCEP_ZERO) QT_RETHROW;

}


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
