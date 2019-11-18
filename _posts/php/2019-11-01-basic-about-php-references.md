---
layout: post
title: PHP的引用
author: "Eheieh"
date: 2019-11-01
header-img: "photos/20191111gushu/DSC00984.jpg"
tags:
  - PHP基础
---

参考：
- [引用的解释](https://www.php.net/manual/zh/language.references.php)

### 引用是什么？

允许用两个变量来指向同一个内容。如：
```php
$a = &$b;
// $a 和 $b 在这里是完全相同的，这并不是 $a 指向了 $b 或者相反，而是 $a 和 $b 指向了同一个地方。
```

- 在PHP中意味着用不同的名字访问同一变量内容。
- 不像C的指针，并不是实际的内存地址，<font color="blue">引用是符号表别名。</font>可以被看作是 Unix 文件系统中的硬链接。

### 引用不是什么？
- <font color="blue">引用不是指针。</font>
- 不能通过引用机制将函数调用范围内的变量绑定到别的变量上面。

举例：

In PHP:
```php
$a = 0;
$b = &a;
echo $a; //0
unset($b); // unset $b
echo $a; //0 it's OK
```

In C:
```c
#include <stdio.h>
int main(int argc, char const *argv[]) {
    int a = 0;
    int* b = &a;

    printf("%i\n", a); //0
    free(b);              // free b
    printf("%i\n", a); //get error: *** error for object 0x7fff6350da08: pointer being freed was not allocated
}
```

### 引用传递

可以将一个变量通过引用传递给函数，这样该函数就可以修改其参数的值。

```php
function foo(&$var)
{
    $var++;
}

$a=5;
foo($a);
echo $a; // $a is 6 here
```

以下内容可以通过引用传递：

- 变量，例如 foo($a)。
  
> foo(&$a)，报 PHP Fatal error:  Call-time pass-by-reference has been removed...

- New 语句，例如 foo(new foobar())

- 从函数中返回的引用，例如：

  ```php
  function &bar()
  {
      $a = 5;
      return $a;
  }
  foo(bar());
  ```

以下引用传递无效：

```php
function foo(&$var)
{
    $var++;
}
function bar() // Note the missing &
{
    $a = 5;
    return $a;
}
foo(bar()); // 自 PHP 5.0.5 起导致致命错误，自 PHP 5.1.1 起导致严格模式错误
            // 自 PHP 7.0 起导致 notice 信息
foo($a = 5); // 表达式，不是变量
foo(5); // 导致致命错误
```

### 引用返回

引用返回用在当想用函数找到引用应该被绑定在哪一个变量上面时。

```php
class foo {
    public $value = 42;

    public function &getValue() {
        return $this->value;
    }
}

$obj = new foo;
$myValue = &$obj->getValue(); // $myValue is a reference to $obj->value, which is 42.
$obj->value = 2;
echo $myValue;                // prints the new value of $obj->value, i.e. 2.
```

### 取消引用

当 unset 一个引用，只是断开了变量名和变量内容之间的绑定。这并不意味着变量内容被销毁了。例如：

```php
$a = 1;
$b =& $a;
unset($a);
```

### 引用定位
其它使用引用的结构有：

#### global 引用
当用 `global $var` 声明一个变量时实际上建立了一个到全局变量的引用。也就是说和这样做是相同的：

```php
$var =& $GLOBALS["var"];
```
#### $this
在一个对象的方法中，`$this` 永远是调用它的对象的引用。

