# Where is flag？  - WriteUp

> **Created by：** A-little-dragon
>
> **Team：** TracelessSec
>
> **题型：**  代码审计



## Writeup

### step 1 查看PHP代码

1. F12在检查元素中可以看到隐藏的PHP代码

![image-20240815165919876](.\image\image-20240815165919876.png)

```php
<?php 

foreach ($_POST as $item => $value){
    $$item=$$value;
    $secret = $$item;
}
foreach ($_GET as $key => $value){
    if ($key=='flag'){
        $str=$value;
        $$str=$secret;
    }
}
if (isset($hehe)){
    echo "<center>".$hehe."</center>";
}
//flag+flaag=DASCTF{XXXXXXX}

?>
```

### step 2 分析

从下面代码可知，如果存在`$hehe`就会输出`echo "<center>".$hehe."</center>";`

```php
if (isset($hehe)){
    echo "<center>".$hehe."</center>";
}
```

继续查看，发现在该循环体中，会从循环拿到POST请求中的键值参数，并对$$item和$secret进行赋值，实际上只是对$secret进行赋值：`$secret=$$value;`

```php
foreach ($_POST as $item => $value){
    $$item=$$value;
    $secret = $$item;
}
```

接着，发现会从循环拿到GET请求中的键值参数，如果键为flag，就会对$str和$$str进行赋值。$secret是上个循环中的$$item的值；

```php
foreach ($_GET as $key => $value){
    if ($key=='flag'){
        $str=$value;
        $$str=$secret;
    }
}
```

因此，我们需要让`$key = flag`，且让`isset($hehe)`为真的话，就要让`$str=hehe；`，进而GET请求则为如下：

```
GET请求 ==> ?flag=hehe
```

接着，我们还需要构造POST请求才行；这里的$item任意，显而易见，GET请求用于创建一个变量$hehe，而POST请求则将$secret的值赋值给$hehe；于是可以构造如下：

```php
POST请求 ==> a=???
```

但是这里的$value不知道是什么，但是通过前面可以知道最后输出的结果正是$secret的值，因为$secret把它的值赋值给了$hehe；

加上题目已经提示了`//flag+flaag=DASCTF{XXXXXXX}`，所以，最后构造如下的payload即可访问flag的值；

```php
第一次请求：
    GET请求 ==> ?flag=hehe
    POST请求 ==> a=flag
    
第二次请求：
    GET请求 ==> ?flag=hehe
	POST请求 ==> a=flaag
```

![image-20240815172658688](.\image\image-20240815172658688.png)

![image-20240815172839978](.\image\image-20240815172839978.png)



最终**flag：** `DASCTF{27b62da69e01bf1ad3e4e737c2b8f4a1}`
