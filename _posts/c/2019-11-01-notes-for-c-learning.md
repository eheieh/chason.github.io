---
layout: post
title: C语言学习
subtitle: C语言的学习笔记
author: "Eheieh"
date: 2019-11-01
header-img: "photos/20191111gushu/DSC01023.jpg"
tags:
  - C语言
---

Windows操作系统：Cygwin（http://www.cygwin.com） 、MinGW（http://www.mingw.org） 

Sublime环境配置，Tools->Build System->New Build System... 添加下面配置并保存为c.sublime-build，重启Sublime就可以通过快捷键Ctrl+Shift+B编译c文件了。

```json
{
	"working_dir": "$file_path",
	"cmd": "gcc -Wall \"$file_name\" -o \"$file_base_name\"",
	"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
	"selector": "source.c",
	"variants":
	[
		{
			"name": "Run",
	        "shell_cmd": "gcc -Wall \"$file\" -o \"$file_base_name\" && start cmd /c \"${file_path}/${file_base_name} & pause\""
		}
	]
}
```

三种C标准：

- ANSI C，20世纪80年代后期
- C99，1999年
- C11，2011年



一切从“hello, world”开始：

```c
#include <stdio.h>
main()
{
	printf("hello, world\n");
}
```



- 一个C程序，无论大小，都由函数和变量组成；

- main是个特殊的函数名，是每个程序的起点；

- printf函数用于格式化输出，只能用\n表示换行符，下面情况会报错：

  ```c
  printf("hello, world
         ");
  ```

  printf函数永远不会自动换行。

  ```c
  printf("hello, ");
  printf("world");
  printf("\n");
  ```

  > Note：printf函数并不是C语言本身的一部分，C语言本身并没有定义输入/输出功能。printf仅是标准库函数中一个有用函数而已。




# 入门

## 变量和算术表达式

C中，所有变量都<font color='red'>必须先声明后使用</font>。

声明用于说明变量的属性，包含类型名和变量表组成。

C提供的基本数据类型：

- int（16位和32位）
- float（通常32位，至少包含6位有效位，范围一般在$10^{-38}$~ $10^{+38}$）
- char：字符（一个字节）
- short：短整型
- long：长整型
- double：双精度浮点型

> 这些数据类型对象的大小也取决于具体的机器。

利用公式 $oC=(5/9)(oF-32)$ 打印华氏温度与摄氏温度对照表：

```c
#include <stdio.h>
/* print Fahrenheit-Celsius table
for fahr = 0, 20, ..., 300 */
main()
{
	int fahr, celsius;
	int lower, upper, step;
	lower = 0; /* lower limit of temperature scale */
	upper = 300; /* upper limit */
	step = 20; /* step size */
	fahr = lower;
	while (fahr <= upper) {
		celsius = 5 * (fahr-32) / 9;
		printf("%3d\t%6d\n", fahr, celsius);
		fahr = fahr + step;
	}
}
```

注意，`celsius = 5 * (fahr-32) / 9; `，不是直接用公式的 `5 / 9`，因为C语言中整数相除将执行**舍位**，结果将为0。

声明为浮点数，计算包含小数后2位：

```c
#include <stdio.h>
/* print Fahrenheit-Celsius table
for fahr = 0, 20, ..., 300; floating-point version */
main()
{
	float fahr, celsius;
	float lower, upper, step;
	lower = 0; /* lower limit of temperatuire scale */
	upper = 300; /* upper limit */
	step = 20; /* step size */
	fahr = lower;
	while (fahr <= upper) {
		celsius = (5.0 / 9.0) * (fahr-32.0);
		printf("%3.0f %6.2f\n", fahr, celsius);
		fahr = fahr + step;
	}
}
```

反过来摄氏温度与华氏温度对照表：

