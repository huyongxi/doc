# 基本概念

* 内核
> 内核主要的职责有，进程调度，内存管理，提供文件系统，创建和终止进程，对外部设备的访问，联网和供系统调用API。内核态和用户态，执行硬件指令可以使CPU在两种状态间来回切换，虚拟内存也划分成，用户空间部分和内核空间部分。在用户态运行，CPU只能访问用户空间部分，访问内核空间会引发硬件异常。运行在内核态既能访问用户空间也能访问内核空间。某些特定的操作如关机，访问内存管理硬件等等。也只能在核心态才能运行。  

* 用户和组  
> 系统会对每个用户的唯一身份做标识，用户可以隶属于多个组。  

* 当前工作目录
> 每个进程都有一个当前工作目录，也是进程解释相对路径的参照点。进程的当前工作目录继承自其父进程。  

* 文件的所有权和权限
> 每个文件都有一个与之相关的用户ID和组ID,分别定义文件的属主和属组。为了访问文件，系统把用户分为3类，文件的属主，与文件组ID相匹配的属组成员用户和其他用户。可以为以上3种用户分别设置3种权限（共计9种权限位）读权限，写权限，执行权限。  


# 文件I/O

* open()

```cpp
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *pathname, int flags, ... /*mode_t mode */);
//Returns file descriptor on success or -1 on error
```
> open 既可以打开一个已存在文件也可以创建并打开一个文件。如果调用成功返回文件描述符，失败返回-1，并将errno置为相应错误标识。如果调用open成功，其返回为进程未使用文件描述符中数值最小者。flags参数表示打开文件所采用的操作，我们需要注意的是必须指定以下三个常量的一种，且只允许指定一个

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


> 当调用open()创建文件时，位掩码参数mode指定了文件的访问权限。三个参数是在第二个参数中有O_CREAT时才作用，如果没有，则第三个参数可以忽略。open函数与fopen函数区别,open函数是Unix下系统调用函数，操作成功返回的是文件描述符，操作失败返回的是-1,fopen是ANSIC标准中C语言库函数，所以在不同的系统中调用不同的内核的API，返回的是一个指向文件结构的指针。同时open函数没有缓冲，fopen函数有缓冲，open函数一般和write配合使用，fopen函数一般和fwrite配合使用。

* read()

```cpp
#include <unistd.h>
ssize_t read(int fd, void* buffer, size_t count);
//Return number of bytes read, 0 on EOF, or -1 on error
```
> count参数指定最多能读取的字节数，buffer参数提供用来存放输入数据的内存缓冲区地址。缓冲区至少应有count个字节。如果read()调用成功，将返回实际读取的字节数，如果遇到文件结尾(EOF)则返回0，如果出错返回-1。一次read所读取的字节数小于请求字节数。对于普通文件而言，有可能读取位置靠近文件末尾。当读其它文件类型，比如管道，FIFO, socket或终端，在不同的环境下也会出现read调用返回的字节数小于请求的字节数。例如默认情况下从终端读取字符，一遇到换行符(\n),read调用就会结束

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

> 如果文件的偏移量已然跨越了文件结尾，然后在执行I/O操作，将会发生什么情况？read()调用返回0，表示文件结尾，wirte()函数可以在文件结尾后的任意位置写入数据。从文件结尾后到新写入数据的这段空间被称为文件空洞。文件空洞不占用任何磁盘空间。直到在文件空洞中写入了数据，文件系统才会为之分配磁盘块。核心转储文件(core dump)是包含空洞文件的常见例子。

* fcntl()

```cpp
#include <fcntl.h>
int fcntl(int fd, int cmd, ...);
//Return on success depends on cmd, or -1 on error;
```
> fcntl()系统调用对一个打开的文件描述符执行一系列控制操作。

* 文件描述符和打开文件之间的关系

> ### inode

> 每个文件系统会为其上的所有文件建立一个i-node表，inode包含文件的元信息，具体有文件的字节数，文件拥有者ID,组ID,文件的权限，文件的时间戳，链接数即多少个文件名指向这个inode,文件数据block的位置。stat命令可以查看某个文件inode信息。

```sh
$ stat 文件名
```
> 除了文件名以外的所有信息都存在inode之中。inode也会消耗磁盘空间，在硬盘格式化的时候操作系统将硬盘分成两个区域。一个是数据区，存放文件数据，另一个是inode区(inode table),存放inode所包含的信息。

```sh
$ df -i
```
> df命令可以查看硬盘分区的inode的总数和已经使用的数量。由于每个文件都必须有一个inode，有可能发生inode已经用光，但是硬盘还未存满的情况。这时，就无法在硬盘上创建新文件。
> ### inode号码
> 每个inode都有一个号码，操作系统用inode号码来识别不同的文件。Linux系统内部不使用文件名，而使用inode号码来识别文件。对于系统来说，文件名只是inode号码便于识别的别称。表面上，用户通过文件名，打开文件。实际上，系统内部这个过程分成三步：首先，系统找到这个文件名对应的inode号码；其次，通过inode号码，获取inode信息；最后，根据inode信息，找到文件数据所在的block，读出数据。使用ls -i命令，可以看到文件名对应的inode号码

