---
layout:     post
title:      "continue、break、goto、exit的区别"
subtitle:   ""
date:       2019-11-15
author:     "Eheieh"
header-img: "img/php/base/continue-bg.jpg"
tags:
    - PHP基础
---


## continue

- 放弃本次循环，进入下一次。
- <font color='red'>只能</font>在循环结构中使用。

- 接受<font color='red'>可选参数</font>，决定跳出几重循环，`continue ;` 默认是1。

``` php
$i = 0;
while ($i++ < 5) {
    echo "Outer<br />\n";
    while (1) {
        echo "Middle<br />\n";
        while (1) {
            echo "Inner<br />\n";
            continue 3;
        }
        echo "This never gets output.<br />\n";
    }
    echo "Neither does this.<br />\n";
}
```





## break

- 跳出当前<u>语法结构</u>，可以用在循环和switch中。
- break n，加参数n，表示跳出循环的层数。

``` php
/* 使用可选参数 */
$i = 0;
while (++$i) {
    switch ($i) {
    case 5:
        echo "At 5<br />\n";
        break 1;  /* 只退出 switch. */
    case 10:
        echo "At 10; quitting<br />\n";
        break 2;  /* 退出 switch 和 while 循环 */
    default:
        break;
    }
}
```



<font style='background-color:yellow'>continue、break不能使用在if条件结构中，如果非要使用，必须套在循环结构中</font>，如。

``` php
$i = 0;
$s = 1;
while ($i == 0) {
    if ($s == 1) {
        $v = 8;
        break;
    }
    $v = 9;
}
```





## goto

- 是一个<font color='red'>操作符</font>，将程序从当前位置跳转到其他任意位置。

- goto <u>本身并没有结束循环的作用</u>。

- <font color='red'>限制</font>：目标位置只能位于同一个文件和作用域。

  > 无法跳出一个函数或方法，无法跳入另一个函数。
  >
  > 也无法跳入任何循环或 switch 结构中。跳出循环和switch，相当于用goto代替多层break。

- 尽量<font color='red'>避免</font>使用goto，降低代码可读性。

```php
// 检测1000以内，那些数的平方根大于29。
for ($i = 1000; $i >= 1; $i--) {
    if (sqrt($i) <= 29) {
        goto a;
    }
    echo $i.'</br>';
}
a:
echo "end";
```



## exit()

- exit() 是一个<font color='red'>语法结构</font>，不是函数。
- 用来结束程序执行，可用在任何地方，本身没有跳出循环含义。相当于 die()。
- 不带参数可以省略括号。
- <font color='red'>参数</font>为字符串时，会直接打印，如果为<font color='blue'>integer</font>（0~254），会作为退出状态码，并不会打印输出。

``` php
$filename = '/path/to/data-file';
$file = @fopen($filename, 'r')
or exit("unable to open file ($filename)");
```
