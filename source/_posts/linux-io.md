---
title: Linux io 
date: 2021-05-12 11:00:00
tags:
    - Linux
---

open函数：

	int open(char *pathname, int flags)	#include <unistd.h>

	参数：
		pathname: 欲打开的文件路径名

		flags：文件打开方式：	#include <fcntl.h>

			O_RDONLY|O_WRONLY|O_RDWR	O_CREAT|O_APPEND|O_TRUNC|O_EXCL|O_NONBLOCK ....

	返回值：
		成功： 打开文件所得到对应的 文件描述符（整数）

		失败： -1， 设置errno	

	int open(char *pathname, int flags， mode_t mode)		123  775	

	参数：
		pathname: 欲打开的文件路径名

		flags：文件打开方式：	O_RDONLY|O_WRONLY|O_RDWR	O_CREAT|O_APPEND|O_TRUNC|O_EXCL|O_NONBLOCK ....

		mode: 参数3使用的前提， 参2指定了 O_CREAT。	取值8进制数，用来描述文件的 访问权限。 rwx    0664

			创建文件最终权限 = mode & ~umask

	返回值：
		成功： 打开文件所得到对应的 文件描述符（整数）

		失败： -1， 设置errno	

close函数：

	int close(int fd);


错误处理函数：		与 errno 相关。

	printf("xxx error: %d\n", errno);

	char *strerror(int errnum);

		printf("xxx error: %s\n", strerror(errno));

	void perror(const char *s);

		perror("open error");


read函数：

	ssize_t read(int fd, void *buf, size_t count);

	参数：
		fd：文件描述符

		buf：存数据的缓冲区

		count：缓冲区大小

	返回值：

		0：读到文件末尾。

		成功；	> 0 读到的字节数。

		失败：	-1， 设置 errno

		-1： 并且 errno = EAGIN 或 EWOULDBLOCK, 说明不是read失败，而是read在以非阻塞方式读一个设备文件（网络文件），并且文件无数据。

write函数：

	ssize_t write(int fd, const void *buf, size_t count);

	参数：
		fd：文件描述符

		buf：待写出数据的缓冲区

		count：数据大小

	返回值：

		成功；	写入的字节数。

		失败：	-1， 设置 errno

文件描述符：

	PCB进程控制块：本质 结构体。

	成员：文件描述符表。

	文件描述符：0/1/2/3/4。。。。/1023     表中可用的最小的。

	0 - STDIN_FILENO

	1 - STDOUT_FILENO

	2 - STDERR_FILENO

阻塞、非阻塞：  是设备文件、网络文件的属性。

	产生阻塞的场景。 读设备文件。读网络文件。（读常规文件无阻塞概念。）

	/dev/tty -- 终端文件。

	open("/dev/tty", O_RDWR|O_NONBLOCK)	--- 设置 /dev/tty 非阻塞状态。(默认为阻塞状态)

fcntl：
	int (int fd, int cmd, ...)

	int flgs = fcntl(fd,  F_GETFL);

	flgs |= O_NONBLOCK

	fcntl(fd,  F_SETFL, flgs);

	获取文件状态： F_GETFL

	设置文件状态： F_SETFL

lseek函数：

	off_t lseek(int fd, off_t offset, int whence);

	参数：
		fd：文件描述符

		offset： 偏移量

		whence：起始偏移位置： SEEK_SET/SEEK_CUR/SEEK_END

	返回值：

		成功：较起始位置偏移量

		失败：-1 errno

	应用场景：	
		1. 文件的“读”、“写”使用同一偏移位置。

		2. 使用lseek获取文件大小

		3. 使用lseek拓展文件大小：要想使文件大小真正拓展，必须引起IO操作。

			使用 truncate 函数，直接拓展文件。	int ret = truncate("dict.cp", 250);

传入参数：

	1. 指针作为函数参数。

	2. 同常有const关键字修饰。

	3. 指针指向有效区域， 在函数内部做读操作。

传出参数：

	1. 指针作为函数参数。

	2. 在函数调用之前，指针指向的空间可以无意义，但必须有效。

	3. 在函数内部，做写操作。

	4。函数调用结束后，充当函数返回值。

传入传出参数：

	1. 指针作为函数参数。

	2. 在函数调用之前，指针指向的空间有实际意义。

	3. 在函数内部，先做读操作，后做写操作。

	4. 函数调用结束后，充当函数返回值。


stat/lstat 函数：

	int stat(const char *path, struct stat *buf);

	参数：
		path： 文件路径

		buf：（传出参数） 存放文件属性。

	返回值：

		成功： 0

		失败： -1 errno

	获取文件大小： buf.st_size

	获取文件类型： buf.st_mode

	获取文件权限： buf.st_mode

	符号穿透：stat会。lstat不会。

link/unlink:

	隐式回收。


目录操作函数：

	DIR * opendir(char *name);

	int closedir(DIR *dp);

	struct dirent *readdir(DIR * dp);

		struct dirent {

			inode

			char dname[256];
		}


递归遍历目录：

	1. 判断命令行参数，获取用户要查询的目录名。	int argc, char *argv[1]

		argc == 1  --> ./

	2. 判断用户指定的是否是目录。 stat  S_ISDIR(); --> 封装函数 isFile() {   }

	3. 读目录： read_dir() { 

		opendir（dir）

		while （readdir（））{

			普通文件，直接打印

			目录：
				拼接目录访问绝对路径。sprintf(path, "%s/%s", dir, d_name) 

				递归调用自己。--》 opendir（path） readdir closedir
		}

		closedir（）

		}
		read_dir() --> isFile() ---> read_dir()

dup 和 dup2：

	int dup(int oldfd);		文件描述符复制。

		oldfd: 已有文件描述符

		返回：新文件描述符。

	int dup2(int oldfd, int newfd); 文件描述符复制。重定向。



fcntl 函数实现 dup：

	int fcntl(int fd, int cmd, ....)

	cmd: F_DUPFD

	参3:  	被占用的，返回最小可用的。

		未被占用的， 返回=该值的文件描述符。

fcntl 函数实现 dup：

	int fcntl(int fd, int cmd, ....)

	cmd: F_DUPFD

	参3:  	被占用的，返回最小可用的。

		未被占用的， 返回=该值的文件描述符。
