# 目录

- [变量](#变量)
- [数据类型](#数据类型)
- [指针](#指针)

## 标识符

### 命令规则

- 数字、字母、_ 组成
- 数字不可以开头
- “_”本身是一个特殊的标识符，称为空标识符。仅当做占位符，不能作为标识符。

## 变量

​	声明：var 变量名 数据类型

​	var a int

​	var b = 10  //如果直接赋值，可以省略数据类型

​	c := 100 //省略var，局部变量中使用，而且变量不能声明过

全局变量

~~~go
var a string = "hello"
var (
    n1 int = 100
    n2 = 200
)
~~~

## 数据类型

![数据类型](./images/数据类型.png)

1. bool，一个字节，值是true或者false，不可以用0或者1表示（java中boolean占用4个字节，而boolean作为数组出现时，每个boolean占用1个字节）

2. int/uint(带符号为与不带符号位的int类型)：根据平台不同是32位(4个字节)或者64位(8个字节)
3. intx/uintx:x代表8,16,32,64，例如：int8，代表占8bit=>1个字节的int类型
4. byte占用8bit，一个字节，相当于uint8，不带符号位
5. floatx:由于没有double类型，所以float64就是double。float32小数精确到7位，4个字节，float64小数精确到15位，8个字节。
6. complex64/complex128:复数类型
7. uintptr:保存指针用的类型，也是随着平台改变而改变，因为指针的长度就是随平台而变。
8. 其他类型值：array，struct，string
9. 引用类型：slice，map，chan
10. 接口类型：interface
11. 函数类型：func

### 基本数据类型

**int**

int默认声明为int类型

~~~go
var inta = 1
fmt.Printf("%T",inta) //int，%T输出数据类型
fmt.Printf("%d",unsafe.Sizeof(a)) //查看占用几个字节
~~~

**float64**

浮点类型默认声明float64。

~~~go
var num = 1.1
fmt.Printf("%T",num) //float64
~~~

**byte**

Go中，字符的本质是一个整数，直接输出时，是该字符对应的UTF-8编码的码值。

一个英文占用1个字节，一个汉字占用3个字节

~~~go
var c1 byte = 'z' //对于在ASCII中的字符，如[0-9,a-z,A-Z..]直接保存到byte
fmt.Println(c1) //122
fmt.Printf("%c",c1) //输出对应的unicode字符
fmt.Printf("%T",c1) //byte默认是uint8
var c2 = '哈' //或者var c2 int = '哈'，因为ASCII码值大于了255，所以不能使用byte
fmt.Printf("%T",c2) //不声明时，默认是int32
~~~

[三种常见字符编码简介-ASCII、Unicode和UTF-8](https://www.cnblogs.com/yuguangchuan/p/4310952.html)

字符类型本质探讨

 1. 字符串存储到计算机中，需要将字符对应的码值（整数）找出来

    存储：字符 => 对应码值 => 二进制 => 存储

    读取：二进制 => 码值 => 字符 => 读取

 2. Go语言，编码都统一成utf-8了。无乱码问题

**bool**占一个字节

**string**

1. 一旦赋值，不能更改。

   ~~~go
   var b  string = "hello"
   b[1] = 'a' //错误的
   ~~~

2. 使用utf-8编码标识Unicode。无乱码。

3. 本质是单个字节连接起来的。

4. 表现形式，双引号（识别转义字符），单引号（原样输出）

5. '+' 连接字符串时，+ 在上一行。

**基本类型默认值**

​	整型：	0

​	浮点型：	0

​	字符串：	“”

​	布尔：	false

**数据转换**

​	和java不一样，不能自动的隐式转换，必须显示转换

​	表达式 T(v)，把v转化为T类型

~~~go
var a int32 = 100
var b int64 = int64(a)
fmt.Println(a,b)
~~~

​	int64 转 int8编译不会报错，按溢出处理

- 基本数据转成string

  方法一：fmt.Sprintf("%参数", 表达式)

  ~~~go
  var num1 int = 99
  var str string //空的str
  str = fmt.Sprintf("%d", num1)
  fmt.Printf("str type %T str=%q\n", str, str)  //string "99"
  ~~~

  方法二：使用strconv包的函数

  [func FormatBool(b bool) string](http://docscn.studygolang.com/pkg/strconv/#FormatBool)

  [func FormatFloat(f float64, fmt byte, prec, bitSize int) string](http://docscn.studygolang.com/pkg/strconv/#FormatFloat)

  [func FormatInt(i int64, base int) string](http://docscn.studygolang.com/pkg/strconv/#FormatInt)

  [func FormatUint(i uint64, base int) string](http://docscn.studygolang.com/pkg/strconv/#FormatUint)

- string转基本类型

  使用strconv包的函数

  [func ParseBool(str string) (value bool, err error)](http://docscn.studygolang.com/pkg/strconv/#ParseBool)

  [func ParseFloat(s string, bitSize int) (f float64, err error)](http://docscn.studygolang.com/pkg/strconv/#ParseFloat)

  [func ParseInt(s string, base int, bitSize int) (i int64, err error)](http://docscn.studygolang.com/pkg/strconv/#ParseInt)

  [func ParseUint(s string, base int, bitSize int) (n uint64, err error)](http://docscn.studygolang.com/pkg/strconv/#ParseUint)

### 派生数据类型

**数组**

- 定义：var 数组名 [大小]数据类型，如var a [5]int

- 初始化

  - var a [3]int = [3]int{1,2,3}
  - var b = [3]int{1,2,3}
  - var c = [...]int{1,2,3}  //这种方式相当于[3]int，如果想要赋值给[]int，是编译错误的
  - var d = [...]int{1: 100,0: 200,2: 300}

- 遍历

  普通的fori循环，str[i]遍历是按照字节遍历的，汉字占用3个字节，所以遍历会乱码。

  range，按照字符遍历

  ```go
  str := "hello成都"
  for index,val := range str{
      fmt.Printf("index=%d,val=%c\n",index,val)
  }
  ```

  rune切片遍历

  ```go
  str := "hello成都"
  str2 := []rune(str)
  for i := 0;i<len(str2);i++ {
      fmt.Printf("val=%c\n",str2[i])
  }
  ```

- 其他函数想修改数组，可以使用引用传递（指针的方式）。默认是**值传递**

**切片**

- 切片是数组的一个引用，是**引用传递**。

- 切片是一个动态数组，长度可变

- 定义：var a []int

- 创建切片

  - var a []int = []int{1,2,3} //直接创建

  - arr := [3]int{1,2,3}   //对数组的一个引用

    s := arr[:]  // arr[startIndex:endIndex] 

  - x := make([]int, 4)  //通过make创建，make([]T, len, capacity)，capacity(容量)可选，capacity>=len

- 使用append增加，append(x, 1)

  底层是新建一个数组（按扩容后大小），把原来数组拷贝过去，切片重新指向新数组。之前的数组，等待GC回收

- 拷贝：copy(slice1，slice2)，拷贝slice2的内容到slice1

- string和slice

  sting底层是一个byte的数组，因此可以进行切片处理

## 运算符

- **无 --i，++i**
- i++只能单独一行，不能这样写i++ > 10，也不能a := i++
- <<=，左移后赋值
- \>>=，右移后赋值
- &=，按位与后赋值
- ^=，按位异或后赋值
- |=，按位或后赋值
- **不支持三目运算符**

**算术运算符**

| 运算符 | 描述 | 实例               |
| ------ | ---- | ------------------ |
| +      | 相加 | A + B 输出结果 30  |
| -      | 相减 | A - B 输出结果 -10 |
| *      | 相乘 | A * B 输出结果 200 |
| /      | 相除 | B / A 输出结果 2   |
| %      | 求余 | B % A 输出结果 0   |
| ++     | 自增 | A++ 输出结果 11    |
| --     | 自减 | A-- 输出结果 9     |

**关系运算符**

| 运算符 | 描述                                                         | 实例              |
| ------ | ------------------------------------------------------------ | ----------------- |
| ==     | 检查两个值是否相等，如果相等返回 True 否则返回 False。       | (A == B) 为 False |
| !=     | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。   | (A != B) 为 True  |
| >      | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。   | (A > B) 为 False  |
| <      | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。   | (A < B) 为 True   |
| >=     | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 | (A >= B) 为 False |
| <=     | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 | (A <= B) 为 True  |

**逻辑运算符**

| &&   | 逻辑 AND 运算符。 如果两边的操作数都是 True，则条件 True，否则为 False。 | (A && B) 为 False  |
| ---- | ------------------------------------------------------------ | ------------------ |
| \|\| | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则条件 True，否则为 False。 | (A \|\| B) 为 True |
| !    | 逻辑 NOT 运算符。 如果条件为 True，则逻辑 NOT 条件 False，否则为 True。 | !(A && B) 为 True  |

**位运算符**

&, |, 和 ^ 

| p    | q    | p & q | p \| q | p ^ q |
| ---- | ---- | ----- | ------ | ----- |
| 0    | 0    | 0     | 0      | 0     |
| 0    | 1    | 0     | 1      | 1     |
| 1    | 1    | 1     | 1      | 0     |
| 1    | 0    | 0     | 1      | 1     |

| 运算符 | 描述                                                         | 实例                                   |
| ------ | ------------------------------------------------------------ | -------------------------------------- |
| &      | 按位与运算符"&"是双目运算符。 其功能是参与运算的两数各对应的二进位相与。 | (A & B) 结果为 12, 二进制为 0000 1100  |
| \|     | 按位或运算符"\|"是双目运算符。 其功能是参与运算的两数各对应的二进位相或 | (A \| B) 结果为 61, 二进制为 0011 1101 |
| ^      | 按位异或运算符"^"是双目运算符。 其功能是参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1。 | (A ^ B) 结果为 49, 二进制为 0011 0001  |
| <<     | 左移运算符"<<"是双目运算符。左移n位就是乘以2的n次方。 其功能把"<<"左边的运算数的各二进位全部左移若干位，由"<<"右边的数指定移动的位数，高位丢弃，低位补0。 | A << 2 结果为 240 ，二进制为 1111 0000 |
| >>     | 右移运算符">>"是双目运算符。右移n位就是除以2的n次方。 其功能是把">>"左边的运算数的各二进位全部右移若干位，">>"右边的数指定移动的位数。 | A >> 2 结果为 15 ，二进制为 0000 1111  |

**赋值运算符**

| 运算符 | 描述                                           | 实例                                  |
| ------ | ---------------------------------------------- | ------------------------------------- |
| =      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C = A + B 将 A + B 表达式结果赋值给 C |
| +=     | 相加后再赋值                                   | C += A 等于 C = C + A                 |
| -=     | 相减后再赋值                                   | C -= A 等于 C = C - A                 |
| *=     | 相乘后再赋值                                   | C *= A 等于 C = C * A                 |
| /=     | 相除后再赋值                                   | C /= A 等于 C = C / A                 |
| %=     | 求余后再赋值                                   | C %= A 等于 C = C % A                 |
| <<=    | 左移后赋值                                     | C <<= 2 等于 C = C << 2               |
| >>=    | 右移后赋值                                     | C >>= 2 等于 C = C >> 2               |
| &=     | 按位与后赋值                                   | C &= 2 等于 C = C & 2                 |
| ^=     | 按位异或后赋值                                 | C ^= 2 等于 C = C ^ 2                 |
| \|=    | 按位或后赋值                                   | C \|= 2 等于 C = C \| 2               |

**其他运算符**

| 运算符 | 描述             | 实例                       |
| ------ | ---------------- | -------------------------- |
| &      | 返回变量存储地址 | &a; 将给出变量的实际地址。 |
| *      | 指针变量。       | *a; 是一个指针变量         |

**运算符优先级**

| 优先级 | 运算符           |
| ------ | ---------------- |
| 7      | ^ !              |
| 6      | * / % << >> & &^ |
| 5      | + - \| ^         |
| 4      | == != < <= >= >  |
| 3      | <-               |
| 2      | &&               |
| 1      | \|\|             |

## 进制

- 八进制以0开头
- 十六进制以0x或0X开头开头

## 控制语句

- switch，不需要加break，匹配到谁执行谁，不会往下继续执行，和java不一样

- switch/case，后面都是一个表达式，常量值、变量、又返回的函数都可以。

- 可以不加default。

- switch穿透：case后添加一个fallthrough，默认只穿透一层。

## 循环

- for循环不加（）
- **Go中无do while、while循环，只有for循环**

## goto

~~~go
goto lable1

lable1:
fmt.Println("haha")
~~~

## 函数

- 可以返回
- 可以当做形参传递
- 支持js那种... 可变传参
- 闭包

### defer

​	当执行到defer时，占时不执行，会将defer后面的语句压入到独立的栈中（defer栈），并把值拷贝一份同时入栈

​	函数执行完毕后，再从defer栈中，按照先入后出的方式出栈。

~~~go
func main() {
    res := sun(10,20)
    fmt.Println("4.", res)
}

func sun(a int,b int) int{
    defer fmt.Println("3.", a)
    defer fmt.Println("2.", b)
    a++
    b++
    res := a + b
    fmt.Println("1.", res)
    return res
}
/*
输出
1. 32
2. 20
3. 10
4. 32
*/
~~~

使用，操作文件时，创建连接后，直接defer关闭文件。连接数据库时，创建连接后，defer关闭文件。

这样就不需要为在什么时候关闭文件，或者关闭数据库烦恼。

### 值传递和引用传递

1. 值传递：int，float，bool，string，数组，结构体
2. 引用传递：指针，slice切片，map，管道chan，interface等

## 指针

## 特点

- 高并发

- 多个返回值

## 注意&规范

- 严格区分大小写。变量名、函数名、常量名，首字母大写是公开的，小写是私有的。无public private关键字。

- 驼峰命名法

- \+ 连接字符串时，+ 在上一行末尾

- 数据转换时，不会自动隐式转换，必须显示转换

- 方法的{ }，{ 必须写在上一行，不能换行

  func main() {

  }

- 语句末尾，不需要分号

- 定义了的变量，或者import的包，必须使用，不然报错

- 不能多条语句放一行

- 数组也是值传递

- Go不支持重载，和js一样

# javascript、java异同点

1. 和javascript相同点

   - 函数可以返回
   - 可以拿一个全局变量接收有返回值的函数并调用，先于main，init执行

   - 闭包

   - ...可变传参
   - 不支持重载
   - 匿名函数

   不同点

   - 无变量提升，go必须先定义

2. 与java的相同点

   不同点

   - 无try..catch..finally，使用defer，panic，recover，抛出panic异常，defer中使用recover捕获处理

# 字符串常用api

1. 获取字符串长度，按字节len(str)

2. 字符串遍历，同时处理中文问题，r := []rune(str)

3. 字符串转整数，num, err :=  strconv.Atoi("12")

4. 整数转字符串，str = strconv.Itoa(12345)

5. 字符串转[]byte，var bytes = []byte("hello go")

6. []byte 转字符串，str = string([]byte{91, 92, 93})

7. 10进制转2，8，16进制，str = strconv.FormatInt(123, 2) //123转二进制

8. 查询子串是否在指定的支付串中，strings.Contains("seafoot", "foot")  //true

9. 统计一个字符串有几个指定的子串，strings.Count("chinese", "e") //2

10.  不区分大小写的字符串比较（==区分大小写），strings.EqualFold("abc", "ABC") //true

11. 返回子串在字符串中第一次出现的index值，如果没有则返回-1，strings.Index("JAGD_abc", "abc") //5

12. 返回子串在字符串中最后一次出现的index，如果没有返回-1，strings.LastIndex("go golang", "go") //3

13. 将指定的子串替换成另外一个字串，strings.Replace("go go hello", "go", "go语言", n) n指希望替换个，-1为全部替换

14. 按照指定字符分割字符串，分割为字符串数组，strings.Split("hello,work,go", ",")

15. 大小写转换，strings.ToLower("GO")//go

    strings.ToUpper("go")//GO

16. 将字符串左右两边空格替换掉，strings.TrimSpace(" hello go  ")

17. 将左右两边的字符去掉，str := strings.Trim("! hello! go!  "," !")  //hello! go，去掉了"!"和" "，还有TrimLeft，TrimRight方法

18. 判断字符串是否以指定字符串开头，strings.HasPrefix("http//aaa.com", "http//")

19. 判断字符串是否已指定字符串结束，strings.HasSuffix("a.jpg", "jpg")



231页