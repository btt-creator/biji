

## C language

### C语言创建内存

#### malloc()：

`malloc()` 函数用于在堆上分配指定数量的字节。它返回分配的内存块的指针，如果分配失败则返回 `NULL`。

语法为：`void* malloc(size_t size);` `size`表示要分配的内存大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)malloc(sizeof(int));  // 在堆上分配一个整数的内存
    if (p != NULL) {
        *p = 5;
        printf("Value: %d\n", *p);
        free(p);  // 释放分配的内存
    } else {
        printf("Memory allocation failed\n");
    }
    return 0;
}

```



#### calloc()：

`calloc()` 类似于 `malloc()`，但有两个区别：它接受两个参数，并且分配的内存会被自动初始化为零。

语法为：`void* calloc(size_t num, size_t size);` `num`元素的数量、`size`每个元素的大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)calloc(4, sizeof(int));  // 在堆上分配4个整数的内存并初始化为0
    if (p != NULL) {
        for (int i = 0; i < 4; i++) {
            printf("p[%d] = %d\n", i, p[i]);  // 输出每个元素的值，应为0
        }
        free(p);  // 释放分配的内存
    } else {
        printf("Memory allocation failed\n");
    }
    return 0;
}

```



#### realloc():

`realloc()` 用于更改之前分配的内存块的大小。它可以扩大或缩小已分配内存的大小，并返回新内存块的指针。

语法为：`void* realloc(void* ptr, size_t newSize); `  `ptr`：之前分配的内存块指针。`newSize`：新的内存大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)malloc(2 * sizeof(int));  // 初始分配2个整数的内存
    if (p != NULL) {
        p[0] = 1; p[1] = 2;
        int *newP = realloc(p, 4 * sizeof(int));  // 改变内存大小以容纳4个整数
        if (newP != NULL) {
            p = newP;
            p[2] = 3; p[3] = 4;
            for (int i = 0; i < 4; i++) {
                printf("p[%d] = %d\n", i, p[i]);
            }
            free(p);  // 释放分配的内存
        } else {
            free(p);  // 如果realloc失败，原始内存需要释放
            printf("Unable to realloc memory\n");
        }
    } else {
        printf("Initial memory allocation failed\n");
    }
    return 0;
}

```



### 内存释放-悬挂指针问题

当我们进行内存的分配后，需要将内存进行释放。使用free函数，语法为：`void free(void* ptr);` `ptr`：指向先前由动态内存分配函数分配的内存块的指针。

示例：

``` C
#include <stdio.h>
#include <stdlib.h>

int main() {
    // 使用malloc分配一个包含5个整数的数组
    int *array = (int*)malloc(5 * sizeof(int));
    
    if (array == NULL) {
        // 如果内存分配失败，则输出错误消息并退出程序
        fprintf(stderr, "Memory allocation failed\n");
        return 1;
    }

    // 初始化数组
    for (int i = 0; i < 5; i++) {
        array[i] = i;
    }

    // 打印数组内容
    for (int i = 0; i < 5; i++) {
        printf("%d ", array[i]);
    }
    printf("\n");

    // 释放数组占用的内存
    free(array);

    // 注意：释放后不应再使用array指针，可将其设为NULL防止悬挂指针问题
    array = NULL;

    return 0;
}

```

****

- **避免悬挂指针**：释放内存后，原来的指针变成了悬挂指针，这意味着它指向的是不再有效的内存区域。为了避免使用悬挂指针，最好在调用 `free()` 后将指针设置为 `NULL`。

- **重复释放**：尝试释放同一个内存块两次是危险的，会导致运行时错误。确保每个动态分配的内存块只被释放一次。
- **释放非动态分配的内存**：仅应释放通过 `malloc()`, `calloc()`, 或 `realloc()` 分配的内存。尝试释放其他内存可能导致未定义行为。



### 静态函数

#### 定义：

在C语言中，静态函数的概念涉及到函数的作用域和链接属性。通过使用关键字 `static` 定义的函数，这种函数具有文件作用域，**这意味着它只能在定义它的文件内部被访问和调用**。这种属性对于封装和模块化编程非常有用。

#### 特性：

- **局部可见性**：静态函数只在其定义的源文件中可见，其他源文件不能链接到这个函数。这有助于防止函数名称的冲突，并且可以在不同的文件中使用相同的函数名而不会互相干扰。
- **保持状态**：与静态变量类似，静态函数在程序的执行期间一直存在，但它的作用域限制在定义它的文件内。

#### 静态函数的用途：

- **封装**：静态函数通常用于封装仅在某个文件内部使用的功能。这样做不仅可以隐藏实现细节，还可以提高模块的独立性和可维护性。
- **避免命名冲突**：在大型项目中，可能会有许多辅助函数，它们在不同的文件中用于不同的目的。将这些函数声明为静态的可以防止同名函数之间的链接错误和命名冲突。
- **优化**：编译器可能会对静态函数进行更优化的处理，因为编译器知道这些函数只在一个文件中被调用，这可能有助于函数调用的内联处理和其他性能优化。

#### 示例：

```C
#include <stdio.h>

