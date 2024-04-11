# C / C++ 宏中 ## 与 # 的用法

## 一般用法
我们使用 # 把宏参数变为一个字符串,用 ## 把两个宏参数贴合在一起.用法:
```cpp
#include<cstdio>
#include<climits>
using namespace std;
#define STR(s)     #s
#define CONS(a,b)  int(a##e##b)
int main()
{
    printf(STR(vck));// 输出字符串"vck"
    printf("%d/n", CONS(2,3));// 2e3 输出:2000
    return 0;
}
```
## 当宏参数是另一个宏的时候
需要注意的是凡宏定义里有用'#'或'##'的地方宏参数是不会再展开
- 非'#'和'##'的情况
```cpp
#define TOW         (2)
#define MUL(a,b)    (a*b)
printf("%d*%d=%d/n", TOW, TOW, MUL(TOW,TOW));
//这行的宏会被展开为：
printf("%d*%d=%d/n", (2), (2), ((2)*(2)));
// MUL里的参数TOW会被展开为(2).
```
- 当有'#'或'##'的时候
```cpp
#define A          (2)
#define STR(s)     #s
#define CONS(a,b)  int(a##e##b)

printf("int max: %s/n",  STR(INT_MAX));// INT_MAX ＃i nclude<climits>
这行会被展开为：
printf("int max: %s/n", "INT_MAX");

printf("%s/n", CONS(A, A));// compile error 
这一行则是：
printf("%s/n", int(AeA));
```
INT_MAX和A都不会再被展开, 然而解决这个问题的方法很简单. 加多一层中间转换宏.
加这层宏的用意是把所有宏的参数在这层里全部展开, 那么在转换宏里的那一个宏(_STR)就能得到正确的宏参数.
```cpp
#define A           (2)
#define _STR(s)     #s
#define STR(s)      _STR(s)          // 转换宏
#define _CONS(a,b)  int(a##e##b)
#define CONS(a,b)   _CONS(a,b)       // 转换宏

// INT_MAX,int型的最大值，为一个变量 ＃i nclude<climits>
printf("int max: %s/n", STR(INT_MAX));
输出为: int max: 0x7fffffff
STR(INT_MAX) -->  _STR(0x7fffffff) 然后再转换成字符串；

printf("%d/n", CONS(A, A));
输出为：200
CONS(A, A)  -->  _CONS((2), (2))  --> int((2)e(2))
```

## # 和 ## 的一些应用特例
- 合并匿名变量名
```cpp
#define  ___ANONYMOUS1(type, var, line)  type  var##line
#define  __ANONYMOUS0(type, line)  ___ANONYMOUS1(type, _anonymous, line)
#define  ANONYMOUS(type)  __ANONYMOUS0(type, __LINE__)

例：ANONYMOUS(static int);  即: static int _anonymous70;  70表示该行行号；
第一层：ANONYMOUS(static int);  -->  __ANONYMOUS0(static int, __LINE__);
第二层：                        -->  ___ANONYMOUS1(static int, _anonymous, 70);
第三层：                        -->  static int  _anonymous70;
```
即每次只能解开当前层的宏，所以__LINE__在第二层才能被解开

- 填充结构
```cpp
#define  FILL(a)   {a, #a}

enum IDD{OPEN, CLOSE};
typedef struct MSG{
  IDD id;
  const char * msg;
}MSG;

MSG _msg[] = {FILL(OPEN), FILL(CLOSE)};
相当于：
MSG _msg[] = {{OPEN, "OPEN"}, {CLOSE, "CLOSE"}};
```
- 记录文件名
```cpp
#define  _GET_FILE_NAME(f)   #f
#define  GET_FILE_NAME(f)    _GET_FILE_NAME(f)
static char  FILE_NAME[] = GET_FILE_NAME(__FILE__);
```
- 得到一个数值类型所对应的字符串缓冲大小
```cpp
#define  _TYPE_BUF_SIZE(type)  sizeof #type
#define  TYPE_BUF_SIZE(type)   _TYPE_BUF_SIZE(type)
char  buf[TYPE_BUF_SIZE(INT_MAX)];
     -->  char  buf[_TYPE_BUF_SIZE(0x7fffffff)];
     -->  char  buf[sizeof "0x7fffffff"];
这里相当于：
char  buf[11];
```