```c
#include <stdio.h>
main()
{
	float fahr, celsius;
	float lower, upper, step;
	lower = -17;
	upper = 100; 
	step = 1;
	celsius = upper;
	while (celsius >= lower) {
		fahr = (celsius / (5.0 / 9.0)) + 32.0;
		printf("%6.0f  %6.2f\n", celsius, fahr);
		celsius = celsius - step;
	}
}
```



## for语句

用for来实现上面的程序：

```c
#include <stdio.h>
/* print Fahrenheit-Celsius table */
main()
{
	int fahr;
	for (fahr = 0; fahr <= 300; fahr = fahr + 20)
		printf("%3d %6.1f\n", fahr, (5.0/9.0)*(fahr-32));
}
```

C语言中一个通用规则的实例：在允许使用某种类型变量值的任何场合，都可以使用该类型的更复杂的表达式。

> This last change is an instance of a general rule - in any context where it is permissible to use the value of some type, you can use a more complicated expression of that type. 

相对于while的实现，for语句结构更为清晰紧凑。<font color='blue'>for语句比较适合初始化和增加步长都是单条语句并且逻辑相关的情形。</font>



## 符号常量

格式：`#define  名字  替换文本`

- 常量命名规则与变量命名规则相同；
- 符号常量命名通常用大写字母拼写；
- 替换文本可以是任何字符序列；
- #define指令行末尾没有分号。

我们把之前程序0、300、20等称为“**幻数（magic numbers）**”，即无法给阅读程序的人提供任何信息，且会使程序修改变得困难。可以使用符号常量代替。

```c
#include <stdio.h>
#define LOWER 0 /* lower limit of table */
#define UPPER 300 /* upper limit */
#define STEP 20 /* step size */
/* print Fahrenheit-Celsius table */
main()
{
	int fahr;
	for (fahr = LOWER; fahr <= UPPER; fahr = fahr + STEP)
		printf("%3d %6.1f\n", fahr, (5.0/9.0)*(fahr-32));
}
```



## 字符输入/输出

**文本流**是由多行字符构成的字符序列。

读/写一个字符的函数：getchar和putchar。

### 文件复制

把输入一次一个字符地复制到输出。

```c
#include <stdio.h>
/* copy input to output; 1st version */
main()
{
	int c;//不把c声明为char类型，是因为它必须足够大，能够存储文件结束符EOF
	c = getchar();
	while (c != EOF) {
		putchar(c);
		c = getchar();
	}
}
```

- 字符在机器内部都是以**位模式**存储的；
- 在没有输入时，getchar返回一个特殊值EOF（end of file）；
  - EOF定义在头文件`<stdio.h>`中，是一个整形数。

> 结束符在Windows下是Ctrl+Z+Enter。

更精简的写法：

```c
#include <stdio.h>
/* copy input to output; 2nd version */
main()
{
	int c;
	while ((c = getchar()) != EOF)
		putchar(c);
}
```



### 字符计数

```c
#include <stdio.h>
/* count characters in input; 1st version */
main()
{
    long nc;
    nc = 0;
    while (getchar() != EOF)
    	++nc;
    printf("%ld\n", nc);
}
```

使用double处理更大的数字。

```c
#include <stdio.h>
/* count characters in input; 2nd version */
main()
{
	double nc;
	for (nc = 0; getchar() != EOF; ++nc)
		;
	printf("%.0f\n", nc);
}
```

while和for可以在执行循环体之前就对条件进行测试，有助于确保程序执行合理的操作。



### 行计数

```c
#include <stdio.h>
/* count lines in input */
main()
{
	int c, nl;
	nl = 0;
	while ((c = getchar()) != EOF)
		if (c == '\n')
			++nl;
	printf("%d\n", nl);
}
```

`\n`代表换行符的值，在ASCII字符集中其值为10。



### 单词计数

单词的定义，任何其中不包含空格、制表符或换行符的字符序列。下面是UNIX系统中wc程序的骨干部分：