// 静态函数声明
static void displayMessage() {
    printf("Hello, I am a static function.\n");
}

int main() {
    displayMessage();  // 调用静态函数
    return 0;
}

// 尝试从另一个文件调用 displayMessage() 将导致编译错误

```



### 局部静态变量

#### 定义：

局部静态变量是一种特殊类型的变量，其生命周期贯穿整个程序执行期，但其作用域限制在它被定义的函数内部。这意味着这类变量只能在其所在的函数中被访问，但它们的值在函数调用之间是持久的。局部静态变量在第一次执行到其声明的地方时初始化，并在程序结束时销毁。

#### 特性：

- **持久性**：即使函数执行完成后，静态变量的值也不会丢失。在函数的下一次调用时，变量将保持其最后一次赋值的状态。
- **初始化**：局部静态变量只在程序第一次执行到其声明的地方时被初始化一次。之后的函数调用不会再次初始化它。
- **默认初始化**：如果没有显式初始化，静态变量会自动初始化为零（对于基本数据类型）或 NULL（对于指针）。

#### 示例:

```c
#include <stdio.h>

void incrementCounter() {
    // 局部静态变量counter
    static int counter = 0;
    counter++;
    printf("Counter: %d\n", counter);
}

int main() {
    incrementCounter(); // 输出: Counter: 1
    incrementCounter(); // 输出: Counter: 2
    incrementCounter(); // 输出: Counter: 3
    return 0;
}

```



### TinyHttp项目

参考：[HTTP服务器的本质:tinyhttpd源码分析及拓展 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/24941375)、[EZLippi/Tinyhttpd: Tinyhttpd 是J. David Blackstone在1999年写的一个不到 500 行的超轻量型 Http Server，用来学习非常不错，可以帮助我们真正理解服务器程序的本质。官网:http://tinyhttpd.sourceforge.net (github.com)](https://github.com/EZLippi/Tinyhttpd)、[从零开始的tinyhttpd项目_/tinyhttpd-0.1.0-CSDN博客](https://blog.csdn.net/qq_43305312/article/details/123029010)、[Linux Socket编程（不限Linux） - 吴秦 - 博客园 (cnblogs.com)](https://www.cnblogs.com/skynet/archive/2010/12/12/1903949.html)

#### 头文件:

```C
/* J. David's webserver */
/* This is a simple webserver.
 * Created November 1999 by J. David Blackstone.
 * CSE 4344 (Network concepts), Prof. Zeigler
 * University of Texas at Arlington
 */
/* This program compiles for Sparc Solaris 2.6.
 * To compile for Linux:
 *  1) Comment out the #include <pthread.h> line.
 *  2) Comment out the line that defines the variable newthread.
 *  3) Comment out the two lines that run pthread_create().
 *  4) Uncomment the line that runs accept_request().
 *  5) Remove -lsocket from the Makefile.
 */
#include <stdio.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <ctype.h>
#include <strings.h>
#include <string.h>
#include <sys/stat.h>
#include <pthread.h>
#include <sys/wait.h>
#include <stdlib.h>
#define ISspace(x) isspace((int)(x))
//函数说明：检查参数c是否为空格字符，
//也就是判断是否为空格(' ')、定位字符(' \t ')、CR(' \r ')、换行(' \n ')、垂直定位字符(' \v ')或翻页(' \f ')的情况。
//返回值：若参数c 为空白字符，则返回非 0，否则返回 0。
#define SERVER_STRING "Server: jdbhttpd/0.1.0\r\n"//定义server名称
void accept_request(int);//接收请求
void bad_request(int);//无效请求
void cat(int, FILE *);
void cannot_execute(int);
void error_die(const char *);
void execute_cgi(int, const char *, const char *, const char *);
int get_line(int, char *, int);
void headers(int, const char *);
void not_found(int);
void serve_file(int, const char *);
int startup(u_short *);
void unimplemented(int);
```



#### accept_request(接收客户端的连接)_重要

```C
// 预备知识
//HTTP请求报文格式
[Method] [URL] [HTTP Version]
[Headers]
[Blank Line]
[Optional Body]
eg:
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
//没有optional body
//上方的响应报文格式
[HTTP Version] [Status Code] [Reason Phrase]
[Headers]
[Blank Line]
[Optional Body]
eg:
HTTP/1.1 200 OK
Date: Mon, 26 Apr 2024 12:00:00 GMT
Server: Apache
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
<html>
<head>
<title>Example Page</title>
</head>
<body>
...
</body>
</html>
//响应体在html里面
//POST方法类似这个
```



```C
/**********************************************************************/
/* A request has caused a call to accept() on the server port to
 * return.  Process the request appropriately.
 * Parameters: the socket connected to the client */
