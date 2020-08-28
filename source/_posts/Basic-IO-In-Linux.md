---
title: Basic IO In Linux
date: 2020-05-15 22:24:34
tags: Linux
---
# File IO
## Header
---
```cpp
#include<sys/types.h>
#include<sys/stat.h>
#include<fcnt.h>
```
## Category
---

[open](#open)

[creat](#creat)

[close](#close)

[lseek](#lseek)

[read](#read)

[write](#write)

[code](#code)

## open
---
```cpp
//Opens a file and return it's file descriptor
int open(const char* pathname,int oflag,...)

args:
    - pathname: file path name
    - oflag:
        - O_RDONLY: read only
        - O_WRONLY: write only
        - RDWR: write and read
        - O_APPEND: appends to file end
        - O_CREAT: if file not exist, creates a new one
        - O_EXCL: Test wether a file exist or not, if not atomaicly creates a file
        - O_TRUNC: I dont know
        - O_NOCTTY: I dont know
        - O_NONBLOCK: If ptahname is a FIFO or a block, set the file to a nonblocking status.
return:
    successed: The file descriptor
    error: -1 and errno sets to appropriately
```
## creat
---
```cpp
//creats a file
int creat(const char* pathname,mode_t mode);

args:
    - pathname: The file pathname
    - mode:
        - O_RDONLY: read only
        - O_WRONLY: write only
        - RDWR: write and read
        - O_APPEND: appends to file end
        - O_CREATE: if file not exist, creates a new one
        - O_EXCL: Test wether a file exist or not, if not atomaicly creates a file
        - O_TRUNC: I dont know
        - O_NOCTTY: I dont know
        - O_NONBLOCK: If ptahname is a FIFO or a block, set the file to a nonblocking status.
return:
    successed: The file descriptor
    error: -1 and errno sets to appropriately.
```
## close
---
```cpp
//close a file
int close(int fildes);

args:
    - fildes: The file descriptor
return:
    - successed: 0
    - error: -1 and errno sets to appropriately.
```
## lseek
---
```cpp
off_t lseek(int fildes,off_t offset,int whence);

args:
    - fildes: The file descriptor.
    - offset: The offset.
    - whence:
        -SEEK_SET: The distance between file begin and offset.
        -SEEK_END: Set the offset to file end add offset.
        -SEEK_CUR: Set the offset to current value add offset.
return:
    successed: The new location of file.
    error: -1 and errno sets to appropriately.
```
## read
---
```cpp
ssize_t read(int fildes, void* buff, size_t nbytes);

args:
    - fildes: The file descriptor
    - buff: The buffer to store data
    - nbytes: Read how many bytes.
return:
    successed:
        - meets EOF: 0
        - The bytes of read.
    error: -1 and errno sets to appropriately.
```
## write
---
```cpp
ssize_t write(int fildes,const void *buff, size_t nbytes);

args:
    - fildes: The file descriptor
    - buff: The write buffer
    - nbytes: How many bytes to write
return:
    successed: The bytes of write
    error: -1 and errno sets appropriately
```
## code
---
```cpp
//package your own io functions
//io.h
#ifndef __BEE_IO_H
#define __BEE_IO_H
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<string>
#include"utils.h"
#include"macro.h"
namespace bee{
/**
 *@brief: opens a file, if not exist creats it.
 *@param[in] pathname: the file pathname
 *@param[in] ofalgs: The file open mode, default by creat and write and read.
 *@reurn: The file descriptor.
 */
int Open(const std::string& pathname,int oflags=0,mode_t mode=0);

/**
 *@brief: creats a file.
 *@param[in] pathname: the file pathname
 *@param[in] ofalgs: The file open mode, default by creat and write and read.
 *@return: The file descriptor.
 */
int Creat(const std::string& pathname, mode_t mode=O_CREAT|O_RDWR);
/**
 *@brief: close a file.
 *@param[in] fd: the file descriptor
 *@return: NULL.
 */
void Close(int fd);
int Lseek(int fd,off_t offset,int whence=SEEK_SET);
/**
 *@brief: read a file.
 *@param[in] fd: the file descriptor
 *@return: The file contains string..
 */
std::string Read(int fd);
/**
 *@brief: write a file.
 *@param[in] fd: the file descriptor
 *@param[in] buffer: The string to write
 *@return: NULL.
 */
void Write(int fd,const std::string& buffer);
};
#endif
```

```cpp
//packages your own io functions
//io.cc
#include"io.h"
#include<string.h>
namespace bee{
#define BUFF_SIZE 8192
int Open(const std::string& pathname,int oflags,mode_t mode){
    int fd;
    if(access(pathname.c_str(),F_OK)!=0){
        fd=open(pathname.c_str(),O_CREAT|O_RDWR|O_TRUNC,0777);
        std::cout<<"creats a new file in: "<<pathname<<std::endl;
    }else if(oflags==0 && mode==0){
        fd=open(pathname.c_str(),O_RDWR|O_APPEND);
    }else{
        fd=open(pathname.c_str(),oflags,mode);
    }

    if(fd==-1){
        ASSERT_DESC(false,"open file error!");
    }
    return fd;
}

int Creat(const std::string& pathname, mode_t mode){
    int fd=creat(pathname.c_str(),mode);
    if(fd==-1){
        ASSERT_DESC(false,"creat file error!");
    }
    return fd;
}

void Close(int fd){
    if(close(fd)==-1){
        ASSERT_DESC(false,"close file error!");
    }
}

int Lseek(int fd,off_t offset,int whence){
    return 0;
}

std::string Read(int fd){
    std::string str="";
   int n=1;
   char buf[BUFF_SIZE];
   lseek(fd,0,SEEK_SET);
   while(n){
        n=read(fd,buf,BUFF_SIZE);
        if(n==-1){
            ASSERT_DESC(false,"read file error!");
        }
        std::string tmp(buf);
        str.append(tmp);
        bzero(buf,BUFF_SIZE);
   }
   lseek(fd,0,SEEK_END);
   return str;
}

void Write(int fd,const std::string& buffer){
    //char buf[]="";
    int len=buffer.size();
    //strncpy(buf,buffer.c_str(),len+1);
    if(write(fd,buffer.c_str(),len)!=len){
        ASSERT_DESC(false,"write file error!");
    }
}

};
```

