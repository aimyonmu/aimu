# aimu
主要记录南京邮电大学数据结构相关课程
#include<stdio.h>
#include<stdlib.h>
typedef int Status;
typedef int ElemType;
#define OK 1
#define ERROR 0
#define TRUE 1
#define FALSE 0
typedef struct eNode
{
	int adjVex;    //与任意顶点相邻接的顶点
	ElemType w;
	struct eNode* nextArc;   //指向下一边节点
}ENode;
typedef struct lGraph
{
	int n;
	int e;
	ENode** a;   //指向一维指针数组
}LGraph;
typedef struct stack
{
	int top;
	int maxsize;
	ElemType* element;

}Stack;
void Create(Stack* S, int msize)
{

	S->maxsize = msize;
	S->element = (ElemType*)malloc(sizeof(ElemType) * msize);
	S->top = -1;
}
void Destory(Stack* S)
{
	S->maxsize = 0;
	free(S->element);
	S->top = -1;
}
bool  IsEmpty(Stack* S)
{
	return S->top == 1;
}
bool IsFull(Stack* S)
{
	return S->top == S->maxsize - 1;
}
int  Top(Stack* S, ElemType* x)
{
	if (IsEmpty(S))
		return 0;
	*x = S->element[S->top];
	return 1;
}
//在栈顶插入元素x
int   Push(Stack* S, ElemType x)
{
	if (IsFull(S))
		return 0;
	S->top++;
	S->element[S->top] = x;
	return 1;
}
int   Pop(Stack* S)
{

	if (IsEmpty(S))
		return  0;
	S->top--;
	return 1;
}
void Clear(Stack* S)
{
	S->top == -1;
}
Status Init(LGraph* lg, int nSize)
{
	int i;
	lg->n = nSize;
	lg->e = 0;
	lg->a = (ENode**)malloc(nSize * sizeof(ENode*));
	for (i = 0;i < lg->n;i++)
		lg->a[i] = NULL;   //将指针数组a置空
	return OK;
}
Status Insert(LGraph* lg, int u, int v, ElemType w)   //边<u,v>
{
	ENode* p;
	if (u < 0 || v<0 || v>lg->n - 1 || u == v)
		return ERROR;
	p = (ENode*)malloc(sizeof(ENode));
	p->adjVex = v;
	p->w = w;
	p->nextArc = lg->a[u];
	lg->a[u] = p;
	lg->e++;
	return OK;
}

Status Exist(LGraph* lg, int u, int v)
{
	ENode* p;
	p = lg->a[u];
	while (p && p->adjVex != v)
		p = p->nextArc;
	if (!p)
		return ERROR;
	else return  OK;
}
void Degree(int* inDegree, LGraph* g)   //设置每个节点度数
{
	int i;
	ENode* p;
	for (i = 0;i < g->n;i++)
		inDegree[i] = 0;
	for (i = 0;i < g->n;i++)
		for (p = g->a[i];p;p = p->nextArc)   //检查以顶点i为尾的所有邻接点
			inDegree[p->adjVex]++;    //将顶点i的临接点p->adjVex的入度加1
}
Status TopSort(int* topo, LGraph* g)
{
	int i, j, k;
	ENode* p;
	Stack s;
	int* inDegree = (int*)malloc(sizeof(int) * g->n);
	Create(&s, g->n);
	Degree(inDegree, g);
	for (i = 0;i < g->n;i++)
		if (!inDegree[i])
			Push(&s, i);
	for (i = 0;i < g->n;i++)
	{
		Top(&s, &j);
		Pop(&s);
		topo[i] = j;  //将顶点j输出到拓扑序列中
		printf("%d", j);
		for (p = g->a[j];p;p = p->nextArc)
		{
			k = p->adjVex;
			inDegree[k]--;
			if (!inDegree[k])
				Push(&s, k);

		}

	}
	return OK;
}
int main()
{
	int m;
	LGraph a;
	Init(&a, 10);
	int* tope;
	m = a.n;
	tope = (int*)malloc(m*sizeof(int));
	Insert(&a, 3, 4, 6);
	Insert(&a, 1, 2, 11);
	Insert(&a, 3, 5, 18);
	Insert(&a, 6, 9, 15);
	Insert(&a, 5, 6, 99);
	Insert(&a, 3, 6, 12);
	Insert(&a, 1, 4, 87);
	TopSort(tope, &a);
}
