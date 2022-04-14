# 一个工具箱，一些常见的算法模块

### 2022.4.14

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