```c
#include <stdio.h>
#define IN 1 /* inside a word */
#define OUT 0 /* outside a word */
/* count lines, words, and characters in input */
main()
{
	int c, nl, nw, nc, state;
	state = OUT;
	nl = nw = nc = 0;
	while ((c = getchar()) != EOF) {
		++nc;
		if (c == '\n')
			++nl;
		if (c == ' ' || c == '\n' || c == '\t')
			state = OUT;
		else if (state == OUT) {
			state = IN;
			++nw;
		}
	}
	printf("%d %d %d\n", nl, nw, nc);
}
```



## 数组

```c
#include <stdio.h>
/* count digits, white space, others */
main()
{
	int c, i, nwhite, nother;
	int ndigit[10];
	nwhite = nother = 0;
	for (i = 0; i < 10; ++i)
		ndigit[i] = 0;
	while ((c = getchar()) != EOF)
		if (c >= '0' && c <= '9')
			++ndigit[c - '0'];
		else if (c == ' ' || c == '\n' || c == '\t')
			++nwhite;
		else
			++nother;
	printf("digits =");
	for (i = 0; i < 10; ++i)
		printf(" %d", ndigit[i]);
	printf(", white space = %d, other = %d\n", nwhite, nother);
}
```



## 函数

函数为计算的封装提供了一种简便的方法；

函数不一定有返回值；

main函数 `return 0;` 表示正常终止，<font color='red'>非0</font>表示出现异常情况或出错结束条件。

```c
#include <stdio.h>
int power(int m, int n);
/* test power function */
main()
{
	int i;
	for (i = 0; i < 10; ++i)
		printf("%d %d %d\n", i, power(2,i), power(-3,i));
	return 0;
}
/* power: raise base to n-th power; n >= 0 */
int power(int base, int n)
{
	int i, p;
	p = 1;
	for (i = 1; i <= n; ++i)
		p = p * base;
	return p;
}
```

ANSI C同较早版本C之间最大区别在于函数的声明与定义方式的不同。

```c
/* power: raise base to n-th power; n >= 0 */
/* (old-style version) */
power(base, n)
int base, n;
{
	int i, p;
	p = 1;
	for (i = 1; i <= n; ++i)
		p = p * base;
	return p;
}
```



## 参数 - 传值调用

C中，所有函数都是“通过值”传递的，会创建私有的临时副本；

要让函数能够修改主调函数中的变量，需传递变量的地址（即指针）；

数组参数，当数组名作为参数，<u>传递给函数的值是数组起始元素的位置或地址</u>——它并不是复制数组元素本身。

## 字符数组

```c
#include <stdio.h>
#define MAXLINE 1000 /* maximum input line length */
int getline(char line[], int maxline);
void copy(char to[], char from[]);
/* print the longest input line */
main()
{
	int len; /* current line length */
	int max; /* maximum length seen so far */
	char line[MAXLINE]; /* current input line */
	char longest[MAXLINE]; /* longest line saved here */
	max = 0;
	while ((len = getline(line, MAXLINE)) > 0)
		if (len > max) {
			max = len;
			copy(longest, line);
		}
	if (max > 0) /* there was a line */
	printf("%s", longest);
	return 0;
}

/* getline: read a line into s, return length */
int getline(char s[], int lim)
{
	int c, i;
	for (i = 0; i < lim - 1 && (c = getchar()) != EOF && c != '\n'; ++i)
		s[i] = c;
	if (c == '\n') {
		s[i] = c;
		++i;
	}
	s[i] = '\0';
	return i;
}

/* copy: copy ’from’ into ’to’; assume to is big enough */
void copy(char to[], char from[])
{
	int i;
	i = 0;
	while ((to[i] = from[i]) != '\0')
		++i;
}
```

## 外部变量与作用域

- 自动变量（局部变量），函数中每个局部变量只在函数被调用时存在，在函数执行完毕退出时消失。

