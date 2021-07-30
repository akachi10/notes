

# go语言编程快速入门

## Go语言简介

Go语言是一个编译语言与java的jvm不同go语言直接编译为可执行的二进制文件。并且具有垃圾回收机制，可以理解为真正的编译语言的java 并且跨平台！

### 使用go的平台

k8s bilibili

## 准备工作

- go install package

  - 为了解决网络不可达的问题输入

    ```shell
    go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct
    ```

- Visual Studio Code

  - 安装go插件记得安装谷歌团队的
  - 安装go工具包 Ctrl + Shift + P 输入 'go install' 点击后勾选所有工具包

## go语言数据类型

## 语法

#### 一等函数



#### 类型别名

![image-20210720112400398](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/112413.png)

### 类型

![image-20210716133148578](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/16/133150.png)

u开头的都是无符号的

- string 字符串 0值是""
  - 允许使用`写入多行字符串
- rune 是int32的别名
  - 用于再unicode中表示一个字符
- byte 是uint8的别名

### 语法

#### 针对类型增加方法

![image-20210721113008725](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210721113008725.png)

go 语言没有对象

可以通过这种方式添加方法

```go
package main

import "fmt"

type celsius float64
type kelvin float64

func kelvinToCelsius(k kelvin) celsius {
	return celsius(k - 273.15)
}
func (k celsius) celsius() celsius {
	return celsius(k - 273.15)
}
func main() {
	var t1 celsius = 2173.15
	fmt.Print(t1.celsius())
}

```

##### 方法体解释

![image-20210727171703271](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210727171703271.png)



##### 这里非常关键的一点是创建新的方法不能用"="号

#### 声明类型

```go
//为了提高代码的可读性
type newType = float64
var jily newType = 23.1
```



#### 声明函数

![image-20210720165723542](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/165724.png)

##### 大写声明的函数都可以再其他包调用

##### 多个返回值

![image-20210720170402239](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/170403.png)

返回多个值的时候可以通定义每个返回值的名字

##### 可变参数函数

比如fmt.Println

```go
func Println(a ...interface{}) (n int, err error) {
	return Fprintln(os.Stdout, a...)
}
```

![image-20210720170730051](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/170731.png)

##### 值类型与引用类型

形参中如果指定值类型比如float64那么改变方法内的参数并不会影响方法外的值	

#### range 迭代

![image-20210720155758184](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/155759.png)

#### go中的隐式类型转换

- 各个类型包括int 与 int8 都不可以进行转换
- 可以通过 := float64(进行强制类型转换等同于JAVA的) (float64)
- 如果想要将数值与string 进行拼接需要用strconv.Itoa()

#### go里面的函数可以返回多个值

#### 要点

- 浮点类型不适合做金融类计算导致最小舍弃入

- 先做乘法再做出发会更加精确
- 使用大数的时候直接赋值就可以了
- 如果需要放弃其中一个参数的话可以用_来替代
- 环绕现象，不同数值类型进行转换时可能发生环绕现象需要判断其最大值最小值，环绕并不会导致异常

#### 声明变量和常量

 ```go
 //例子中专门用了为二的两种特殊声明方式
 	const (
 		lightSpeed = 299792 //km
 	)
 	var distance, speed = 56000000, 30 //km
 	speed += 1
 	fmt.Println(distance/lightSpeed, "seconds")
 	distance = 41000000 //km
 	fmt.Println(distance/lightSpeed, "seconds")
 
 ```

#### 复值运算符

```go
+= *= /= -= age++ //不能写 ++age
```

#### if 和 switch

> switch中需要以**fallthrough**来继续执行下面的case体中的内容并且不进行对比

```shell
package main

import "fmt"

func main() {
	var room = "lake"

	switch {
	case room == "cave":
		fmt.Println("You find yourself in a dimly lit cavern.")
	case room == "lake":
		fmt.Println("The ice seems solid enough.")
		fallthrough
	case room == "underwater":
		fmt.Println("The water is freezing cold.")
	}
}

```

#### for

```go
	for i := 0; i < 300; i++ {

		var izd = rand.Intn(232)

		if izd == 1 {
			fmt.Print("a,")
		} else {
			fmt.Print(izd, ",")
		}
		if i%30 == 0 {
			println()
		}
	}

```

#### 作用域

有个特殊的package 级别的作用域变量

只能用var 生成 

#### 短声明:=

等同于var 不过在比如for 后面声明变量就可以使用:=来进行声明

#### 带类型声明

var days float32

#### 对于utf8的处理



```go
// utf8.RuneCountInString包中的函数能够识别期中长度
	fmt.Println(len(question), "bytes")
	fmt.Println(utf8.RuneCountInString(question), "runes")


	for i, c := range question {
		fmt.Printf("%v  %c \n", i, c)
	}
```



### 基础包

- fmt

  - printf  print fmrmat

    - 例子:fmt.printf("zezeze %v+%v","asdasd",1+1)

    格式化动词:

    ​	%v 填充对应字符串

    ​    %4v 保持4格长度 如果不够填充空格

    ​	%f 指代float

    ​    %.3f指代保留三位小数点

    ​    %4.4f 填充4为 最多保留2位小数点，如果超过设置则会以实际d位数决定

    ![image-20210716131223213](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/16/131236.png)

    ​    %05.2f如果在填充位数与%之间的内容会被作为填充物，只能输入0

    ​    %t 会打印数据类型

    ​    %x 打印16进制

    ​    %b 打印每个位

  - println

  - print

- rand 生成随机数

  - intn(10)

- strings 字符串处理

  - Contains 对比是否在其中存在

- time

  - sleep
  
- big

- utf8

  - 操纵多语言字符串包

- strconv 

  - itoa 

  ![image-20210720143319704](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/20/143322.png)

## 附录

### 概念

- package 等同于java
  - 通过 import引入包
- function 等同于java
  - func 声明函数

### 相关资源

- 官网

  https://go.dev/

- 下载地址

  https://dl.google.com/go/go1.16.6.windows-amd64.msi

- Visual Studio Code

  https://code.visualstudio.com/

## 课堂笔记与作业

### 第二课

-  