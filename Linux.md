# 基本概念

* 内核
> 内核主要的职责有，进程调度，内存管理，提供文件系统，创建和终止进程，对外部设备的访问，联网和提供系统调用API。  
> 内核态和用户态，执行硬件指令可以使CPU在两种状态间来回切换，虚拟内存也划分成，用户空间部分和内核空间部分。  
> 在用户态运行，CPU只能访问用户空间部分，访问内核空间会引发硬件异常。运行在内核态既能访问用户空间也能访问内核空间。  
> 某些特定的操作如关机，访问内存管理硬件等等。也只能在核心态才能运行。  

* 用户和组  
> 系统会对每个用户的唯一身份做标识，用户可以隶属于多个组。  

* 当前工作目录
> 每个进程都有一个当前工作目录，也是进程解释相对路径的参照点。进程的当前工作目录继承自其父进程。  

* 文件的所有权和权限
> 每个文件都有一个与之相关的用户ID和组ID,分别定义文件的属主和属组。为了访问文件，系统把用户分为3类，文件的属主，  
> 与文件组ID相匹配的属组成员用户和其他用户。可以为以上3种用户分别设置3种权限（共计9种权限位）读权限，写权限，执行权限。  


# 文件I/O

* open()

```cpp
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *pathname, int flags, ... /*mode_t mode */);
//Returns file descriptor on success or -1 on error
```
> open 既可以打开一个已存在文件也可以创建并打开一个文件。如果调用成功返回文件描述符，失败返回-1，并将errno置为相应错误标识。 
> 如果调用open成功，其返回为进程未使用文件描述符中数值最小者。 
> flags参数表示打开文件所采用的操作，我们需要注意的是必须指定以下三个常量的一种，且只允许指定一个

| 访问模式 | 描述 |
|----|-----|
| O_RDONLY | 以只读方式打开文件 |
| O_WRONLY | 以只写方式打开文件 |
| O_RDWR | | 以读写方式打开文件 |

> 以下的常量是选用的，这些选项是用来和上面的必选项进行按位或起来作为flags参数

| 访问模式 | 描述 |
| ---- | ---- |
| O_APPEND | 表示追加，如果原来文件里面有内容，则这次写入会写在文件的最末尾 |
| O_CREAT | 表示如果指定文件不存在，则创建这个文件 |
| O_EXCL | 表示如果要创建的文件已存在，则出错，同时返回 -1，并且修改 errno 的值 |
| O_TRUNC | 表示截断，如果文件存在，并且以只写、读写方式打开，则将其长度截断为0 |
| O_NOCTTY | 如果路径名指向终端设备，不要把这个设备用作控制终端 |
| O_NONBLOCK | 如果路径名指向 FIFO/块文件/字符文件，则把文件的打开和后继 I/O设置为非阻塞模式（nonblocking mode）|

> 以下三个常量同样是选用的，它们用于同步输入输出

| 访问模式 | 描述 |
| ---- | ---- |
| O_DSYNC | 等待物理 I/O 结束后再 write。在不影响读取新写入的数据的前提下，不等待文件属性更新 |
| O_RSYNC | read 等待所有写入同一区域的写操作完成后再进行 |
| O_SYNC |  等待物理 I/O 结束后再 write，包括更新文件属性的 I/O |


> 当调用open()创建文件时，位掩码参数mode指定了文件的访问权限。三个参数是在第二个参数中有O_CREAT时才作用，如果没有，
> 则第三个参数可以忽略。
> open函数与fopen函数区别,open函数是Unix下系统调用函数，操作成功返回的是文件描述符，操作失败返回的是-1,fopen是ANSIC
> 标准中C语言库函数，所以在不同的系统中调用不同的内核的API，返回的是一个指向文件结构的指针。同时open函数没有缓冲，
> fopen函数有缓冲，open函数一般和write配合使用，fopen函数一般和fwrite配合使用。

* read()

```cpp
#include <unistd.h>
ssize_t read(int fd, void* buffer, size_t count);
//Return number of bytes read, 0 on EOF, or -1 on error
```
> count参数指定最多能读取的字节数，buffer参数提供用来存放输入数据的内存缓冲区地址。缓冲区至少应有count个字节。
> 如果read()调用成功，将返回实际读取的字节数，如果遇到文件结尾(EOF)则返回0，如果出错返回-1。一次read所读取的字节数
> 小于请求字节数。对于普通文件而言，有可能读取位置靠近文件末尾。当读其它文件类型，比如管道，FIFO, socket或终端，在不同
> 的环境下也会出现read调用返回的字节数小于请求的字节数。例如默认情况下从终端读取字符，一遇到换行符(\n),read调用就会结束

* write()

```cpp
#include <unistd.h>
ssize_t write(int fd, void* buffer, size_t count);
//Retuen number of bytes written,or -1 on error
```
> write()调用参数和read()调用相似。

* close()

```cpp
#include <unistd.h>
int close(int fd);
//Return 0 on success, or -1 on error
```
>close()系统调用关闭一个打开的文件描述符。

* lseek()

```cpp
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence);
//Return new file offset if successful,or -1 on error
```
> offset参数指定了一个以字节为单位的数值，whence参数表明应该参照哪个基点来解释offset参数。应为下列其中之一。

| whence | 说明 |
| ----- | ----- |
| SEEK_SET | 将文件偏移量设置为从文件头部开始的offset个字节 |
| SEEK_CUR | 相对于当前文件偏移量，将文件偏移量调整offset个字节 |
| SEEK_END | 将文件偏移量设置为起始于文件尾部的offset个字节 |

> 如果whence参数设置为SEEK_CUR或SEEK_END,offset参数可以为正数也可以为负数，如果whence为SEEK_SET,offset必须为非负数。

* 文件空洞

> 如果文件的偏移量已然跨越了文件结尾，然后在执行I/O操作，将会发生什么情况？read()调用返回0，表示文件结尾，wirte()函数
> 可以在文件结尾后的任意位置写入数据。从文件结尾后到新写入数据的这段空间被称为文件空洞。文件空洞不占用任何磁盘空间。直到
> 在文件空洞中写入了数据，文件系统才会为之分配磁盘块。核心转储文件(core dump)是包含空洞文件的常见例子。

* fcntl()

```cpp
#include <fcntl.h>
int fcntl(int fd, int cmd, ...);
//Return on success depends on cmd, or -1 on error;
```
> fcntl()系统调用对一个打开的文件描述符执行一系列控制操作。

* 文件描述符和打开文件之间的关系

> 

	