- 外部变量，在程序执行期间一直存在。

  - 必须定义在所有函数之外，且只能定义一次。
  - 在函数中使用外部变量需要加`extern`类型声明。
    - 在外部变量的定义出现在使用它的函数之前，可以省略extern声明。
    - 包含多个源文件时，习惯把extern声明放在一个单独的头文件中，后缀为`.h`。

  过分依赖外部变量会导致一定的风险，使程序中的数据关系模糊不清。

> 定义（define）：表示创建变量或分配存储单元；
>
> 声明（declaration）：说明变量的性质，但并不分配存储单元。

```c
#include <stdio.h>
#define MAXLINE 1000 /* maximum input line size */
int max; /* maximum length seen so far */
char line[MAXLINE]; /* current input line */
char longest[MAXLINE]; /* longest line saved here */
int getline(void);
void copy(void);
/* print longest input line; specialized version */
main()
{
	int len;
	extern int max;
	extern char longest[];
	max = 0;
	while ((len = getline()) > 0)
		if (len > max) {
			max = len;
			copy();
		}
	if (max > 0) /* there was a line */
	printf("%s", longest);
	return 0;
}

/* getline: specialized version */
int getline(void)
{
	int c, i;
	extern char line[];
	for (i = 0; i < MAXLINE - 1 && ( c = getchar) != EOF && c != '\n'; ++i ) 
		line[i] = c;
	
	if (c == '\n') {
		line[i] = c;
		++i;
	}
	line[i] = '\0';
	return i;
}

/* copy: specialized version */
void copy(void)
{
	int i;
	extern char line[], longest[];
	i = 0;
	while ((longest[i] = line[i]) != '\0')
		++i;
}
```



# 类型、运算符与表达式

## 类型

整型：

- signed（带符号）和unsigned（无符号）。
- 无符号常量和十六进制字符常量。

浮点运算：

- 单精度和高精度



### 变量名

- 限制：字母和数字
- 第一个字符必须为字母，包括下划线“_”（通常用于命名较长的变量名，提高可读性）。	
- 库例程的名字通常以下划线开头，因此变量名不建议以下划线开头；
- 严格区分大小写，建议变量名使用小写字母，符号常量全部使用大写字母；
- 不要使用保留关键字，如：if、else、int、float等；
- 命名要见名知意，局部变量一般较短，外部变量较长。



### 数据类型及长度

C提供几种基本数据类型：

- char：字符型，占用一个字节，可以存放本地字符集中的一个字符；
- int：整型，通常反映了所有机器中整数的最自然长度；
- float：单精度浮点型；
- double：双精度浮点型。

此外，加上一些限定符：

- short（通常为16位）、long（通常为32位）用于限定整型：

  ```c
  short int sh;
  long int counter;
  ```

  关键字int可以省略。

- signed、unsigned用于限定char类型或任何整型；

  - unsigned类型的数总是正值或0，并遵循算术模$2^n$定律。
  - 不带限定符的char类型对象是否带符号取决于具体机器，但可打印字符总是正值。

- long double表示高精度的浮点数。

> 关于类型长度定义及相关属性在标准头文件<limits.h>与<float.h>中。



### 常量

#### 整数常量

- 前缀0，表示八进制形式；
- 前缀 0x 或 0X，表示十六进制形式；

- long类型以字母 l 或 L 结尾；
- unsigned类型以字母 u 或 U 结尾；
- unsigned long类型后缀为 ul 或 UL。

#### 浮点数常量

- 表示方法：123.4 和 1e-2；
- 没有后缀的浮点数常量为double类型；
- 后缀 f 或 F 表示float类型；
- 后缀 l 或 L 表示long double类型。

#### 字符常量

- 就是一个整数；

- 括在一个单引号中；

- 字符常量`'\0'`表示值为0的字符，就是空字符（null）；

