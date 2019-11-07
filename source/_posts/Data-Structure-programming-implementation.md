---
title: Data Structure - programming implementation
date: 2018-07-14 23:42:23
tags:
	- programming
	- Data Structure
	- note
categories:
	- Notes
	- Data Structure
---

来源于*大一数据结构上机课程的练习*，最近复习数据结构时候做个备份吧感觉当时写的还算很有心
有时间慢慢整理吧~
- - - - - - 
标注OOP的话就是cpp面向对象实现的，没有的话就是普通的结构体
所有的structure实现都是头文件.h和.cpp实现操作，具体的测试main函数就不放出了，自行测试

<!-- more -->

# 顺序表实现
{% codeblock lang:cpp %}
//sqlist.h
#ifndef SQLIST
#define SQLIST
#define MaxSize 50
typedef char ElemType;
typedef struct {
	ElemType data[MaxSize];
	int length;
} SqList; /**
*
*
*
**/
void InitList(SqList * &L); //初始化顺序表
void CreateList(SqList * &L, ElemType a[], int n); //采用尾插法依次插入元素
void DispList(SqList *L); //打印线性表
int ListLength(SqList *L); //求线性表长度
bool isEmptyList(SqList *L); //判断线性表是否为空表(空表则返回true)
bool GetElem(SqList *L, int n, ElemType &e); //求线性表某位置的元素值,求得的值返回给e
int LocateElem(SqList *L, ElemType e); //求某值在线性表中的位置
bool InsertList(SqList * &L, ElemType e, int n); //在线性表某位置插入元素
bool DeleteElem(SqList * &L, int n); //删除线性表中某位置元素
void DestroyList(SqList * &L); //释放线性表
#endif
{% endcodeblock %}

{% codeblock lang:cpp %}
//sqlist.cpp
#include "sqlist.h"
#include <iostream>
#include <cstdlib>
void InitList(SqList * &L)
{
	L = (SqList *) malloc(sizeof(SqList));
	L->length = 0;
}

void CreateList(SqList * &L, ElemType a[], int n)
{
	for(int i = 0;i < n; i++) {
		L->data[i] = *(a+i);
	}
	L->length = n;
}

void DispList(SqList *L)
{
	for (int i = 0; i < L->length; i++) {
		std::cout<< i+1 <<". "<<L->data[i]<<"\n";
	}
	std::cout<<"Finished!"<<std::endl;
}

int ListLength(SqList *L)
{
	return L->length;
}

bool isEmptyList(SqList *L)
{
	return (L->length == 0);
}

bool GetElem(SqList *L, int n, ElemType &e)
{
	if (n < 1 || L->length < n) return false;
	else e = L->data[--n];
	return true;
}

int LocateElem(SqList *L, ElemType e)
{
	int i = 0;
	while(i < L->length && e != L->data[i]) i++;
	if (i >= L->length) return 0;
	else return i+1;
}

bool InsertList(SqList * &L, ElemType e, int n)
{
	if (n < 1 || L->length+1 < n) return false;
	n--;
	for(int j = L->length; j > n; j--) 
		L->data[j] = L->data[j-1];
	L->data[n] = e;
	L->length++;
	return true;
}

bool DeleteElem(SqList * &L, int n)
{
	if (n < 1 || L->length < n) return false;
	n--;
	for(int j = n; j < L->length-1; j++) 
		L->data[j] = L->data[j+1];
	L->length--;
	return true;
}

void DestroyList(SqList * &L)
{
	free(L);
}
{% endcodeblock %}

# [OOP]队列ADT顺序表实现(非循环)

{% codeblock lang:cpp  %}
//SequenceQueue.h
#ifndef SEQUENCEQUEUE
#define SEQUENCEQUEUE

typedef char ElemType;
class SqQueue { /**
  *
  *[面向对象实现]队列ADT(顺序表实现)(非循环)
  *
  **/
private:
  ElemType *data;
  int front;
  int rear;
public:
  SqQueue();
  ~SqQueue();
  bool isEmptyQueue();
  void TraverseQueue();
  int QueueLength();
  bool GetFront(ElemType &e);
  bool EnQueue(ElemType e);
  bool DeQueue(ElemType &e);
  void CreateQueue();
};