/**********************************************************************/
//接收客户端的连接，并读取请求数据
void accept_request(int client)
{
 char buf[1024]; //存储接受到的请求数据。
 int numchars; //存储接受到的字符数。
 char method[255]; //存储请求的类型，例如GET、POST。
 char url[255]; //存储请求的URL
 char path[512]; //表示文件路径
 size_t i, j;  //size_t是无符号整数类型，用于存储内存中对象的大小。其在不同系统上，有着不同的大小，但是能容纳系统能够处理的最大对象大小。
 struct stat st; //用于存储文件信息
 int cgi = 0;      /* becomes true if server decides this is a CGI
                    * program */ //决定是否执行cgi。
 char *query_string = NULL; //用于存储查询字符串。
//获取一行HTTP报文数据
 numchars = get_line(client, buf, sizeof(buf));
 //
 i = 0; j = 0;
 //对于HTTP报文来说，第一行的内容即为报文的起始行，格式为<method> <request-URL> <version>，
 //每个字段用空白字符相连
 while (!ISspace(buf[j]) && (i < sizeof(method) - 1))
 {
 //提取其中的请求方式是GET还是POST
  method[i] = buf[j];
  i++; j++;
 }
 method[i] = '\0';
//函数说明：strcasecmp()用来比较参数s1 和s2 字符串，比较时会自动忽略大小写的差异。
//返回值：若参数s1 和s2 字符串相同则返回0。s1 长度大于s2 长度则返回大于0 的值，s1 长度若小于s2 长度则返回小于0 的值。
 if (strcasecmp(method, "GET") && strcasecmp(method, "POST"))
 {
 //tinyhttp仅仅实现了GET和POST
  unimplemented(client);
  return;
 }
//cgi为标志位，置1说明开启cgi解析
 if (strcasecmp(method, "POST") == 0)
//如果请求方法为POST，需要cgi解析
  cgi = 1;

 i = 0;
 //将method后面的后边的空白字符略过
 while (ISspace(buf[j]) && (j < sizeof(buf)))
  j++;
 //继续读取request-URL
 while (!ISspace(buf[j]) && (i < sizeof(url) - 1) && (j < sizeof(buf)))
 {
  url[i] = buf[j];
  i++; j++;
 }
 url[i] = '\0';
//如果是GET请求，url可能会带有?,有查询参数
 if (strcasecmp(method, "GET") == 0)
 {
  query_string = url;
  while ((*query_string != '?') && (*query_string != '\0'))
   query_string++;
  if (*query_string == '?')
  {
  //如果带有查询参数，需要执行cgi，解析参数，设置标志位为1
   cgi = 1;
  //将解析参数截取下来，将'?'字符替换为空白字符。
   *query_string = '\0';
   query_string++;
  }
 }
//以上已经将起始行解析完毕
//url中的路径格式化到path
 sprintf(path, "htdocs%s", url); //使用sprintf将服务器的根路径与url拼接成完整的文件路径，然后存储在'PATH'中。
//学习到这里明天继续TODO
//如果path只是一个目录，默认设置为首页index.html(chatGPT说的设置为该目录下的index.html)
 if (path[strlen(path) - 1] == '/')
  strcat(path, "index.html");
 
 //函数定义:    int stat(const char *file_name, struct stat *buf); 
 //函数说明:    通过文件名filename获取文件信息，并保存在buf所指的结构体stat中 
 //返回值:     执行成功则返回0，失败返回-1，错误代码存于errno（需要include <errno.h>）
 if (stat(path, &st) == -1) {
 //假如访问的网页不存在，则不断的读取剩下的请求头信息，并丢弃即可
  while ((numchars > 0) && strcmp("\n", buf))  /* read & discard headers */
   numchars = get_line(client, buf, sizeof(buf));
  //最后声明网页不存在
  not_found(client);
 }
 else
 {
 //如果访问的网页存在则进行处理
  if ((st.st_mode & S_IFMT) == S_IFDIR)//S_IFDIR代表目录
 //如果路径是个目录，那就将主页进行显示
   strcat(path, "/index.html");
  if ((st.st_mode & S_IXUSR) ||
      (st.st_mode & S_IXGRP) ||
      (st.st_mode & S_IXOTH)    )
      //S_IXUSR:文件所有者具可执行权限
      //S_IXGRP:用户组具可执行权限
      //S_IXOTH:其他用户具可读取权限  
   cgi = 1;
  if (!cgi)
  //将静态文件返回
   serve_file(client, path);
  else
  //执行cgi动态解析
   execute_cgi(client, path, method, query_string);
 }

 close(client);//因为http是面向无连接的，所以要关闭
}
```



#### bad_request

```C
/**********************************************************************/
/* Inform the client that a request it has made has a problem.
 * Parameters: client socket */
