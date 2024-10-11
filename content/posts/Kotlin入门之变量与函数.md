# Kotlin入门之变量与基本的程序结构



## 变量

在Kontlin中变量的声明和基本使用是和Java不太相同的。

### 变量的声明

- 声明的格式

val 变量名:类型 或者 var 变量名:类型 

在这里面主要是val和var的区别，val（value的简写）用来声明一个不可变的变量，这种变量在初始赋值之后就再也不能重新赋 值，对应Java中的final变量。 var（variable的简写）用来声明一个可变的变量，这种变量在初始赋值之后仍然可以再被重新 赋值，对应Java中的非final变量。

### 那么为什么Kontlin这么看重于变量的可变和不可变呢？

在Java中，除非你主动在变量前声明了final关键字，否则这个变量就是可变的，这样显然并不是一件好事，因为在后续开发中可能不知道谁就会将你定义的变量给修改了，然后就有可能在某些奇奇怪怪的地方出现问题了。因此时刻管理好自己的变量是很重要的。但是，不是每个人都能养成这种良好的编程习惯。
所以Kontlin在设计之初就提供了明确的规则使用val和var来声明变量的是否可变。这样的一种设计思想在现在的编程语言很常见，rust也有这样的设计风格。

**Java和Kotlin数据类型对照表**

![](https://i.postimg.cc/k56kXF5W/screenshot-34.png)



## 程序的逻辑控制结构

### 条件语句if和when

```kotlin
fun ifStructure(num1:Int,num2:Int):Int{
    var value=0
    if (num1>num2){
        value =num1
    } eles{
        value=num2
    }
    return value
}
```

上面是Kotlin的if语句的基本使用基本和Java的if一样。

Kotlin中的if语句相比于Java有一个额外的功能，它是可以有返回值的,具体实现如下面的代码

```kotlin
fun largerNumber(num1: Int, num2: Int): Int {
 	val value = if (num1 > num2) {
 				num1
 			} else {
				num2
 			}
 	return value
}
```



