# 一个工具箱，一些常见的算法模块 以及 遇到的问题

压力面前保持优雅

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