/**********************************************************************/
void bad_request(int client)
{
 char buf[1024];
//发送400
 sprintf(buf, "HTTP/1.0 400 BAD REQUEST\r\n");
 send(client, buf, sizeof(buf), 0);
 sprintf(buf, "Content-type: text/html\r\n");
 send(client, buf, sizeof(buf), 0);
 sprintf(buf, "\r\n");
 send(client, buf, sizeof(buf), 0);
 sprintf(buf, "<P>Your browser sent a bad request, ");
 send(client, buf, sizeof(buf), 0);
 sprintf(buf, "such as a POST without a Content-Length.\r\n");
 send(client, buf, sizeof(buf), 0);
}
```



#### cat

```C
/**********************************************************************/
/* Put the entire contents of a file out on a socket.  This function
 * is named after the UNIX "cat" command, because it might have been
 * easier just to do something like pipe, fork, and exec("cat").
 * Parameters: the client socket descriptor
 *             FILE pointer for the file to cat */
/**********************************************************************/
void cat(int client, FILE *resource)
{
//发送文件的内容
 char buf[1024];
//读取文件到buf中
 fgets(buf, sizeof(buf), resource);
 while (!feof(resource))//判断文件是否读取到末尾
 {
 //读取并发送文件内容
  send(client, buf, strlen(buf), 0);
  fgets(buf, sizeof(buf), resource);
 }
}

/**********************************************************************/
/* Inform the client that a CGI script could not be executed.
 * Parameter: the client socket descriptor. */
/**********************************************************************/
void cannot_execute(int client)
{
 char buf[1024];
//发送500
 sprintf(buf, "HTTP/1.0 500 Internal Server Error\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "Content-type: text/html\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "<P>Error prohibited CGI execution.\r\n");
 send(client, buf, strlen(buf), 0);
}

/**********************************************************************/
/* Print out an error message with perror() (for system errors; based
 * on value of errno, which indicates system call errors) and exit the
 * program indicating an error. */
/**********************************************************************/
void error_die(const char *sc)
{
 perror(sc);
 exit(1);
}
```

![img](https://pic3.zhimg.com/80/v2-a8a9ca946e018c18a9293806fca234ea_720w.webp)

#### execute_cgi_重要

```c
/**********************************************************************/
/* Execute a CGI script.  Will need to set environment variables as
 * appropriate.
 * Parameters: client socket descriptor
 *             path to the CGI script */
