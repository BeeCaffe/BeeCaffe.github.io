---
title: What Compile Do When An Empty Class Is Declared
date: 2020-05-15 22:37:01
tags: C++
---
* **conclusion**: when an empty class is declared, the compile will generate some members by default.
(1) default constructor;
(2) copy constructor;
(3) operator=();
(4) default destructor;
* **code**
```
// the mepty class
class Empty{
};

//euqal to following
class Empty{
public:
    Empty(){};
    Empty(const Empty&){};
    Empty& operator=(const Empty&){return *this};
    inline ~Empty(){};
};
```
## if user declares a constructor, the compile will not generate default constructor
* **explaination**: when a constrcutor is decalred in C++, the compile will not generate default constructor any more. Due to compile regards you do not need a default constructor anymore.
* **code**
```
// the mepty class
class Empty{
 public:
    Empty(int val):m_data(val){}
  private:
    int m_data;
};

//equals to following
class Empty{
public:
    Empty(int val):m_data(val){}
    //Empty(){};
    Empty(const Empty&){};
    Empty& operator=(const Empty&){return *this};
    inline ~Empty(){};
};
```