#endif
{% endcodeblock %}

{% codeblock lang:cpp %}
//SequenceQueue.cpp
#include "SequenceQueue.h"
#include <iostream>
using namespace std;
const int MaxSize = 50;

SqQueue::SqQueue() //构造函数
{
  data = new ElemType[MaxSize];
  front = rear = -1;
}

SqQueue::~SqQueue() //析构函数
{
  delete []data;
}

bool SqQueue::isEmptyQueue()
{
  return front == rear;
}

void SqQueue::TraverseQueue()
{
  if (this->isEmptyQueue()) {
    cout << "==-> Queue is empty. <-==" << endl;
    return ;
  }
  std::cout << "\n==-- Now print the elements of the queue. --==\n";
	int n = 1;
	for (int i = front + 1; i != rear + 1; i++) {
		cout << n << ". " << *(data + i) << "\t";
		if (n%5 == 0) std::cout << "\n";
		n++;
	} std::cout << "\n-- Traversal Print Finished! --" << std::endl;
	return ;
}

int SqQueue::QueueLength()
{
  return rear - front;
}

bool SqQueue::GetFront(ElemType &e)
{
  if (this->isEmptyQueue()) {
    cout << "\n==-> ERROR. Queue is empty. <-==" << endl;
    return false;
  }
  e = *(data + front + 1);
  return true;
}

bool SqQueue::EnQueue(ElemType e)
{
  if (rear + 1 == MaxSize) { //队列满，上溢
		cout << "\n==-> ERROR.. Queue is full. <-==" << endl;
		return false;
	}
  rear++;
  *(data + rear) = e;
  return true;
}

bool SqQueue::DeQueue(ElemType &e)
{
  if (this->isEmptyQueue()) {
    cout << "\n==-> ERROR. Queue is empty. <-==" << endl;
    return false;
  }
  front++;
  e = *(data + front);
  return true;
}

void SqQueue::CreateQueue()
{
  if (data == NULL) {
  	cout << "\n==-- ERROR.Pointer is NULL --==" << endl;
  	return ;
	}
	ElemType tmp;
	cout << "\n  ========Now input the data of the Queue========\n";
  cout<<"--If you want to finish inputing, please input \"#\"--\n";
  cout << "1. ";
  cin >> tmp;
	for(int i = 2; tmp != '#'; i++) { //结束标志为‘#’
  	if (!this->EnQueue(tmp)) {
  		break;
  	}
  	cout << i << ". ";
		cin >> tmp;
  }
	cout << "\n==--< Finished input! >--==" << endl;
}

{% endcodeblock %}

# 队列ADT顺序表实现(循环)

{% codeblock lang:cpp %}
//SequenceQueue.h
#ifndef SEQUENCEQUEUE
#define SEQUENCEQUEUE

typedef char ElemType;
typedef struct List SqQueue, *ptrSqQueue; /**
 *
 *队列ADT(顺序表实现)(循环)
 *
 **/

void InitQueue(ptrSqQueue &Q); //初始化队列，为队列分配内存，建立空队列
void FreeQueue(ptrSqQueue &Q); //释放队列内存
bool isEmptyQueue(ptrSqQueue Q); //判断队列是否为空，空则返回true
void TraverseQueue(ptrSqQueue Q); //遍历打印队列元素
int QueueLength(ptrSqQueue Q); //返回队列元素的个数
bool GetFront(ptrSqQueue Q, ElemType &e); //取队头元素，返回给e
bool EnQueue(ptrSqQueue Q, ElemType e); //入队
bool DeQueue(ptrSqQueue Q, ElemType &e); //出队，删除的队头元素返回给e
void CreateQueue(ptrSqQueue Q); //交互式创建队列元素

#endif
{% endcodeblock %}

{% codeblock lang:cpp %}
//SequenceQueue.cpp
#include <iostream>
#include <cstdlib>
#include "SequenceQueue.h"
#define MaxSize 50

struct List {
	ElemType data[MaxSize];
	int front; //队头指针
	int rear; //队尾指针
};

void InitQueue(ptrSqQueue &Q) //初始化队列，为队列分配内存，建立空队列
{
	Q = (ptrSqQueue)malloc(sizeof(SqQueue));
	Q->front = Q->rear = 0;
}

