---
title: Pipe In Linux
date: 2020-05-15 22:26:33
tags: Linux
---
## Header
---
```
#include<uinstd.h>
#include<fcntl.h>
```
## Category
* [conception](#conception)
* [pipe](#pipe)
* [pipe2](#pipe2)
* [code](#code)

## conception
---
Pipe is one of the most basic IPC machanism in Linux system, whihc is able to tranfer data in parent and child process. We can easily creat and use pipe by Linux system call. The size of pipe is **65KB** in generally.

**The attributes of pipe includes:**
1. The pipe is a file in logical. Actually, it is a Linux kernel buffer.
2. There are two file descriptor in a pipe, and they respectively represents write and read.
3. The data must enter pipe by write end, and leave pip by read end.
In a word, pipe is a circle queue mechanism which is used by linux kernel.

**The limitaion of pipe:**
1. Once the data is readed, the data are taken forever, and the pipe dosen't reserve those data.
2. The pipe is a half word channel, which can only be read in one side.
3. The pipe can only be accessed by the processes which has the same father process or with father process.

**The process of parent thread and child thread IPC**

![image.png](https://upload-images.jianshu.io/upload_images/13348038-361488176f58b439.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1. The father process calls pipe function to cerate an pipe, which obtains fd[0] and fd[1].
2. The father process fork() an child process, both father and child process have fd[0] and fd[0].
3. The father process colses the read end and child process colse the write end. Then, the father process sends message to the pipe and the child process gets the data from pipe.
 
**Four special situations about pipes**
![image.png](https://upload-images.jianshu.io/upload_images/13348038-9789f7955152bb49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. If write side has no writers (write side file descriptor = 0), after data read over, the read file descriptor will return zero.
2. If write side has writers (write size file descriptor  > 0), after pipe is empty, the reader will wait for the writer to write.
![image.png](https://upload-images.jianshu.io/upload_images/13348038-d58d65ba47340439.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. If read side has no readers (read side file descriptor = 0), when writer try to write datas to the pipe, the process will receive SIGPIPE.
4. If read side has readers (read side file descriptor > 0), when pipe is full, the writer will block and wait.

## pipe
---
```
//create a pipe
int pipe(int pipefd[2]);

args
    - pipefd: two file descriptor
return
    - success: 0
    - error: -1
    
Notes:
    - pipefd[0]: read
    - pipefd[1]: write
```
## pipe2
---
```cpp
//If flags is 0, then pipe2() is the same as pipe().
int pipe2(int pipefd[2], int flags);
args:
    - pipefd: The file descriptor of pipe.
    - flags:
        - O_CLOEXEC:
        - O_DIRECT
        - O_NONBLOCK
return:
    - successed: 0
    - error: -1
```
## code
An example of pipe, we fork a child process in the main process, the main process write data to the pipe and the child process read the data and print it to the console.
```cpp
#include<unistd.h>
#include<sys/wait.h>
#include<stdio.h>
#include<stdlib.h>
#include<assert.h>
#include<string.h>
int main(){
    pid_t pid;
    int fd[2];
    if(pipe(fd)==-1){
        assert(false);
    }
    pid=fork();
    char buff[1024];
    char* p="test of pipe\n";
    int len;
    if(pid==-1){
        assert(false);
    }else if(pid==0){//child process
        close(fd[1]);
        if((len=read(fd[0],buff,sizeof(buff)))==-1){
            assert(false);
        }
        if(write(STDOUT_FILENO,buff,len)==-1){
            assert(false);
        }
        close(fd[0]);
    }else{//main process
        close(fd[0]);
        if(write(fd[1],p,strlen(p))==-1){
            assert(false);
        }
        wait(NULL);
        close(fd[1]);
         char* s="main end!\n";
         write(STDOUT_FILENO,s,strlen(s));
    }
       return 0;
}
```
