---
layout: post
title: c++泛型单例模式
---

单例模式是在设计模式里面很常见的一种,用来确保一个类只有一个实例. 首先是原生态版本的实现.


```c++
#include <iostream>
#ifndef SING_H
class sing {
  private:
    static sing *m_sing;
  public:
    int num;
    static void open();
    static void close();
    static sing *getInstance();
    void func();
  protected:
    ~sing();
    sing();
};

#define SING_H
#endif


#include <iostream>
#include <string>
#include "sing.h"
#include "assert.h"
using namespace std;

sing *sing::m_sing = NULL;
sing::sing()
{
  printf("sing construct\n");
}

sing::~sing()
{
  printf("sing destruct\n");
}

sing *sing::getInstance()
{
  if (m_sing == NULL) {
    printf("m_sing is NULL\n");
    m_sing = new sing();
    assert(m_sing != NULL);
  }
  return m_sing;
}

void sing::close()
{
  if (m_sing == NULL) {
    delete m_sing;
    m_sing = NULL;
  }
}

void sing::func()
{
  printf("sing::func\n");
}

#include <iostream>
#include "sing.h"

int main()
{
  sing *s = sing::getInstance();
  s->num = 10;

  sing *s1 = sing::getInstance();
  printf("%d\n", s1->num);
  return 0;
}
```

c++ 的单态实现, 这里getInstance()获得那个单态的指针对象.

以下是c++ 的泛型的单态的实现 

```c++
#ifndef TSING_H
#define TSING_H
template 
class sing {
  private:
    static T *m_sing;

  public:
    static void close();
    static T* getInstance();
    sing();
    ~sing();
};

template  T *sing::m_sing = NULL;

template  sing::sing() {
  printf("sing construct\n");
}

template  sing::~sing() {
  printf("sing destructor\n");
  close();
}

template  T* sing::getInstance() {
  if (m_sing == NULL) {
    printf("m_sing is NULL\n");
    m_sing = new T();
  }
  return m_sing;
}

template  void sing::close() {
  if (m_sing != NULL) {
    delete m_sing;
  }
}
#endif
#include <iostream>
#include "tsing.h"

struct node {
  int num;
  bool flag;
  int type;
};

int main()
{
  node *s = sing::getInstance();
  s->num = 10;
  s->flag = 0;
  s->type = 100;

  //s->num = 10;

  node *s1 = sing::getInstance();
  printf("%d %d %d\n", s1->num, s1->flag, s1->type);
  return 0;
}
```
在我理解以来,在没有用泛型以前这里的静态指针sing,既是容器也是sing类型对象,也就是说之前这个单态只能设计来给sing这个类来使用,泛型的sing类,这里把sing只是这个容器的名字,T想要单态的类型,任意的类型对象. 而前面这种没有实现泛型是将sing即是这个类的容器也是这个类的对象.