```sh
$ ls -i 文件名
```
> ### 目录文件
> 在Linux系统中，目录也是一种文件，打开目录实际上就是打开目录文件。目录文件就是一系列目录项（dirent）的列表。每个目录项，由两部分组成：所包含文件的文件名，以及该文件名对应的inode号码。目录文件的读权限（r）和写权限（w），都是针对目录文件本身。由于目录文件内只有文件名和inode号码，所以如果只有读权限，只能获取文件名，无法获取其他信息，因为其他信息都储存在inode节点中，而读取inode节点内的信息需要目录文件的执行权限（x）。
> ### 硬链接
> 一般情况下，文件名和inode号码是"一一对应"关系，每个inode号码对应一个文件名。但是，Linux系统允许，多个文件名指向同一个inode号码。这意味着，可以用不同的文件名访问同样的内容；对文件内容进行修改，会影响到所有文件名；但是，删除一个文件名，不影响另一个文件名的访问。这种情况就被称为"硬链接"。inode信息中有一项叫做"链接数"，记录指向该inode的文件名总数。当这个值减到0，表明没有文件名指向这个inode，系统就会回收这个inode号码，以及其所对应block区域。
> ### 软链接
> 文件A和文件B的inode号码虽然不一样，但是文件A的内容是文件B的路径。读取文件A时，系统会自动将访问者导向文件B。因此，无论打开哪一个文件，最终读取的都是文件B。这时，文件A就称为文件B的软链接或符号链接。文件A依赖于文件B而存在，如果删除了文件B打开文件A就会报错。这是软链接与硬链接最大的不同：文件A指向文件B的文件名，而不是文件B的inode号码，文件B的inode"链接数"不会因此发生变化。
> ### 系统级打开文件表(open file table)
> 内核对所有打开的文件维护一个系统级的描述表格，并将表中各条目称为打开文件句柄。一个打开文件句柄存储了与一个打开文件相关的全部信息，如下所示。
> * 当前文件偏移量（调用read()和write()时更新，或使用lseek()直接修改。
> * 打开文件时所用使用的状态标志
> * 文件访问模式
> * 对该文件inode对象引用
> * 等等
> 
> ### 进程级的文件描述符表
> 对于每个进程，内核为其维护打开文件的描述符表，该表的每一条目都记录了单个文件描述符的相关信息，如下所示。
> * 控制文件描述符操作的一组标志。
> * 对打开文件句柄的引用。

> 如果两个文件描述符，指向同一个打开文件句柄，将共享同一文件偏移量。

* 复制文件描述符

```cpp
#include <unistd.h>
int dup(int oldfd);
//Return file descriptor on success, or -1 on error
```
> dup()调用复制一个打开的文件描述符oldfd, 并返回一个新描述符，二者都指向同一打开的文件句柄。系统会保证新描述符一定是编号值最低的未使用文件描述符。

```cpp
#include <unistd.h>
int dup2(int oldfd, int newfd);
//Return new file descriptor on success, or -1 on error
```
> dup2()系统调用会为oldfd参数所指定的文件描述符创建副本，其编号由newfd参数指定。如果newfd参数所指定编号的文件描述符之前已经打开，那么dup2()会首先将其关闭。并会忽略newfd关闭期间出现的任何错误。

* 在文件特定偏移量处的I/O，pread()和pwrite()

> 系统调用pread()和pwrite(), 会在offset参数所指定的位置进行文件I/O操作，且它们不会改变文件的当前偏移量。

```cpp
#include <unistd.h>
ssize_t pread(int fd, void* buf, size_t count, off_t offset);
//Return numbers of bytes read, 0 on EOF, or -1 on error

ssize_t pwrite(int fd, void* buf, size_t count, off_t offset);
//Return number of bytes written, or -1 on error
```
> pread()调用等于将如下调用纳入同一原子操作：

```cpp
off_t orig;
orig = lseek(fd, 0, SEEK_CUR);
lseek(fd, offset, SEEK_SET);
s = read(fd,buf, len);
lseek(fd, orig, SEEK_SET);
```
> pwrite()类似

# 进程

>进程是由内核定义的抽象的实体，并为该实体分配用以执行程序的各项系统资源。从内核角度看，进程由用户内存空间和一系列内核数据结构组成，用户内存空间包含了程序的代码以及代码使用的变量，而内核数据结构则用于维护进程的状态信息。包括许多与进程相关的标识号(IDs)、虚拟内存表、打开文件的描述符表、信号传递以及处理的有关信息、进程资源使用以及限制、当前工作目录和大量的其他信息。

* 进程号和父进程号

```cpp
#include <unistd.h>
pid_t getpid(void);
//Always successful return process ID of caller
pit_t getppid(void);
//Always successful return process ID of parent of caller
```

>  getpid和getppid返回进程ID和父进程ID，如果子进程的父进程终止，则子进程就会变成“孤儿”，init 进程随即将收养该进程，子进程对getppid的调用将返回进程号1。

* 栈和栈帧

> 用户栈，函数的调用和返回使栈的增长和收缩呈线性。X86_32体系架构之上的Linux，栈驻留在内存的高地址并向下增长。内核栈是每个进程保留在内核内存中的区域，在执行系统调用的过程中供内核内部函数调用使用。每个用户栈帧包括如下信息：

> * 函数实参和局部变量
> * 每个函数用到的一些CPU寄存器

* 执行非局部跳转：setjmp() 和 longjmp()

> 使用库函数setjmp() 和 longjmp() 可以执行非局部跳转。像C语言的goto，但goto语句存在一个限制，即不能从当前函数跳转到另一函数。

```cpp
#include <setjmp.h>
int setjmp(jmp_buf env);
void longjmp(jmp_buf env, int val);
```

> setjmp()调用为后续由longjmp()调用执行的跳转确立了跳转目标。从编程角度来看，调用longjmp()函数后，看起来就和从第二次调用setjmp()返回时完全一样。通过查看setjmp()返回值，可以区分setjmp调用是初始返回还是第二次“返回”，初始调用返回0，后续“伪“返回的值为longjmp()调用中val参数。
>
> p140