void FreeQueue(ptrSqQueue &Q) //释放队列内存
{
	free(Q);
}

bool isEmptyQueue(ptrSqQueue Q) //判断队列是否为空，空则返回true
{
	return Q->rear == Q->front;
}

void TraverseQueue(ptrSqQueue Q) //遍历打印队列元素
{
	if (isEmptyQueue(Q)) {
		std::cout << "==-> Queue is empty. <-==" << std::endl;
    return ;
  }
	std::cout << "\n==-- Now print the elements of the queue. --==\n";
	int n = 1;
	for (int i = (Q->front + 1) % MaxSize; i != Q->rear + 1; ) {
		std::cout << n << ". " << Q->data[i] << "\t";
		if (n%5 == 0) std::cout << "\n";
		n++;
		i = (i + 1) % MaxSize;
	} std::cout << "\n-- Traversal Print Finished! --" << std::endl;
	return ;
}

int QueueLength(ptrSqQueue Q) //返回队列元素的个数
{
	return (Q->rear - Q->front + MaxSize) % MaxSize;
}

bool GetFront(ptrSqQueue Q, ElemType &e) //取队头元素，返回给e
{
	if (isEmptyQueue(Q)) {
		std::cout << "\n==-> ERROR.. Queue is empty. <-==" << std::endl;
    return false;
  }
	e = Q->data[(Q->front+1)%MaxSize];
	return true;
}

bool EnQueue(ptrSqQueue Q, ElemType e) //入队
{
	if ((Q->rear+1)%MaxSize == Q->front) { //队列满，上溢
		std::cout << "\n==-> ERROR.. Queue is full. <-==" << std::endl;
		return false;
	}
	Q->rear = (Q->rear + 1) % MaxSize;
	Q->data[Q->rear] = e;
	return true;
}

bool DeQueue(ptrSqQueue Q, ElemType &e) //出队，删除的队头元素返回给e
{
	if (isEmptyQueue(Q)) {
		std::cout << "\n==-> ERROR.. Queue is empty. <-==" << std::endl;
    return false;
  }
	Q->front = (Q->front + 1) % MaxSize;
	e = Q->data[Q->front];
	return true;
}

void CreateQueue(ptrSqQueue Q) //交互式创建队列元素
{
	if (Q == NULL) {
  	std::cout<<"\n==-- ERROR.Pointer is NULL --=="<<std::endl;
  	return;
	}
	ElemType tmp;
	std::cout << "\n  ========Now input the data of the STACK========\n";
  std::cout<<"--If you want to finish inputing, please input \"#\"--\n";
  std::cout << "1. ";
  std::cin >> tmp;
	for(int i = 2; tmp != '#'; i++) { //结束标志为‘#’
  	if (!EnQueue(Q, tmp)) {
  		break;
  	}
  	std::cout << i << ". ";
		std::cin >> tmp;
  }
	std::cout << "\n==--< Finished input! >--==" << std::endl;
}
{% endcodeblock %}

# 队列ADT链表实现

{% codeblock lang:cpp %}
//LinkQueue.h
#ifndef LINKQUEUE
#define LINKQUEUE

typedef char ElemType;
typedef struct node QueueNode, *ptrQueueNode;
typedef struct ptr_of_queue LinkQueue, *ptrLinkQueue;/**
 *
 *队列ADT(链表实现)
 *
 **/
void InitQueue(ptrLinkQueue &Q); //初始化队列，为队列指针分配内存，建立空队
void FreeQueue(ptrLinkQueue &Q); //释放队列内存
bool isEmptyQueue(ptrLinkQueue Q); //判断队列是否为空，空则返回true
void TraverseQueue(ptrLinkQueue Q); //遍历打印队列元素
int QueueLength(ptrLinkQueue Q); //返回队列元素的个数
bool GetFront(ptrLinkQueue Q, ElemType &e); //取队头元素，返回给e
bool EnQueue(ptrLinkQueue Q, ElemType e); //入队
bool DeQueue(ptrLinkQueue Q, ElemType &e); //出队，删除的队头元素返回给e
void CreateQueue(ptrLinkQueue Q); //交互式创建队列元素

