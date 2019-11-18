---
layout: post
title: PHP几个基础知识
author: "Eheieh"
date: 2019-11-01
header-img: "photos/20191111gushu/DSC00982.jpg"
tags:
  - PHP基础
---


翻译自：[PHP The Right Way - The Basics](https://phptherightway.com/pages/The-Basics.html)

# The Basics

## 比较运算符

PHP比较运算符是一个经常被被忽略的方面，这可能导致许多难以意料的问题。一个问题来自严格比较（整数作为布尔值的比较）。

```php
$a = 5;   // 5 as an integer

var_dump($a == 5);       // compare value; return true
var_dump($a == '5');     // compare value (ignore type); return true
var_dump($a === 5);      // compare type/value (integer vs. integer); return true
var_dump($a === '5');    // compare type/value (integer vs. string); return false

//Equality comparisons
if (strpos('testing', 'test')) {    // 'test' is found at position 0, which is interpreted as the boolean 'false'
    // code...
}

// vs. strict comparisons
if (strpos('testing', 'test') !== false) {    // true, as strict comparison was made (0 !== false)
    // code...
}
```

参考：
- [比较运算符](https://www.php.net/language.operators.comparison)
- [PHP 类型比较表](https://www.php.net/types.comparisons)
- [比较备忘表](https://phpcheatsheets.com/compare/)，很全面的表，包含不同版本。

## 条件语句

### if 条件

当 `if else` 条件存在一个函数或类中，一个普遍误解是认为 `else` 必须被用于结合潜在结束声明。
然而如果结束定义了返回值，`else` 就不在需要作为 `return` 在函数结尾，使 `else` 变得毫无意义。

```php
function test($a)
{
    if ($a) {
        return true;
    } else {
        return false;
    }
}

// vs.
function test($a)
{
    if ($a) {
        return true;
    }
    return false;    // else is not necessary
}

// or even shorter:
function test($a)
{
    return (bool) $a;
}
```

参考：
- [If 条件](https://www.php.net/control-structures.if)


### switch 条件

`switch` 条件是避免无止境 `if ... elseif ...` 复制的好方式，但是有些点你需要知道：
- `switch` 条件只会比较值，不会比较类型（相当于 `==`）。
- 重复比较 `case` 知道被匹配，如果没有一个匹配，就会匹配到 `default`（如果定义了）。
- 如果一个 `break`，那么会执行每个 `case` 直到遇到一个 `break` 或 `return`。
- 在一个函数中，使用 `return` 代替 `break` 作为函数结束。
   ```php
   $answer = test(2);    // the code from both 'case 2' and 'case 3' will be implemented

   function test($a)
   {
       switch ($a) {
           case 1:
               // code...
               break;             // break is used to end the switch statement
           case 2:
               // code...         // with no break, comparison will continue to 'case 3'
           case 3:
               // code...
               return $result;    // within a function, 'return' will end the function
           default:
               // code...
               return $error;
       }
   }
   ```

参考：
- [Switch 条件](https://www.php.net/control-structures.switch)
- [PHP switch](http://phpswitch.com/)

## 全局命名空间

当我使用命名空间，你可能发现内置函数被你所写的函数隐藏了（就是与内置函数重名）。为解决这个问题，可以在全局函数名称之前使用一个反斜杠。

如果没有定义任何命名空间，所有的类与函数的定义都是在全局空间。

```php
<?php
namespace phptherightway;

function fopen()
{
    $file = \fopen();    // Our function name is the same as an internal function.
                         // Execute the function from the global space by adding '\'.
}

function array()
{
    $iterator = new \ArrayIterator();    // ArrayIterator is an internal class. Using its name without a backslash
                                         // will attempt to resolve it within your namespace.
}
```

参考：
- [全局空间](https://www.php.net/language.namespaces.global)
- [用户空间命名指南](https://www.php.net/userlandnaming.rules)


## 字符串

### 串联

- 如果一行超过推荐的行长（120字符），考虑连接一行。
- 为了可读性，最好使用连接运算符（`.`）而不是连接赋值运算符（`.=`）。
- 当在变量原始范围内，使用新行缩进连接。

```php
$a  = 'Multi-line example';    // concatenating assignment operator (.=)
$a .= "\n";
$a .= 'of what not to do';

// vs
$a = 'Multi-line example'      // concatenation operator (.)
    . "\n"                     // indenting new lines
    . 'of what to do';
```

参考：
- [字符串运算符](https://www.php.net/language.operators.string)

### 字符串类型

字符串是一系列字符，这听起来很相当地简单。
也就说，有几种不同类型的字符串，它们提供的语法略有不同，它们的行为也略有不同。

### 单引号

单引号被用来表示“字面字符串”。字面字符串不会尝试去解析特殊字符或变量。

如果使用单引号，你在里面使用变量名如 `'some $thing'`，你或看到精确输出 `some $thing`。
如果使用双引号，PHP会尝试解析 `$thing` 变量名或提示错误（如果变量不存在）。

```php
echo 'This is my string, look at how pretty it is.';    // no need to parse a simple string

// Output: This is my string, look at how pretty it is.
```

参考：
- [单引号](https://www.php.net/language.types.string#language.types.string.syntax.single)

### 双引号

双引号如字符串的瑞士军刀。它们不止会如上所述解析变量，还包括所有种类的特殊字符，如 `\n` 表示换行符，`\t` 表示制表符，等等。

```php
echo 'phptherightway is ' . $adjective . '.'     // a single quotes example that uses multiple concatenating for
    . "\n"                                       // variables and escaped string
    . 'I love learning' . $code . '!';

// vs
echo "phptherightway is $adjective.\n I love learning $code!"  // Instead of multiple concatenating, double quotes
```

双引号能包含变量，这被称为“内插法”。

```php
$juice = 'plum';
echo "I like $juice juice";    // Output: I like plum juice
```

当使用内插法时，通常意味着这变量会接触其他字符。这可能会带来一些混乱，哪些是变量名，哪些是字面字符。

为解决这个问题，我们用花括号包裹变量。

```php
$juice = 'plum';
echo "I drank some juice made of $juices";    // $juice cannot be parsed

// vs
$juice = 'plum';
echo "I drank some juice made of {$juice}s";    // $juice will be parsed

/**
 * Complex variables will also be parsed within curly brackets
 */

$juice = array('apple', 'orange', 'plum');
echo "I drank some juice made of {$juice[1]}s";   // $juice[1] will be parsed
```

参考：
- [双引号](https://www.php.net/language.types.string#language.types.string.syntax.double)

### Nowdoc 语法

Nowdoc 语法是在5.3中引入，其内部行为与单引号相同，只是它适合多行字符串使用，而不需要连接符。

```php
$str = <<<'EOD'             // initialized by <<<
Example of string
spanning multiple lines
using nowdoc syntax.
$a does not parse.
EOD;                        // closing 'EOD' must be on it's own line, and to the left most point

/**
 * Output:
 *
 * Example of string
 * spanning multiple lines
 * using nowdoc syntax.
 * $a does not parse.
 */
```
参考：
- [Nowdoc 语法](https://www.php.net/language.types.string#language.types.string.syntax.nowdoc)


### Heredoc 语法

Heredoc 语法其内部行为与双引号相同，只是它适合多行字符串使用，而不需要连接符。

```php
$a = 'Variables';

$str = <<<EOD               // initialized by <<<
Example of string
spanning multiple lines
using heredoc syntax.
$a are parsed.
EOD;                        // closing 'EOD' must be on it's own line, and to the left most point

/**
 * Output:
 *
 * Example of string
 * spanning multiple lines
 * using heredoc syntax.
 * Variables are parsed.
 */
```
参考：
- [Heredoc 语法](https://www.php.net/language.types.string#language.types.string.syntax.heredoc)

> 需要注意，多行字符串也能通过多行的形式声明，如。

```php
$str = "
Example of string
spanning multiple lines
using statement syntax.
$a are parsed.
";

/**
 * Output:
 *
 * Example of string
 * spanning multiple lines
 * using statement syntax.
 * Variables are parsed.
 */
```

### 哪种快些？

有一个说法是单引号字符串比双引号字符串快一点点，这基本不是真的。

如果你定义一个单一字符串，不去尝试连接值或其他复杂操作，那么使用单引号和双引号完全一样，同样快。

如果你连接任何类型的多个字符串，或将值插入一个双引号，那么结果可能有所不同。
如果是少量值，连接会更快；如果是很多值，那么插入会更快。

无论你对字符串做什么，没有一种类型会对改善应用程序性能有显而易见的影响，尝试以这些方法重写代码通常是徒劳的。
所以避免这些微观优化，除非你真正理解它们的意义和区别。

参考：
- [反驳单引号性能神话](http://nikic.github.io/2012/01/09/Disproving-the-Single-Quotes-Performance-Myth.html)


## 三元运算符

三元运算符是精简代码的好方法，但经常被过度使用。
三元运算符可以被堆叠或嵌套，建议<font color="blue">一行一个</font>增强可读性。

```php
$a = 5;
echo ($a == 5) ? 'yay' : 'nay';
```

相比之下，下面代码为了减少行数牺牲了可读性。

```php
echo ($a) ? ($a == 5) ? 'yay' : 'nay' : ($b == 10) ? 'excessive' : ':(';    // excess nesting, sacrificing readability
```

如果 `return` 和三元运算符，请使用正确语法。

```php
$a = 5;
echo ($a == 5) ? return true : return false;    // this example will output an error

// vs

$a = 5;
return ($a == 5) ? 'yay' : 'nope';    // this example will return 'yay'
```

需要注意，你不需要使用三元运算符返回布尔值，如下。

```php
$a = 3;
return ($a == 3) ? true : false; // Will return true if $a == 3 or false

// vs

$a = 3;
return $a == 3; // Will return true if $a == 3 or false
```

这也适用于所有运算符（`===`、`!==`、`!=`、`==`等）。


### 利用括号改善三元运算符

但使用三元运算符时，括号能改善代码可读性，同时包含语句联结块。
一个非必须使用括号的例子：

```php
$a = 3;
return ($a == 3) ? "yay" : "nope"; // return yay if $a == 3 or nope

// vs

$a = 3;
return $a == 3 ? "yay" : "nope"; // return yay if $a == 3 or nope
```

括起来可以联结语句，使语句以一个整体或块被检查。
如下，如果 `($a == 3 && $b == 4)` 为 `true`，且 `$c === 5` 为 `true`，则返回 `true`。

```php
return ($a == 3 && $b == 4) && $c == 5;
```

另外例子，如果 `($a != && $b != 4)` 或 `$c == 5` 返回 `true`。

```php
return ($a != 3 && $b != 4) || $c == 5;
```

从5.3起，可以省略三元运算符中间部分。如 `expr1 ?:expr3`，`expr1` 为 `true`，返回 `expr1`，否则返回 `expr3`。

参考：
- [三元运算符](https://www.php.net/language.operators.comparison)

## 变量声明

有时，程序员视图通过声明不同名称的变量使得代码“更干净”，实际上，这将造成内存使用翻倍。
如下，假设一个字符串包含 1MB 数据，通过复制这个变量，会增加脚本执行 2MB。

```php
$about = 'A very long string of text';    // uses 2MB memory
echo $about;

// vs

echo 'A very long string of text';        // uses 1MB memory
```

参考：
- [几点性能优化](http://web.archive.org/web/20140625191431/https://developers.google.com/speed/articles/optimizing-php)