/**********************************************************************/
//执行cgi动态解析
void execute_cgi(int client, const char *path,
                 const char *method, const char *query_string)
{
 char buf[1024];
 int cgi_output[2];//声明的读写管道，切莫被名称给忽悠，会给出图进行说明
 int cgi_input[2];//
 pid_t pid;
 int status;
 int i;
 char c;
 int numchars = 1;
 int content_length = -1;
 buf[0] = 'A'; buf[1] = '\0'; //用于保证能正确进入并执行循环。
 if (strcasecmp(method, "GET") == 0)
 //如果是GET请求
 //读取并且丢弃头信息，CGI感兴趣的地方在url，而不再头信息，而url已经在accept_request中得到。
  while ((numchars > 0) && strcmp("\n", buf)) //表示读取到一个空行
   numchars = get_line(client, buf, sizeof(buf)); //读取得到的字符数
 else    
 {
 //处理的请求为POST
  numchars = get_line(client, buf, sizeof(buf));
  while ((numchars > 0) && strcmp("\n", buf))
  {//循环读取头信息找到Content-Length字段的值，Content-Length指定了消息主体的长度。
   buf[15] = '\0';//目的是为了截取Content-Length:
   
   if (strcasecmp(buf, "Content-Length:") == 0)
   	//"Content-Length: 15"
    //atoi函数接受一个字符串参数，将字符串转换为整数，存储在参数中去。
    content_length = atoi(&(buf[16]));//获取Content-Length的值
   numchars = get_line(client, buf, sizeof(buf));
  }
  if (content_length == -1) {
  //错误请求
   bad_request(client);
   return;
  }
 }
//返回正确响应码200
 sprintf(buf, "HTTP/1.0 200 OK\r\n");
 send(client, buf, strlen(buf), 0);
//#include<unistd.h>
//int pipe(int filedes[2]);
//返回值：成功，返回0，否则返回-1。参数数组包含pipe使用的两个文件的描述符。fd[0]:读管道，fd[1]:写管道。
//必须在fork()中调用pipe()，否则子进程不会继承文件描述符。即fork必须在pipe之后。
//两个进程不共享祖先进程，就不能使用pipe。但是可以使用命名管道。
//pipe(cgi_output)执行成功后，cgi_output[0]:读通道 cgi_output[1]:写通道，这就是为什么说不要被名称所迷惑
 if (pipe(cgi_output) < 0) {  //创建输出管道
  cannot_execute(client); 
  return;
 }
 if (pipe(cgi_input) < 0) {  //创建输入管道
  cannot_execute(client);
  return;
 }

 if ( (pid = fork()) < 0 ) { //fork创建子进程，如果创建成功则返回进程ID，创建失败则返回-1。
  cannot_execute(client);
  return;
 }
 //fork出一个子进程运行cgi脚本
 if (pid == 0)  /* 子进程: 运行CGI 脚本 */ //区分父、子进程，当pid==0时，则代表在子进程。
 {
  char meth_env[255];
  char query_env[255]; //query_enc是包含HTTP请求的查询字符串部分。
 					   //eg:http://example.com/index.php?name=John&age=30中查询字符串name=John&age=30
  char length_env[255];

  dup2(cgi_output[1], 1);//1代表着stdout，0代表着stdin，将系统标准输出重定向为cgi_output[1]
  dup2(cgi_input[0], 0);//将系统标准输入重定向为cgi_input[0]，这一点非常关键，
  						//cgi程序中用的是标准输入输出进行交互
  close(cgi_output[0]);//关闭了cgi_output中的读通道
  close(cgi_input[1]);//关闭了cgi_input中的写通道
  //CGI标准需要将请求的方法存储环境变量中，然后和cgi脚本进行交互
  //存储REQUEST_METHOD
  sprintf(meth_env, "REQUEST_METHOD=%s", method); //格式化字符串，并将格式化后的结果放在数组中去。
  putenv(meth_env); //用于将字符串添加到环境中，使得CGI脚本通过查看环境变量了解到被请求的方法。
  if (strcasecmp(method, "GET") == 0) {
  //存储QUERY_STRING
   sprintf(query_env, "QUERY_STRING=%s", query_string);
   putenv(query_env);
  }
  else {   /* POST */
  	//存储CONTENT_LENGTH，用于获取完整的表单数据。
   sprintf(length_env, "CONTENT_LENGTH=%d", content_length);
   putenv(length_env);
  }
 // 表头文件#include<unistd.h>
 // 定义函数
 // int execl(const char * path,const char * arg,....);
 // 函数说明
 // execl()用来执行参数path字符串所代表的文件路径，接下来的参数代表执行该文件时传递过去的argv(0)、argv[1]……，最后一个参数必须用空指针(NULL)作结束。
 // 返回值
 // 如果执行成功则函数不会返回，执行失败则直接返回-1，失败原因存于errno中。
  execl(path, path, NULL);//执行CGI脚本 //用新程序替换掉原始的旧程序，替换成功时，新程序开始运行，且不会返回原始程序。
  exit(0);
 } else {    /* 父进程 */
  close(cgi_output[1]);//关闭了cgi_output中的写通道，注意这是父进程中cgi_output变量和子进程要区分开
  close(cgi_input[0]);//关闭了cgi_input中的读通道
  if (strcasecmp(method, "POST") == 0)
   for (i = 0; i < content_length; i++) {
   	//开始读取POST中的内容
    recv(client, &c, 1, 0); //recv函数是一种用于从连接的套接字接受数据，其中client表示套接字，&c是一个字符指针。
       						//1表示只接受一个字节的数据，0表示没有特殊的标志被设置，用于标准的接受操作。
	//将数据发送给cgi脚本
    write(cgi_input[1], &c, 1);
   }
   //读取cgi脚本返回数据
  while (read(cgi_output[0], &c, 1) > 0)
  	//发送给浏览器
   send(client, &c, 1, 0);
//运行结束关闭
  close(cgi_output[0]);
  close(cgi_input[1]);
//定义函数：pid_t waitpid(pid_t pid, int * status, int options);
//函数说明：waitpid()会暂时停止目前进程的执行, 直到有信号来到或子进程结束. 
//如果在调用wait()时子进程已经结束, 则wait()会立即返回子进程结束状态值. 子进程的结束状态值会由参数status 返回, 
//而子进程的进程识别码也会一快返回. 
//每个进程除了拥有一个自己的pid之外，还属于一个进程组。例如，打开一个程序时，可能打开多个进程，这些进程可能会被分到一个进程组。
//如果不在意结束状态值, 则参数status 可以设成NULL. 参数pid 为欲等待的子进程识别码, 其他数值意义如下：
//1、pid<-1 等待进程组识别码为pid 绝对值的任何子进程.即一个特定进程中的所有子进程。
//2、pid=-1 等待任何子进程, 相当于wait().
//3、pid=0 等待进程组识别码与目前进程相同的任何子进程.
//4、pid>0 等待任何子进程识别码为pid 的子进程.
//pid<-1能允许指定进程组的所有子进程，而pid=0则限制为当前进程的进程组中的所有子进程、pid>0则等待指定的子进程。
  waitpid(pid, &status, 0);
 }
}
```

#### 管道使用原则：

管道的使用原则是，每个管道的读端和写端必须在不同的进程中。如果一个进程需要读取数据，那么另一个进程必须写入数据，反之亦然。管道的任何一端不被使用时应该关闭，以防资源泄漏和潜在的死锁。

#### get_line

```C
/**********************************************************************/
/* Get a line from a socket, whether the line ends in a newline,
 * carriage return, or a CRLF combination.  Terminates the string read
 * with a null character.  If no newline indicator is found before the
 * end of the buffer, the string is terminated with a null.  If any of
 * the above three line terminators is read, the last character of the
 * string will be a linefeed and the string will be terminated with a
 * null character.
 * Parameters: the socket descriptor
 *             the buffer to save the data in
 *             the size of the buffer
 * Returns: the number of bytes stored (excluding null) */