- ANSI C中的转义字符：

  ```
  \a   响铃符	   \\     反斜杠  
  \b   回退符       \?     问号
  \f   换页符       \’     单引号  
  \n   换行符       \"     双引号  
  \r   回车符       \ooo   八进制数  
  \t   横向制表符    \xhh   十六进制数  
  \v   纵向制表符
  ```

#### 常量表达式

- 仅仅只包含常量的表达式；
- 这种表达式在编译时求值，而不在运行时求值；
- 可以出现在常量可以出现的任何位置。

#### 字符串常量

- 也叫字符串字面值，用双引号括起来；

- 字符串中使用`\"`表示双引号字符；

- 字符串常量连接：`"hello, " "world"`等同于`"hello, world" `；

- 技术角度，字符串常量就是字符数组；

- 字符串内部用一个空字符`\0`作为串的结尾；

  技术字符串长度的实现：

  ```c
  /* strlen: return length of s */
  int strlen(char s[])
  {
      int i;
      while (s[i] != '\0')
      	++i;
      return i;
  }
  ```

- <font color='red'>注意字符常量与字符串常量的区别</font>：’x’ is not the same as "x" 。前者是一个整数，其值是字母 `x` 在机器字符集中对应的数字（内部表示值）；后者是一个包含一个字符 `x` 以及一个结束符 `\0` 的字符数组。

枚举常量

- 枚举是一个常量整型值的列表：

  ```c
  enum boolean { NO, YES };
  enum escapes { BELL = '\a', BACKSPACE = '\b', TAB = '\t',
  			   NEWLINE = '\n', VTAB = '\v', RETURN = '\r' };
  enum months { JAN = 1, FEB, MAR, APR, MAY, JUN,
  			  JUL, AUG, SEP, OCT, NOV, DEC };
  				/* FEB = 2, MAR = 3, etc. */
  ```

- 枚举为建立常量值与名字之间的关联提供了一种便利的方式；

- 可以以符号形式打印枚举变量的值。



### 声明

- 所有变量都必须先声明后使用；

- 一个声明指定一种变量类型，后面可带一个或多个改类型的变量；

- 可以在声明的同时对变量进行初始化；

- <font color='blue'>任何变量的声明都可以使用 const 限定符，限定变量值不能被修改</font>。

  const也可配合数组参数使用，表明函数不能修改数组元素的值：`int strlen(const char[]); `。

  <u>如果试图修改const限定的值，其结果取决于具体的实现。</u>

```c
int lower, upper, step;
char c, line[1000];

int lower;
int upper;
int step;
char c;
char line[1000];

// 声明并初始化
char esc = ’\\’;
int i = 0;
int limit = MAXLINE+1;
float eps = 1.0e-5;
```





## 运算符

### 算术运算符

二元算术运算符包括：+, -, *, /, %（取模） 

<font color='blue'>整数除法会截断结果中的小数部分。</font>

表达式 `x % y`，<font color='blue'>取模运算符 % 不能用于 float 或 double 类型</font>。有负数作数的情况下，整数除法截取的方向以及取模运算结果的符号取决于具体机器的实现。

如判断是否是闰年：

```c
if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0)
    printf("%d is a leap year\n", year);
else
    printf("%d is not a leap year\n", year);
```



优先级（从低到高）：二元运算符（+、-）  <  运算符（*、/、%）  <  一元运算符（+、-）

算术运算符采用从左到右的结合规则。

### 关系运算符与逻辑运算符

- 关系运算符：>  >=  <  <= 
  - 具有相同优先级，优先级仅次于相等性运算符：==、!=；
  - 关系运算符优先级低于算术运算符。

注意：逻辑运算符 && 和 ||，在知道结果为真或假后立即停止计算，即<font color='blue'>短路作用</font>。

如上面的 getline() 也可以如下：

```c
for (i=0; i < lim-1 && (c=getchar()) != ’\n’ && c != EOF; ++i)
    s[i] = c;
```

类型转换



# 控制流

# 函数与程序结构

# 指针与数组

# 结构

# 输入与输出

# UNIX系统接口