#endif
{% endcodeblock %}

{% codeblock lang:cpp %}
//LinkQueue.cpp
#include <iostream>
#include <cstdlib>
#include "LinkQueue.h"

struct node {
	ElemType data;
	struct node *next;
};
struct ptr_of_queue {
	ptrQueueNode front;
	ptrQueueNode rear;
};

void InitQueue(ptrLinkQueue &Q) //初始化队列，为队列指针分配内存，建立空队
{
	Q = (ptrLinkQueue)malloc(sizeof(LinkQueue));
	Q->front = Q->rear = NULL;
}

void FreeQueue(ptrLinkQueue &Q) //释放队列内存
{
	ptrQueueNode p = Q->front, r;
	if (p != NULL) {
		r = p->next;
		while (r != NULL) {
			free(p);
			p = r; r = p->next;
		}
	}
	free(p);
	free(Q);
}

bool isEmptyQueue(ptrLinkQueue Q) //判断队列是否为空，空则返回true
{
	return Q->rear == NULL;
}

void TraverseQueue(ptrLinkQueue Q) //遍历打印队列元素
{
	ptrQueueNode p = Q->front;
	if (p == NULL)
	{
		std::cout<<"\n==--> The Queue is Empty. <--=="<<std::endl;
		return ;
	}
	std::cout<<"\n==-- Now print the elements of the QUEUE. --==\n";
	int i = 1;
	while (p!=NULL) {
		std::cout<< i << ". " << p->data << "\t";
		p = p->next;
		if (i%5 == 0) std::cout<<"\n";
		i++;
	} std::cout << "\n-- Traversal Print Finished! --" << std::endl;
	return ;
}

int QueueLength(ptrLinkQueue Q) //返回队列元素的个数
{
	int l = 0;
	ptrQueueNode p = Q->front;
	while (p != NULL) {
		l++;
		p = p->next;
	} return l;
}

bool GetFront(ptrLinkQueue Q, ElemType &e) //取队头元素，返回给e
{
	if (isEmptyQueue(Q))
	{
		std::cout<<"==--< ERROR.Empty Queue. >--=="<<std::endl;
		return false;
	}
	e = Q->front->data;
	return true;
}

bool EnQueue(ptrLinkQueue Q, ElemType e) //入队
{
	if (Q==NULL)
	{
		std::cout<<"--< ERROR!The pointer is NULL. >--"<<std::endl;
		return false;
	}
	ptrQueueNode tmpNode;
	tmpNode = (ptrQueueNode)malloc(sizeof(QueueNode));
	tmpNode->data = e;
	tmpNode->next = NULL;
	if (isEmptyQueue(Q)) Q->front = Q->rear = tmpNode;
	else {
		Q->rear->next = tmpNode;
		Q->rear = tmpNode;
	}
	return true;
}

bool DeQueue(ptrLinkQueue Q, ElemType &e) //出队，删除的队头元素返回给e
{
	if (Q==NULL)
	{
		std::cout<<"--< ERROR!The pointer is NULL. >--"<<std::endl;
		return false;
	}
	if (isEmptyQueue(Q))
	{
		std::cout<<"==--< ERROR.Empty Queue. >--=="<<std::endl;
		return false;
	}
	ptrQueueNode t;
	t = Q->front;
	if (Q->front == Q->rear) Q->front = Q->rear = NULL;
	else Q->front = t->next;
	e = t->data;
	free(t);
	return true;
}

void CreateQueue(ptrLinkQueue Q) //交互式创建队列元素
{
	if (Q==NULL)
	{
		std::cout<<"--< ERROR!The pointer is NULL. >--"<<std::endl;
		return ;
	}
	ElemType tmp;
	std::cout << "\n  ========Now input the data of the QUEUE========\n";
  	std::cout<<"--If you want to finish inputing, please input \"#\"--\n";
  	std::cout << "1. ";
  	std::cin >> tmp;
	for(int i = 2; tmp != '#'; i++) { //结束标志为‘#’
  		if (!EnQueue(Q, tmp)) break;
  		std::cout << i << ". ";
		std::cin >> tmp;
 	}
	std::cout << "\n==--< Finished input! >--==" << std::endl;
}
{% endcodeblock %}