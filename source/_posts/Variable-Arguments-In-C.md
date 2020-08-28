---
title: Variable Arguments In C++
date: 2020-05-15 22:33:34
tags: C++
---
## 1. what is variable arguments?
In C\C++ program we always need some functions which arguments number are variable, such as **printf()** and **scanf()**. We called those function as variable arguments function and we use placeholder **...** to represent the uncertained[1]. arguments.
        
    int printf(const char* fmt, ...);
    int scanf(const char* fmt, ...);

And, there is an simple example of variable arguments:

        #include <iostream>
        #include <cstdarg>
        
        /*
         * @brief : The purpose of this function is to print any number of integer,
         * @param[in] count : The number of int you suppose to print.
         * @param[in] ... : variable arguments.
         */
        int print_args(int count, ...){
            int i,value;
            //// the pointer of arguments address
            va_list arg_ptr;
            //// get address of first argument
            va_start(arg_ptr, count);
            for(int i=0; i<count; ++i){
                //// get the next argument address
                value = va_arg(arg_ptr,int);
                printf("position %d=%d\n",i+1,value);
            }
            //// set arg_ptr yo 0
            va_end(arg_ptr);
        }
        
        int main() {
            print_args(5,1,2,3,4,5);
            return 0;
        }
## 2. The macros of va_list, va_start, va_arg and va_end
Those types are used as parameters for the macros defined in <cstdarg> to retrieve the additional arguments of a function[2].

**va_list** The type to hold information about variable arguments [2].

**va_start(va_list,paramN)** Initialize a variable argument list. Initailizes **va_list** type pointer to retrieve the additional arguments after paramN[2].

**va_arg(va_list, type)** Retrieve next argument.

**va_end(va_list)** End using variable argument list, set va_list type argument to nullptr.

We can illustrate the upper for loop as the following picture:

![va_list.jpg](https://upload-images.jianshu.io/upload_images/13348038-c8913e90783c0b8f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
45B0883250139C6D9A27)

when we call **va_start(arg_ptr,count)**, the arg_ptr has been set to the start address of variable arguments list. Then, the **va_arg(arg_ptr,int)** function move the arg_ptr pointer. In the end, we set arg_ptr to zero.

## 3. vsprintf(char* str, const char* format, va_list arg)
There is a utilization of variable arguments or said va_list, that is function **vsprintf()**, this function is able to print variable arguments in a suitable format.

** str ** the char* pointer, point to char[] where we suppose to print.

**format** the contorl format of string, such as "%d,%f,%n" ...

**arg** va_list type argument which points to the start position of variable arguments list.

There is a example to utilize va_list and vsprintf()**[3]** :

    #include<stdio.h>
    #include<stdarg.h>
    char buffer[80];
    int vspfunc(char* format, ...){
        va_list arg;
        int ans;
        va_start(arg,format);
        ans = vsprintf(buffer,format,arg);
        va_end(arg);
        return ans;
    }
    
    int main(){
        int i = 5;
        float f = 27.0;
        char str[50] = "runoob.com";
        vspfunc("%d %f %s",i,f,str);
        printf("%s\n",buffer);
        return 0;
    }
## Reference
[1] https://www.cnblogs.com/zlcxbb/p/6802895.html  
[2] http://www.cplusplus.com/reference/cstdarg/va_list/  
[3] https://www.runoob.com/cprogramming/c-function-vsprintf.html