/**********************************************************************/
//解析一行http报文
int get_line(int sock, char *buf, int size)
{
 int i = 0;
 char c = '\0';
 int n; //读取recv函数的返回值，即读取的字节数。

 while ((i < size - 1) && (c != '\n')) //留出一个位置用于空字符'\0'用于终止。
 {
  n = recv(sock, &c, 1, 0); //使用recv从套接字读取一个字符，一次接受一个。即将从sock套接字读取的数据存储到变量c中。
  /* DEBUG printf("%02X\n", c); */
  if (n > 0)
  {
   if (c == '\r') //读取到回车符，先使用MSG_PEEK查看下一个字符。
   {
    n = recv(sock, &c, 1, MSG_PEEK); //用于检查下一个字符是不是\n，需要重新使用一次recv实际读取出来。
    /* DEBUG printf("%02X\n", c); */
    if ((n > 0) && (c == '\n'))
     recv(sock, &c, 1, 0);
    else
     c = '\n';
   }
   buf[i] = c;
   i++;
  }
  else
   c = '\n';
 }
 buf[i] = '\0';
 
 return(i);
}
```

#### headers

```C
/**********************************************************************/
/* Return the informational HTTP headers about a file. */
/* Parameters: the socket to print the headers on
 *             the name of the file */
/**********************************************************************/
void headers(int client, const char *filename)
{
 char buf[1024];
 (void)filename;  /* could use filename to determine file type */
//发送HTTP头
 strcpy(buf, "HTTP/1.0 200 OK\r\n");
 send(client, buf, strlen(buf), 0);
 strcpy(buf, SERVER_STRING);
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "Content-Type: text/html\r\n");
 send(client, buf, strlen(buf), 0);
 strcpy(buf, "\r\n");
 send(client, buf, strlen(buf), 0);
}
```

#### not_found

```C
/**********************************************************************/
/* Give a client a 404 not found status message. */
/**********************************************************************/
void not_found(int client)
{
 char buf[1024];
 //返回404
 sprintf(buf, "HTTP/1.0 404 NOT FOUND\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, SERVER_STRING);
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "Content-Type: text/html\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "<HTML><TITLE>Not Found</TITLE>\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "<BODY><P>The server could not fulfill\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "your request because the resource specified\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "is unavailable or nonexistent.\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "</BODY></HTML>\r\n");
 send(client, buf, strlen(buf), 0);
}

/**********************************************************************/
/* Send a regular file to the client.  Use headers, and report
 * errors to client if they occur.
 * Parameters: a pointer to a file structure produced from the socket
 *              file descriptor
 *             the name of the file to serve */
/**********************************************************************/
```

#### serve_file

```C
//将请求的文件发送回浏览器客户端
void serve_file(int client, const char *filename)
{
 FILE *resource = NULL;
 int numchars = 1;
 char buf[1024];

 buf[0] = 'A'; buf[1] = '\0';//这个赋值不清楚是干什么的
 while ((numchars > 0) && strcmp("\n", buf)) //将HTTP请求头读取并丢弃
  numchars = get_line(client, buf, sizeof(buf));
//打开文件
 resource = fopen(filename, "r");
 if (resource == NULL)
 //如果文件不存在，则返回not_found
  not_found(client);
 else
 {
 //添加HTTP头
  headers(client, filename);
 //并发送文件内容
  cat(client, resource);
 }
 fclose(resource);//关闭文件句柄
}

```

#### startup_重要

```C
/**********************************************************************/
/* This function starts the process of listening for web connections
 * on a specified port.  If the port is 0, then dynamically allocate a
 * port and modify the original port variable to reflect the actual
 * port.
 * Parameters: pointer to variable containing the port to connect on
 * Returns: the socket */
/**********************************************************************/
//启动服务端
int startup(u_short *port)
{
 int httpd = 0;  //定义服务器套接字的文件描述符，初始化为0
 struct sockaddr_in name; //定义一个结构体name，用来设置套接字的地址信息。
//设置http socket，创建套接字
 httpd = socket(PF_INET, SOCK_STREAM, 0); //调用socket函数来创建一个新的流式套接字，用于IPV4，PF_INET表示协议族，主要用于IPV4的通信，0表示使用默认协议，通常是TCP。创建完成之后存在与协议族，而没有地址。
 if (httpd == -1)
  error_die("socket"); //如果返回-1，则证明生成的流式套接字失败。
 //设置套接字地址结构
 memset(&name, 0, sizeof(name)); //使用memset将套接字的内存初始化为0。
 name.sin_family = AF_INET; //设置地址族为IPV4，与上方的PF_INET类似，一个是地址(Address)，一个是协议（Protocal）
 name.sin_port = htons(*port); //将port指向的端口号从主机字节序转换为网络字节序
 name.sin_addr.s_addr = htonl(INADDR_ANY); //设置服务器套接字地址为任意地址，INADDR_ANY常用于监听任何接口上的连接。
 //绑定套接字，即将httpd与name结构体指定的地址和端口，绑定失败时，则报错。
 if (bind(httpd, (struct sockaddr *)&name, sizeof(name)) < 0)
  error_die("bind");
  //动态分配端口
 if (*port == 0)  /*动态分配一个端口 */
 {
  int namelen = sizeof(name);
  if (getsockname(httpd, (struct sockaddr *)&name, &namelen) == -1) //使用getsockname获取与套接字关联的本地地址
   error_die("getsockname");
  *port = ntohs(name.sin_port); //将动态分配的端口号转换回主机字节序，将其存在在port指针指向的位置。
 }
 //监听连接
 if (listen(httpd, 5) < 0) //使用listen函数开始监听套接字，5是backlog的大小，指定系统应该为相应套接字排队的最大连接数。
  error_die("listen");
 return(httpd); //函数返回服务器套接字描述符，供主函数使用。
}

```

#### unimplemented

```C

/**********************************************************************/
/* Inform the client that the requested web method has not been
 * implemented.
 * Parameter: the client socket */
/*********************************************************************/
void unimplemented(int client)
{
 char buf[1024];
//发送501说明相应方法没有实现
 sprintf(buf, "HTTP/1.0 501 Method Not Implemented\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, SERVER_STRING);
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "Content-Type: text/html\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "<HTML><HEAD><TITLE>Method Not Implemented\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "</TITLE></HEAD>\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "<BODY><P>HTTP request method not supported.\r\n");
 send(client, buf, strlen(buf), 0);
 sprintf(buf, "</BODY></HTML>\r\n");
 send(client, buf, strlen(buf), 0);
}
/**********************************************************************/
```

#### main

```C
int main(void)
{
 int server_sock = -1;
 u_short port = 0;
 int client_sock = -1;
 struct sockaddr_in client_name;
 int client_name_len = sizeof(client_name);
 pthread_t newthread;
//启动server socket
 server_sock = startup(&port); //一个服务器通常只创建一个监听socket描述符
 printf("httpd running on port %d\n", port);
 while (1)
 {
 //接受客户端连接
  client_sock = accept(server_sock,
                       (struct sockaddr *)&client_name,
                       &client_name_len); //建立连接完成，可以传输数据。
  if (client_sock == -1)
   error_die("accept");
 /*启动线程处理新的连接 */
 if (pthread_create(&newthread , NULL, accept_request, client_sock) != 0) //实现多线程的并发处理
   perror("pthread_create");
 }
//关闭server socket
 close(server_sock);
 return(0);
}
```





#### 多线程并发

`pthread_detach`用于将指定的线程标记为分离状态，当该线程处于分离状态时，它的资源会在线程终止时立即被回收。

#### 其他知识

首先介绍一个socket，socket是一种抽象概念，提供了在网络上进行通信的接口，其处于应用层与传输层之间。利用三元组(ip地址，协议，端口)标识网络中的进程，而本地则是使用pid进行标识。

![在这里插入图片描述](https://img-blog.csdnimg.cn/64efdeb0934245d4b74aebc95eda8741.png)

- 当创建完socket，存在一个名字空间，但它并没有被命名，需要使用`bind()`函数将套接字地址与所创建的套接字号联系起来。套接字地址根据创建socket时的地址协议族不同而不同，所以需要bind在一起。

- 作为服务器，当调用`socket()`和`bind()`之后就需要调用`listen()`来监听这个socket，如果这时候`connect()`发出连接请求，服务端就会收到到这个请求。

- 而客户端则调用`socket()`创建套接字，然后调用`connect()`来发出连接请求，服务器端就会收到到这个请求。

- 这时，当服务器监听到连接请求时，就会使用`accept()`来接受这个请求。

- 当建立完服务器与客户之间的连接时，便可调用网络I/O进行读写操作了。即在该项目中使用`recv()`和`send()`。

- 当完成了相应的读写操作时，就需要关闭相应的socket描述字，使用`close()`进行关闭。
- 当tcp建立三次握手时，发生在socket中的情况。

![image](https://images.cnblogs.com/cnblogs_com/skynet/201012/201012122157476286.png)

- 四次挥手时的情况。

![image](https://images.cnblogs.com/cnblogs_com/skynet/201012/201012122157494693.png)



## C++ language

#### 封装、继承、多态

- 封装是将数据和操作数据的方法捆绑在一起，并隐藏对象的内部实现细节。通过封装，可以保护数据不被外部代码直接访问和修改。
- 继承是面向对象编程中的一种机制，允许一个类继承另一个类的属性和方法，从而实现代码的重用和扩展。
- 多态是指在面向对象编程中，允许一个接口使用不同的具体实现。在C++中，这通常通过虚函数来实现。

##### 虚函数

虚函数值得是在基类中使用“virtual”进行修饰的成员函数，以允许在派生类中重写该函数。虚函数让C++运行时支持多态。

#### 类中成员访问控制级别：

- 公有：公有成员可以从类的外部访问。这意味着任何对象、函数或类都可以访问和使用这些成员。通常，公有成员包括类的接口（即外部可以调用的方法）。
- 私有：私有成员只能从类的内部访问。这意味着只有类的成员函数和友元函数可以访问这些成员。私有成员通常用于存储类的内部数据和实现细节，防止外部直接访问和修改。
- 保护：保护成员可以从类的内部和派生类（子类）访问。这意味着除了类本身的成员函数和友元函数外，派生类的成员函数也可以访问这些成员。保护成员通常用于在类的层次结构中共享数据。



#### 重载(overloading)和重写(overriding)的区别

- 重载指的是一个作用于内定义多个函数，他们具有相同的函数名称但是拥有者不同的参数列表。通过传递的参数来确定调用不同的重载版本。

```c++
#include <iostream>

class Example {
public:
    void func(int x) {
        std::cout << "func with int: " << x << std::endl;
    }

    void func(double x) {
        std::cout << "func with double: " << x << std::endl;
    }

    void func(int x, double y) {
        std::cout << "func with int and double: " << x << ", " << y << std::endl;
    }
};

int main() {
    Example ex;
    ex.func(10);          // 调用 void func(int x)
    ex.func(3.14);        // 调用 void func(double x)
    ex.func(10, 3.14);    // 调用 void func(int x, double y)
    return 0;
}

```

- 重写指的是在派生类中重新定义基类中的虚函数，重写函数需要与被重写的基类虚函数相同的参数列表和返回值类型。重写允许派生类提供该类特定的实现，通过不同的类调用实现重写函数(多态)。

**动态多态和静态多态：动态多态通常指的是虚函数的重写，即在编译时不知道具体执行函数，只有在运行时才知道执行的具体函数；静态多态通常指的是函数重载(即只通过传入参数的不同，在编译期便知道后续要执行的具体函数)、运算符重载、重定义(隐藏)。**



#### final标识符

’final‘关键字用于防止类被继承或防止虚函数被重写。

