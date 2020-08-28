---
title: Install Boost In Linux
date: 2020-05-15 22:32:34
tags: Linux
---
## 1. Fast install
Typing the following command in linux console to install boost dirtectly.

**Notes**: you'd best add **sudo** before you command to aviod premission deny especially in system level path.

    wget https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.gz
        
    tar -xzvf boost_1_54_0.tar.gz
    
    cd boost_1_54_0
    
    ./bootstrap.sh --prefix=/usr/local
    
    ./b2 install --with=all
    
    ## you can find boost library in /usr/local/lib.
    
## 2. Test example
There is an test example of boost libray, you just need to copy the soucure code of the following program and compile this source code:
* **command**
        
        vim boost_test.cc

        g++ boost_test.cc -L/usr/local/lib -lboost_system
        
        ./a.out

* **source code**

        #include <boost/lexical_cast.hpp>
        #include <iostream>
        int main()
        {
                using boost::lexical_cast;
                int a = lexical_cast<int>("123");
                double b = lexical_cast<double>("123.12");
                std::cout<<a<<std::endl;
                std::cout<<b<<std::endl;
                return 0;
        }

* **output**
        
        123
        123.12
    