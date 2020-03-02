

## 一、变量及常量

### 1.变量声明、初始化及赋值

**① 未初始化的标准格式**
> var 变量名 变量类型

**② 未初始化的批量格式**
```
var (
    a int
    b string
    c []float32
    d func() bool
    e struct {
        x int
        y string
    }
)
```
**③ 未初始化变量量的默认值**
+ 整形和浮点型变量默认值：0
+ 字符串默认值为空字符串
+ 布尔型默认值为false
+ 函数、**指针变量量初始值为nil**

**④ 初始化变量格式**
```
// 初始化变量的标准格式
var 变量名 类型 = 表达式

// 初始化变量的编译器自动推断类型格式
var 变量名 = 表达式

// 短变量声明格式（变量名 := 表达式）
num := 10
```

### 2.数据类型
+ 基本数据类型（原⽣生数据类型）：整型、浮点型、布尔型、字符串串、字节
byte和字符rune
+ 复合数据类型（派生数据类型）：指针（pointer）、数组（array）、切
片（slice）、映射（map）、函数（function）、结构体（struct）、通道
（channel）

### 3.打印格式化
**① 通用**
+ %v  值的默认格式表示 value
+ %+v  类似%v，但输出结构体时会添加字段名
+ %#v  值的Go语法表示
+ %T  值的类型的Go语法表示 type

**② 布尔值**
+ 单词true或false

**③ 整数**
+ %b  表示为二进制 binary
+ %c  该值对应的unicode码值 char
+ %d  表示为十进制 digital
+ %8d  表示该整型⻓长度是8，不足8则在数值前补空格。如果超出8，则以
实际为准。
+ %08d 数字⻓度是8，不足8位的，在数字前补0。如果超出8，则以实际
为准。
+ %o  表示为⼋八进制 octal
+ %q  该值对应的单引号括起来的go语法字符字⾯面值，必要时会采用安
全的转义表示 quotation
+ %x  表示为⼗六进制，使用a-f hex
+ %X  表示为⼗六进制，使用A-F
+ %U  表示为Unicode格式：U+1234，等价于"U+%04X" unicode

**④ 浮点数与复数的两个组分**
+ %b  无小数部分、二进制指数的科学计数法，如-123456p-78；参⻅
strconv.FormatFloat
+ %e  （=%.6e）有6位⼩小数部分的科学计数法，如-1234.456e+78
+ %E  科学计数法，如-1234.456E+78
+ %f  （=%.6f）有6位⼩小数部分，如123.456123 float
+ %F  等价于%f
+ %g  根据实际情况采⽤%e或%f格式（以获得更简洁、准确的输出）
+ %G  根据实际情况采用%E或%F格式（以获得更简洁、准确的输出）

**⑤ 字符串串和[]byte**
+ %s  直接输出字符串或者[]byte string
+ %q  该值对应的双引号括起来的go语法字符串字面值，必要时会采⽤
安全的转义表示
+ %x  每个字节用两字符⼗六进制数表示（使用a-f）
+ %X  每个字节用两字符⼗六进制数表示（使用A-F）

### 4.数据类型转换
> T(表达式)

### 5.常量与枚举

**① 常量的定义格式**
```
// 显式类型定义
const B string = "Steven"
// 隐式类型定义
const C = "Steven"
// 多个相同类型的声明可以简写
const WIDTH , HEIGHT = value1, value2
```
**② 枚举**
```
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```
> 常量组中如果不指定类型和初始值，则与上⼀行⾮空常量的值相同
```
// 打印a、b、c，输出：10 10 10
const (
    a = 10
    b
    c
)
```
### 6.类型别名
```
// 类型别名
type 类型别名 = 类型

// 定义类型的语法格式
type 新的类型名 类型
```

## 二、运算符
> 与java一样

## 三、流程控制（条件、循环语句）
> 不用加括号，其余与java大致相同
```
package main
import "fmt"

func main() {
    num := 20
    if num%2 == 0 {
        fmt.Println(num, "偶数")
    } else {
        fmt.Println(num, "奇数")
    }
}
```
**for 循环的 range 格式**
```
// 遍历字符串，获得字符
func traverseString() {
    str := "123ABCabc一丁"
    for i, value := range str {
    fmt.Printf("第 %d 位的ASCII值=%d ，字符是%c \n", i, value ,value)
    }
}
```

**goto语句**
> Go 语⾔的 goto 语句可以⽆条件地转移到程序指定的⾏
```
// 语法格式
LABEL: statement
goto LABEL
```