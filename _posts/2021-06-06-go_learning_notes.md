---
layout: post
title:  "go学习笔记"
categories: go
tags: go
author: ZhangJie
---

* content
{:toc}
go学习笔记。




## go模块代理

```
用法
Go 1.13 及以上（推荐）

打开你的终端并执行

$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
完成。

macOS 或 Linux

打开你的终端并执行

$ export GO111MODULE=on
$ export GOPROXY=https://goproxy.cn
或者

$ echo "export GO111MODULE=on" >> ~/.profile
$ echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
$ source ~/.profile
完成。

Windows

打开你的 PowerShell 并执行

C:\> $env:GO111MODULE = "on"
C:\> $env:GOPROXY = "https://goproxy.cn"
或者

1. 打开“开始”并搜索“env”
2. 选择“编辑系统环境变量”
3. 点击“环境变量…”按钮
4. 在“<你的用户名> 的用户变量”章节下（上半部分）
5. 点击“新建…”按钮
6. 选择“变量名”输入框并输入“GO111MODULE”
7. 选择“变量值”输入框并输入“on”
8. 点击“确定”按钮
9. 点击“新建…”按钮
10. 选择“变量名”输入框并输入“GOPROXY”
11. 选择“变量值”输入框并输入“https://goproxy.cn”
12. 点击“确定”按钮
完成。
```

## 开发工具

- gomodules

go mod 是Golang 1.11 版本引入的官方包（package）依赖管理工具，用于解决之前没有地方记录依赖包具体版本的问题，方便依赖包的管理。

go mod download: 下载依赖包到本地
go mod edit: 编辑go.mod文件
go mod graph: 打印模块依赖图
go mod init: 初始化当前文件夹，创建go.mod文件
go mod tidy: 增加缺少的包，删除无用的包
go mod vendor: 将依赖复制到vendor目录下
go mod verify: 校验依赖
go mod why: 解释为什么需要依赖


- goimports

goimports工具是Go官方提供的一种工具，它能够为我们自动格式化 Go 语言代码并对所有引入的包进行管理，包括自动增删依赖的包引用、将依赖包按字母序排序并分类。

```
go install golang.org/x/tools/cmd/goimports@latest
```


- go doc

```
> go doc fmt.Println
package fmt // import "fmt"

func Println(a ...any) (n int, err error)
    Println formats using the default formats for its operands and writes to
    standard output. Spaces are always added between operands and a newline
    is appended. It returns the number of bytes written and any write error
    encountered.
```


- go install

go install命令是用来编译并安装代码包或者源文件的。实际上，go install命令只比go build命令多做了一件事，即：安装编译后的结果文件到指定目录。

- go fmt

go fmt命令主要是用来格式化代码。

- go test

go test命令会自动读取源码目录下名为"*_test.go"的文件，生成并运行测试用的可执行文件。




## VS Code go插件

在 VS Code 中，使用快捷键：ctrl+shift+P，然后键入：go:install/update tools，将所有插件都勾选上，然后点击 OK 即开始安装。


## go语言基础知识

### package

`package main`表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 `main` 的包。

如果包名不是以 `.` 或 `/` 开头，如 `"fmt"` 或者 `"container/list"`，则 Go 会在全局文件进行查找；如果包名以 `./` 开头，则 Go 会在相对目录中查找；如果包名以 `/` 开头（在 Windows 下也可以这样使用），则会在系统的绝对路径中查找。



### 变量

当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float 为 0.0，bool 为 false，string 为空字符串，指针为 nil。记住，所有的内存在 Go 中都是经过初始化的。


同一类型的多个变量可以声明在同一行，如：

```go
var a, b, c int
```

(这是将类型写在标识符后面的一个重要原因)

多变量可以在同一行进行赋值，如：

```go
a, b, c = 5, 7, "abc"
```

上面这行假设了变量 a，b 和 c 都已经被声明，否则的话应该这样使用：

```go
a, b, c := 5, 7, "abc"
```

如果你想要交换两个变量的值，则可以简单地使用 `a, b = b, a`。

请记住“:=”是一个变量声明语句，而“=”是一个变量赋值操作。

匿名变量的特点是一个下划线"_"，任何赋给这个标识符的值都将被抛弃，匿名变量不占用内存空间，不会分配内存。匿名变量与匿名变量之间也不会因为多次声明而无法使用。


在下面的代码中，第一个语句声明了in和err两个变量。在第二个语句只声明了out一个变量，然后对已经声明的err进行了赋值操作。
```go
in, err := os.Open(infile)
// ...
out, err := os.Create(outfile)
```
简短变量声明语句中必须至少要声明一个新的变量，下面的代码将不能编译通过：
```go
f, err := os.Open(infile)
// ...
f, err := os.Create(outfile) // compile error: no new variables
```
解决的方法是第二个简短变量声明语句改用普通的多重赋值语句。

#### 作用域

要特别注意短变量声明语句的作用域范围，考虑下面的程序，它的目的是获取当前的工作目录然后保存到一个包级的变量中。这本来可以通过直接调用os.Getwd完成，但是将这个从主逻辑中分离出来可能会更好，特别是在需要处理错误的时候。函数log.Fatalf用于打印日志信息，然后调用os.Exit(1)终止程序。
```go
var cwd string

func init() {
    cwd, err := os.Getwd() // compile error: unused: cwd
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
}
```
虽然cwd在外部已经声明过，但是:=语句还是将cwd和err重新声明为新的局部变量。因为内部声明的cwd将屏蔽外部的声明，因此上面的代码并不会正确更新包级声明的cwd变量。

由于当前的编译器会检测到局部声明的cwd并没有使用，然后报告这可能是一个错误，但是这种检测并不可靠。因为一些小的代码变更，例如增加一个局部cwd的打印语句，就可能导致这种检测失效。
```go
var cwd string

func init() {
    cwd, err := os.Getwd() // NOTE: wrong!
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
    log.Printf("Working directory = %s", cwd)
}
```
全局的cwd变量依然是没有被正确初始化的，而且看似正常的日志输出更是让这个BUG更加隐晦。

有许多方式可以避免出现类似潜在的问题。最直接的方法是通过单独声明err变量，来避免使用:=的简短声明方式：
```go
var cwd string

func init() {
    var err error
    cwd, err = os.Getwd()
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
}
```


### 类型

Go 也有基于架构的类型，例如：int、uint 和 uintptr。

这些类型的长度都是根据运行程序所在的操作系统类型所决定的：

- `int` 和 `uint` 在 32 位操作系统上，它们均使用 32 位（4 个字节），在 64 位操作系统上，它们均使用 64 位（8 个字节）。
- `uintptr` 的长度被设定为足够存放一个指针即可。


与操作系统架构无关的类型都有固定的大小，并在类型的名称中就可以看出来：

整数：

- int8（-128 -> 127）
- int16（-32768 -> 32767）
- int32（-2,147,483,648 -> 2,147,483,647）
- int64（-9,223,372,036,854,775,808 -> 9,223,372,036,854,775,807）

无符号整数：

- uint8（0 -> 255）
- uint16（0 -> 65,535）
- uint32（0 -> 4,294,967,295）
- uint64（0 -> 18,446,744,073,709,551,615）

浮点型（IEEE-754 标准）：

- float32（+- 1e-45 -> +- 3.4 * 1e38）
- float64（+- 5 * 1e-324 -> 107 * 1e308）

你应该尽可能地使用 float64，因为 `math` 包中所有有关数学运算的函数都会要求接收这个类型。

Go 中不允许不同类型之间的混合使用。



### 预定义常量iota

iota比较特殊，可以被认为是一个可被编译器修改的常量，在每一个const关键字出现时被重置为0，然后在下一个const出现之前，每出现一次iota，其所代表的数字会自动增1。

```go
const (         // iota被重设为0
    c0 = iota   // c0 == 0
    c1 = iota   // c1 == 1
    c2 = iota   // c2 == 2
)

// 如果两个const的赋值语句的表达式是一样的，那么可以省略后一个赋值表达式。因此，上面的前两个const语句可简写为：
const (         // iota被重设为0
    c0 = iota   // c0 == 0
    c1          // c1 == 1
    c2          // c2 == 2
)
```

使用位左移与 iota 计数配合可优雅地实现存储单位的常量枚举：

```go
type ByteSize float64
const (
	_ = iota // 通过赋值给空白标识符来忽略值
	KB ByteSize = 1<<(10*iota)
	MB
	GB
	TB
	PB
	EB
	ZB
	YB
)
```

### 字符串

字符串是一种值类型，且值不可变，即创建某个文本后你无法再次修改这个文本的内容；更深入地讲，字符串是字节的定长数组。

在以字符串作为参数传递给fmt.Println函数时，字符串的内容并没有被复制——传递的仅仅是字符串的地址和长度（字符串的结构在reflect.StringHeader中定义）。

```go
type StringHeader struct {
    Data uintptr
    Len  int
}
```

字符串结构由两个信息组成：第一个是字符串指向的底层字节数组，第二个是字符串的字节的长度。字符串其实是一个结构体，因此字符串的赋值操作也就是reflect.StringHeader结构体的复制过程，并不会涉及底层字节数组的复制。


和 C/C++不一样，Go 中的字符串是根据长度限定，而非特殊字符`\0`。

可以通过函数 `len()` 来获取字符串所占的字节长度，例如：`len(str)`。


**注意事项** 获取字符串中某个字节的地址的行为是非法的，例如：`&str[i]`。


在循环中使用加号 `+` 拼接字符串并不是最高效的做法，更好的办法是使用函数 `strings.Join()`，有没有更好的办法了？有！使用字节缓冲（`bytes.Buffer`）拼接更加给力！


#### strings 和 strconv 包

- 前缀和后缀

`HasPrefix` 判断字符串 `s` 是否以 `prefix` 开头：

```go
strings.HasPrefix(s, prefix string) bool
```

`HasSuffix` 判断字符串 `s` 是否以 `suffix` 结尾：

```go
strings.HasSuffix(s, suffix string) bool
```


- 字符串包含关系

`Contains` 判断字符串 `s` 是否包含 `substr`：

```go
strings.Contains(s, substr string) bool
```

- 判断子字符串或字符在父字符串中出现的位置（索引）

`Index` 返回字符串 `str` 在字符串 `s` 中的索引（`str` 的第一个字符的索引），-1 表示字符串 `s` 不包含字符串 `str`：

```go
strings.Index(s, str string) int
```

`LastIndex` 返回字符串 `str` 在字符串 `s` 中最后出现位置的索引（`str` 的第一个字符的索引），-1 表示字符串 `s` 不包含字符串 `str`：

```go
strings.LastIndex(s, str string) int
```

如果需要查询非 ASCII 编码的字符在父字符串中的位置，建议使用以下函数来对字符进行定位：

```go
strings.IndexRune(s string, r rune) int
```


- 字符串替换

`Replace` 用于将字符串 `str` 中的前 `n` 个字符串 `old` 替换为字符串 `new`，并返回一个新的字符串，如果 `n = -1` 则替换所有字符串 `old` 为字符串 `new`：

```go
strings.Replace(str, old, new, n) string
```

- 统计字符串出现次数

`Count` 用于计算字符串 `str` 在字符串 `s` 中出现的非重叠次数：

```go
strings.Count(s, str string) int
```

- 修改字符串大小写

`ToLower` 将字符串中的 Unicode 字符全部转换为相应的小写字符：

```go
strings.ToLower(s) string
```

`ToUpper` 将字符串中的 Unicode 字符全部转换为相应的大写字符：

```go
strings.ToUpper(s) string
```


- 修剪字符串

你可以使用 `strings.TrimSpace(s)` 来剔除字符串开头和结尾的空白符号；如果你想要剔除指定字符，则可以使用 `strings.Trim(s, "cut")` 来将开头和结尾的 `cut` 去除掉。该函数的第二个参数可以包含任何字符，如果你只想剔除开头或者结尾的字符串，则可以使用 `TrimLeft` 或者 `TrimRight` 来实现。

- 分割字符串

`strings.Fields(s)` 将会利用 1 个或多个空白符号来作为动态长度的分隔符将字符串分割成若干小块，并返回一个 slice，如果字符串只包含空白符号，则返回一个长度为 0 的 slice。

`strings.Split(s, sep)` 用于自定义分割符号来对指定字符串进行分割，同样返回 slice。


- 拼接 slice 到字符串

`Join` 用于将元素类型为 string 的 slice 使用分割符号来拼接组成一个字符串：

```go
strings.Join(sl []string, sep string) string
```

针对从字符串类型转换为数字类型，Go 提供了以下函数：

- `strconv.Atoi(s string) (i int, err error)` 将字符串转换为 int 型。
- `strconv.ParseFloat(s string, bitSize int) (f float64, err error)` 将字符串转换为 float64 型。

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	var orig string = "666"
	var an int
	var newS string

	fmt.Printf("The size of ints is: %d\n", strconv.IntSize)	  

	an, _ = strconv.Atoi(orig)
	fmt.Printf("The integer is: %d\n", an) 
	an = an + 5
	newS = strconv.Itoa(an)
	fmt.Printf("The new string is: %s\n", newS)
}
```


基本数据类型与字符串之间的转换，优先使用 strconv 而不是 fmt，因为前者性能更佳。

```go
// Bad
for i := 0; i < b.N; i++ {
 s := fmt.Sprint(rand.Int())
}

BenchmarkFmtSprint-4    143 ns/op    2 allocs/op

// Good
for i := 0; i < b.N; i++ {
 s := strconv.Itoa(rand.Int())
}

BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

为什么性能上会有两倍多的差距，因为 fmt实现上利用反射来达到范型的效果，在运行时进行类型的动态判断，所以带来了一定的性能损耗。


### 指针

在Go语言中，返回函数中局部变量的地址也是安全的。

局部变量可能在函数返回之后依然存在。

编译器会自动选择在栈上还是在堆上分配局部变量的存储空间，但可能令人惊讶的是，这个选择并不是由用var还是new声明变量的方式决定的。

```go
var global *int

func f() {
    var x int
    x = 1
    global = &x
}

func g() {
    y := new(int)
    *y = 1
}
```
f函数里的x变量必须在堆上分配，因为它在函数退出后依然可以通过包一级的global变量找到，虽然它是在函数内部定义的；用Go语言的术语说，这个x局部变量从函数f中逃逸了。相反，当g函数返回时，变量*y将是不可达的，也就是说可以马上被回收的。因此，*y并没有从函数g中逃逸，编译器可以选择在栈上分配*y的存储空间（译注：也可以选择在堆上分配，然后由Go语言的GC回收这个变量的内存空间），虽然这里用的是new方式。其实在任何时候，你并不需为了编写正确的代码而要考虑变量的逃逸行为，要记住的是，逃逸的变量需要额外分配内存，同时对性能的优化可能会产生细微的影响。


对于有C/C++编程经验的程序员需要强调的是：不用关心Go语言中函数栈和堆的问题，编译器和运行时会帮我们搞定；同样不要假设变量在内存中的位置是固定不变的，指针随时可能会变化，特别是在你不期望它变化的时候。


### 数组与切片

Go语言的数组是一种值类型，虽然数组的元素可以被修改，但是数组本身的赋值和函数传参都是以整体复制的方式处理的。

#### 将数组传递给函数

把一个大数组传递给函数会消耗很多内存。有两种方法可以避免这种现象：

- 传递数组的指针(不常用)；
- 使用数组的切片。


#### 切片

切片的底层数据虽然也是对应数据类型的数组，但是每个切片还有独立的长度和容量信息，切片赋值和函数传参数时也是将切片头信息部分按传值方式处理。因为切片头含有底层数据的指针，所以它的赋值也不会导致底层数据的复制。

```go
type SliceHeader struct {
    Data uintptr
    Len  int
    Cap  int
}
```

除了数据之外，切片结构还包含了切片长度和切片容量信息，这2个信息也是传值的。如果被调用函数中修改了Len或Cap信息的话，就无法反映到调用参数的切片中，这时候我们一般会通过返回修改后的切片来更新之前的切片。这也是为何内置的append必须要返回一个切片的原因。

切片可以和nil进行比较，只有当切片底层数据指针为空时切片本身为nil，这时候切片的长度和容量信息将是无效的。如果有切片的底层数据指针为空，但是长度和容量不为0的情况，那么说明切片本身已经被损坏了（比如直接通过reflect.SliceHeader或unsafe包对切片作了不正确的修改）。

在判断一个切片是否为空时，一般通过len获取切片的长度来判断，一般很少将切片和nil值做直接的比较。

切片（slice）是对数组一个连续片段的引用（该数组我们称之为相关数组，通常是匿名的），所以切片是一个引用类型（因此更类似于 C/C++ 中的数组类型，或者 Python 中的 list 类型）。


**优点** 因为切片是引用，所以它们不需要使用额外的内存并且比使用数组更有效率，所以在 Go 代码中 切片比数组更常用。

切片的初始化格式是：`var slice1 []type = arr1[start:end]`。
这表示 slice1 是由数组 arr1 从 start 索引到 `end-1` 索引之间的元素构成的子集（切分数组，start:end 被称为 slice 表达式）。

对于类型，和数组的最大不同是，切片的类型和长度信息无关，只要是相同类型元素构成的切片均对应相同的切片类型。




##### 切片定义方式

```go
var (
    a []int               // nil切片, 和 nil 相等, 一般用来表示一个不存在的切片
    b = []int{}           // 空切片, 和 nil 不相等, 一般用来表示一个空的集合
    c = []int{1, 2, 3}    // 有3个元素的切片, len和cap都为3
    d = c[:2]             // 有2个元素的切片, len为2, cap为3
    e = c[0:2:cap(c)]     // 有2个元素的切片, len为2, cap为3
    f = c[:0]             // 有0个元素的切片, len为0, cap为3
    g = make([]int, 3)    // 有3个元素的切片, len和cap都为3
    h = make([]int, 2, 3) // 有2个元素的切片, len为2, cap为3
    i = make([]int, 0, 3) // 有0个元素的切片, len为0, cap为3
)
```


##### 将切片传递给函数

如果你有一个函数需要对数组做操作，你可能总是需要把参数声明为切片。当你调用该函数时，把数组分片，创建为一个 切片引用并传递给该函数。这里有一个计算数组元素和的方法:

```go
func sum(a []int) int {
	s := 0
	for i := 0; i < len(a); i++ {
		s += a[i]
	}
	return s
}

func main() {
	var arr = [5]int{0, 1, 2, 3, 4}
	sum(arr[:])
}
```

##### 用 make() 创建一个切片

当相关数组还没有定义时，我们可以使用 make() 函数来创建一个切片 同时创建好相关数组：`var slice1 []type = make([]type, len)`。

也可以简写为 `slice1 := make([]type, len)`，这里 `len` 是数组的长度并且也是 `slice` 的初始长度。


- 指定切片容量

在尽可能的情况下，在使用 make() 初始化切片时提供容量信息，特别是在追加切片时。

与 map 不同，slice capacity 不是一个提示：编译器将为提供给 make() 的 slice 的容量分配足够的内存，这意味着后续的 append() 操作将导致零分配（直到 slice 的长度与容量匹配，在此之后，任何 append 都可能调整大小以容纳其他元素）。

```go
const size = 1000000

// Bad
for n := 0; n < b.N; n++ {
 data := make([]int, 0)
   for k := 0; k < size; k++ {
     data = append(data, k)
  }
}

BenchmarkBad-4    219    5202179 ns/op

// Good
for n := 0; n < b.N; n++ {
 data := make([]int, 0, size)
   for k := 0; k < size; k++ {
     data = append(data, k)
  }
}

BenchmarkGood-4   706    1528934 ns/op
```


##### new() 和 make() 的区别

看起来二者没有什么区别，都在堆上分配内存，但是它们的行为不同，适用于不同的类型。

- new(T) 为每个新的类型T分配一片内存，初始化为 0 并且返回类型为\*T的内存地址：这种方法 **返回一个指向类型为 T，值为 0 的地址的指针**，它适用于值类型如数组和结构体；它相当于 `&T{}`。

- make(T) **返回一个类型为 T 的初始值**，它只适用于3种内建的引用类型：切片、map 和 channel。

换言之，new 函数分配内存，make 函数初始化；下图给出了区别：


```go
var p *[]int = new([]int) // *p == nil; with len and cap 0
p := new([]int)
```

`p := make([]int, 0)` ，切片 已经被初始化，但是指向一个空的数组。

以上两种方式实用性都不高。下面的方法：

```go
var v []int = make([]int, 10, 50)
```

或者
	
```go
v := make([]int, 10, 50)
```

这样分配一个有 50 个 int 值的数组，并且创建了一个长度为 10，容量为 50 的 切片 v，该 切片 指向数组的前 10 个元素。


##### 切片的复制与追加

如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。下面的代码描述了从拷贝切片的 copy 函数和向切片追加新元素的 append 函数。

```go
package main
import "fmt"

func main() {
	slFrom := []int{1, 2, 3}
	slTo := make([]int, 10)

	n := copy(slTo, slFrom)
	fmt.Println(slTo)
	fmt.Printf("Copied %d elements\n", n) // n == 3

	sl3 := []int{1, 2, 3}
	sl3 = append(sl3, 4, 5, 6)
	fmt.Println(sl3)
}
```

**注意**： append 在大多数情况下很好用，但是如果你想完全掌控整个追加过程，你可以实现一个这样的 AppendByte 方法：

```go
func AppendByte(slice []byte, data ...byte) []byte {
	m := len(slice)
	n := m + len(data)
	if n > cap(slice) { // if necessary, reallocate
		// allocate double what's needed, for future growth.
		newSlice := make([]byte, (n+1)*2)
		copy(newSlice, slice)
		slice = newSlice
	}
	slice = slice[0:n]
	copy(slice[m:n], data)
	return slice
}
```


在容量不足的情况下，append的操作会导致重新分配内存，可能导致巨大的内存分配和复制数据代价。即使容量足够，依然需要用append函数的返回值来更新切片本身，因为新切片的长度已经发生了变化。


在开头一般都会导致内存的重新分配，而且会导致已有的元素全部复制1次。因此，从切片的开头添加元素的性能一般要比从尾部追加元素的性能差很多。



可以用copy和append组合可以避免创建中间的临时切片，同样是完成添加元素的操作：
```go
a = append(a, 0)     // 切片扩展1个空间
copy(a[i+1:], a[i:]) // a[i:]向后移动1个位置
a[i] = x             // 设置新添加的元素
```

用copy和append组合也可以实现在中间位置插入多个元素(也就是插入一个切片):
```go
a = append(a, x...)       // 为x切片扩展足够的空间
copy(a[i+len(x):], a[i:]) // a[i:]向后移动len(x)个位置
copy(a[i:], x)            // 复制新添加的切片
```
稍显不足的是，在第一句扩展切片容量的时候，扩展空间部分的元素复制是没有必要的。没有专门的内置函数用于扩展切片的容量，append本质是用于追加元素而不是扩展容量，扩展切片容量只是append的一个副作用。


##### 删除切片元素

根据要删除元素的位置有三种情况：从开头位置删除，从中间位置删除，从尾部删除。其中删除切片尾部的元素最快：
```go
a = []int{1, 2, 3}
a = a[:len(a)-1]   // 删除尾部1个元素
a = a[:len(a)-N]   // 删除尾部N个元素
```
删除开头的元素可以直接移动数据指针：
```go
a = []int{1, 2, 3}
a = a[1:] // 删除开头1个元素
a = a[N:] // 删除开头N个元素
```

删除开头的元素也可以不移动数据指针，但是将后面的数据向开头移动。可以用append原地完成（所谓原地完成是指在原有的切片数据对应的内存区间内完成，不会导致内存空间结构的变化）：
```go
a = []int{1, 2, 3}
a = append(a[:0], a[1:]...) // 删除开头1个元素
a = append(a[:0], a[N:]...) // 删除开头N个元素
```

也可以用copy完成删除开头的元素
```go
a = []int{1, 2, 3}
a = a[:copy(a, a[1:])] // 删除开头1个元素
a = a[:copy(a, a[N:])] // 删除开头N个元素
```

对于删除中间的元素，需要对剩余的元素进行一次整体挪动，同样可以用append或copy原地完成：
```go
a = []int{1, 2, 3, ...}

a = append(a[:i], a[i+1:]...) // 删除中间1个元素
a = append(a[:i], a[i+N:]...) // 删除中间N个元素

a = a[:i+copy(a[i:], a[i+1:])]  // 删除中间1个元素
a = a[:i+copy(a[i:], a[i+N:])]  // 删除中间N个元素
```

##### 避免重复的字符串到字节切片的转换

不要反复从固定字符串创建字节 slice，因为重复的切片初始化会带来性能损耗。相反，请执行一次转换并捕获结果。

```go
// Bad
for i := 0; i < b.N; i++ {
 w.Write([]byte("Hello world"))
}

BenchmarkBad-4   50000000   22.2 ns/op

// Good
data := []byte("Hello world")
for i := 0; i < b.N; i++ {
 w.Write(data)
}

BenchmarkGood-4  500000000   3.25 ns/op
```

##### 遍历数组

用for range方式迭代的性能可能会更好一些，因为这种迭代可以保证不会出现数组越界的情形，每轮迭代对数组元素的访问时可以省去对下标越界的判断。


可以用fmt.Printf函数提供的%T或%#v谓词语法来打印数组的类型和详细信息：
```go
fmt.Printf("b: %T\n", b)  // b: [3]int
fmt.Printf("b: %#v\n", b) // b: [3]int{1, 2, 3}
```


##### 空数组

 
空数组, 长度为0的数组在内存中并不占用空间。空数组虽然很少直接使用，但是可以用于强调某种特有类型的操作时避免分配额外的内存空间，比如用于管道的同步操作：

```go
c1 := make(chan [0]int)
go func() {
    fmt.Println("c1")
    c1 <- [0]int{}
}()
<-c1
```

当然一般更倾向于用无类型的匿名结构体代替：

```go
c2 := make(chan struct{})
go func() {
    fmt.Println("c2")
    c2 <- struct{}{} // struct{}部分是类型, {}表示对应的结构体值
}()
<-c2
```


##### 避免切片内存泄漏

如前面所说，切片操作并不会复制底层的数据。底层的数组会被保存在内存中，直到它不再被引用。但是有时候可能会因为一个小的内存引用而导致底层整个数组处于被使用的状态，这会延迟自动内存回收器对底层数组的回收。

例如，FindPhoneNumber函数加载整个文件到内存，然后搜索第一个出现的电话号码，最后结果以切片方式返回。
```go
func FindPhoneNumber(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    return regexp.MustCompile("[0-9]+").Find(b)
}
```
这段代码返回的[]byte指向保存整个文件的数组。因为切片引用了整个原始数组，导致自动垃圾回收器不能及时释放底层数组的空间。一个小的需求可能导致需要长时间保存整个文件数据。这虽然这并不是传统意义上的内存泄漏，但是可能会拖慢系统的整体性能。

要修复这个问题，可以将感兴趣的数据复制到一个新的切片中（数据的传值是Go语言编程的一个哲学，虽然传值有一定的代价，但是换取的好处是切断了对原始数据的依赖）：
```go
func FindPhoneNumber(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    b = regexp.MustCompile("[0-9]+").Find(b)
    return append([]byte{}, b...)
}
```

类似的问题，在删除切片元素时可能会遇到。假设切片里存放的是指针对象，那么下面删除末尾的元素后，被删除的元素依然被切片底层数组引用，从而导致不能及时被自动垃圾回收器回收（这要依赖回收器的实现方式）：
```go
var a []*int{ ... }
a = a[:len(a)-1]    // 被删除的最后一个元素依然被引用, 可能导致GC操作被阻碍
```

保险的方式是先将需要自动内存回收的元素设置为nil，保证自动回收器可以发现需要回收的对象，然后再进行切片的删除操作：
```go
var a []*int{ ... }
a[len(a)-1] = nil // GC回收最后一个元素内存
a = a[:len(a)-1]  // 从切片删除最后一个元素
```

当然，如果切片存在的周期很短的话，可以不用刻意处理这个问题。因为如果切片本身已经可以被GC回收的话，切片对应的每个元素自然也就是可以被回收的了。


### Map

map 是引用类型，可以使用如下声明：

```go
var map1 map[keytype]valuetype
var map1 map[string]int
```

map 传递给函数的代价很小：在 32 位机器上占 4 个字节，64 位机器上占 8 个字节，无论实际上存储了多少数据。通过 key 在 map 中寻找值是很快的，比线性查找快得多，但是仍然比从数组和切片的索引中直接读取要慢 100 倍；所以如果你很在乎性能的话还是建议用切片来解决问题。



map中的元素并不是一个变量，而是一个值。因此，我们不能对map的元素进行取址操作。

```go
var mp map[int]int
mp = make(map[int]int)
mp[1] = 123
p := &mp[1]  // cannot take the address of mp[1]
```


#### 测试键值对是否存在及删除元素

测试 map1 中是否存在 key1：

`val1, isPresent = map1[key1]`

isPresent 返回一个 bool 值：如果 key1 存在于 map1，val1 就是 key1 对应的 value 值，并且 isPresent为true；如果 key1 不存在，val1 就是一个空值，并且 isPresent 会返回 false。

如果你只是想判断某个 key 是否存在而不关心它对应的值到底是多少，你可以这么做：

```go
_, ok := map1[key1] // 如果key1存在则ok == true，否则ok为false
```

从 map1 中删除 key1：直接 `delete(map1, key1)` 就可以。


#### map 类型的切片

假设我们想获取一个 map 类型的切片，我们必须使用两次 `make()` 函数，第一次分配切片，第二次分配 切片中每个 map 元素。

```go
package main
import "fmt"

func main() {
	// Version A:
	items := make([]map[int]int, 5)
	for i:= range items {
		items[i] = make(map[int]int, 1)
		items[i][1] = 2
	}
	fmt.Printf("Version A: Value of items: %v\n", items)

	// Version B: NOT GOOD!
	items2 := make([]map[int]int, 5)
	for _, item := range items2 {
		item = make(map[int]int, 1) // item is only a copy of the slice element.
		item[1] = 2 // This 'item' will be lost on the next iteration.
	}
	fmt.Printf("Version B: Value of items: %v\n", items2)
}
```

输出结果：

	Version A: Value of items: [map[1:2] map[1:2] map[1:2] map[1:2] map[1:2]]
	Version B: Value of items: [map[] map[] map[] map[] map[]]

需要注意的是，应当像 A 版本那样通过索引使用切片的 map 元素。在 B 版本中获得的项只是 map 值的一个拷贝而已，所以真正的 map 元素没有得到初始化。



#### 指定 map 容量提示

在尽可能的情况下，在使用 make() 初始化的时候提供容量信息。
向 make() 提供容量提示会在初始化时尝试调整 map 的大小，这将减少在将元素添加到 map 时为 map 重新分配内存。

注意，与 slice 不同。map capacity 提示并不保证完全的抢占式分配，而是用于估计所需的 hashmap bucket 的数量。 因此，在将元素添加到 map 时，甚至在指定 map 容量时，仍可能发生分配。

```go
// Bad
m := make(map[string]os.FileInfo)

files, _ := ioutil.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
// m 是在没有大小提示的情况下创建的； 在运行时可能会有更多分配。

// Good
files, _ := ioutil.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
// m 是有大小提示创建的；在运行时可能会有更少的分配。
```



### switch 结构

一旦成功地匹配到某个分支，在执行完相应代码后就会退出整个 switch 代码块，也就是说您不需要特别使用 `break` 语句来表示结束。

因此，程序也不会自动地去执行下一个分支的代码。如果在执行完每个分支的代码后，还希望继续执行后续分支的代码，可以使用 `fallthrough` 关键字来达到目的。

```go
switch i {
	case 0: // 空分支，只有当 i == 0 时才会进入分支
	case 1:
		f() // 当 i == 0 时函数不会被调用
}
```

```go
switch i {
	case 0: fallthrough
	case 1:
		f() // 当 i == 0 时函数也会被调用
}
```



### runtime

通过`runtime`包在运行时获取所在的操作系统类型，以及如何通过 `os` 包中的函数 `os.Getenv()` 来获取环境变量中的值，并保存到 string 类型的局部变量 path 中。

```go
package main

import (
	"fmt"
   "runtime"
	"os"
)

func main() {
	var goos string = runtime.GOOS
	fmt.Printf("The operating system is: %s\n", goos)
	path := os.Getenv("PATH")
	fmt.Printf("Path is %s\n", path)
}
```

如果你在 Windows 下运行这段代码，则会输出 `The operating system is: windows` 以及相应的环境变量的值；如果你在 Linux 下运行这段代码，则会输出 `The operating system is: linux` 以及相应的的环境变量的值。


```go
cmd := exec.Command("notepad")
cmd.Run()


cmd := exec.Command("go")
// 获取命令输出
out, _ := cmd.CombinedOutput()
fmt.Printf("%s", out)


cmd := exec.Command("go", "version")
// 获取命令输出
out, _ := cmd.CombinedOutput()
fmt.Printf("%s", out)
```


```go
for i := 0; i < len(os.Args); i++ {
	fmt.Println(os.Args[i])
}
```


### init 函数

变量除了可以在全局声明中初始化，也可以在 init 函数中初始化。这是一类非常特殊的函数，它不能够被人为调用，而是在每个包完成初始化后自动执行，并且执行优先级比 main 函数高。

一个.go文件中可以有多个init()函数。init函数不能被其他函数调用，而是在main函数执行之前自动被调用。

一个可能的用途是在开始执行程序之前对数据进行检验或修复，以保证程序状态的正确性。

```go
package trans

import "math"

var Pi float64

func init() {
   Pi = 4 * math.Atan(1) // init() function computes Pi
}
```

在它的 init 函数中计算变量 Pi 的初始值。


要注意的是，在main.main函数执行之前所有代码都运行在同一个goroutine，也就是程序的主系统线程中。因此，如果某个init函数内部用go关键字启动了新的goroutine的话，新的goroutine只有在进入main.main函数之后才可能被执行到。


### defer

关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 `return` 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 `return` 语句同样可以包含一些操作，而不是单纯地返回某个值）。

关键字 defer 的用法类似于面向对象编程语言 Java 和 C# 的 `finally` 语句块，它一般用于释放某些已分配的资源。

当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）：

```go
func f() {
	for i := 0; i < 5; i++ {
		defer fmt.Printf("%d ", i)
	}
}
```

上面的代码将会输出：`4 3 2 1 0`。


关键字 defer 允许我们进行一些函数执行完成后的收尾工作，例如：

- 关闭文件流

```go
// open a file  
defer file.Close()
```

- 解锁一个加锁的资源

```go
mu.Lock()  
defer mu.Unlock() 
```

- 打印最终报告

```go
printHeader()  
defer printFooter()
```

- 关闭数据库链接

```go
// open a database connection  
defer disconnectFromDB()
```

合理使用 defer 语句能够使得代码更加简洁。


### 函数

- 将函数作为参数

函数可以作为其它函数的参数进行传递，然后在其它函数内调用执行，一般称之为回调。

```go
package main

import (
	"fmt"
)

func main() {
	callback(1, Add)
}

func Add(a, b int) {
	fmt.Printf("The sum of %d and %d is: %d\n", a, b, a+b)
}

func callback(y int, f func(int, int)) {
	f(y, 2) // this becomes Add(1, 2)
}
```

- 可变数量的参数

```go
// more 对应 []int 切片类型
func Sum(a int, more ...int) int {
    for _, v := range more {
        a += v
    }
    return a
}
```

当可变参数是一个空接口类型时，调用者是否解包可变参数会导致不同的结果：
```go
func main() {
    var a = []interface{}{123, "abc"}

    Print(a...) // 123 abc
    Print(a)    // [123 abc]
}

func Print(a ...interface{}) {
    fmt.Println(a...)
}
```

第一个Print调用时传入的参数是a...，等价于直接调用Print(123, "abc")。第二个Print调用传入的是未解包的a，等价于直接调用Print([]interface{}{123, "abc"})。



不仅函数的参数可以有名字，也可以给函数的返回值命名：
```go
func Find(m map[int]int, key int) (value int, ok bool) {
    value, ok = m[key]
    return
}
```

### 闭包

当我们不希望给函数起名字的时候，可以使用匿名函数，例如：`func(x, y int) int { return x + y }`。

这样的一个函数不能够独立存在（编译器会返回错误：`non-declaration statement
outside function body`），但可以被赋值于某个变量，即保存函数的地址到变量中：`fplus := func(x, y int) int { return x + y }`，然后通过变量名对函数进行调用：`fplus(3,4)`。

当然，您也可以直接对匿名函数进行调用：`func(x, y int) int { return x + y } (3, 4)`。

下面是一个计算从 1 到 1 百万整数的总和的匿名函数：

```go
func() {
	sum := 0
	for i := 1; i <= 1e6; i++ {
		sum += i
	}
}()
```

如果返回值命名了，可以通过名字来修改返回值，也可以通过defer语句在return语句之后修改返回值：
```go
func Inc() (v int) {
    defer func(){ v++ } ()
    return 42
}
```
其中defer语句延迟执行了一个匿名函数，因为这个匿名函数捕获了外部函数的局部变量v，这种函数我们一般叫闭包。闭包对捕获的外部变量并不是传值方式访问，而是以引用的方式访问。


闭包的这种引用方式访问外部变量的行为可能会导致一些隐含的问题：

```go
func main() {
    for i := 0; i < 3; i++ {
        defer func(){ println(i) } ()
    }
}
// Output:
// 3
// 3
// 3
```

因为是闭包，在for迭代语句中，每个defer语句延迟执行的函数引用的都是同一个i迭代变量，在循环结束后这个变量的值为3，因此最终输出的都是3。

修复的思路是在每轮迭代中为每个defer函数生成独有的变量。可以用下面两种方式：

```go
func main() {
    for i := 0; i < 3; i++ {
        i := i // 定义一个循环体内局部变量i
        defer func(){ println(i) } ()
    }
}

func main() {
    for i := 0; i < 3; i++ {
        // 通过函数传入i
        // defer 语句会马上对调用参数求值
        defer func(i int){ println(i) } (i)
    }
}
```

不过一般来说,在for循环内部执行defer语句并不是一个好的习惯，此处仅为示例，不建议使用。


### time

- 计算函数执行时间

有时候，能够知道一个计算执行消耗的时间是非常有意义的，尤其是在对比和基准测试中。最简单的一个办法就是在计算开始之前设置一个起始时候，再由计算结束时的结束时间，最后取出它们的差值，就是这个计算所消耗的时间。想要实现这样的做法，可以使用 `time` 包中的 `Now()` 和 `Sub` 函数：

```go
start := time.Now()
longCalculation()
end := time.Now()
delta := end.Sub(start)
fmt.Printf("longCalculation took this amount of time: %s\n", delta)
```


### 锁和 sync 包

```go
import  "sync"

type Info struct {
	mu sync.Mutex
	// ... other fields, e.g.: Str string
}
```

如果一个函数想要改变这个变量可以这样写:

```go
func Update(info *Info) {
	info.mu.Lock()
    // critical section:
    info.Str = // new value
    // end critical section
    info.mu.Unlock()
}
```


### 接口（interface）与反射（reflection）

接口类型是一种抽象的类型。它不会暴露出它所代表的对象的内部值的结构和这个对象支持的基础操作的集合；它们只会表现出它们自己的方法。也就是说当你有看到一个接口类型的值时，你不知道它是什么，唯一知道的就是可以通过它的方法来做什么。


```go
package main

import "fmt"

type Shaper interface {
	Area() float32
}

type Square struct {
	side float32
}

func (sq *Square) Area() float32 {
	return sq.side * sq.side
}

func main() {
	sq1 := new(Square)
	sq1.side = 5

	var areaIntf Shaper
	areaIntf = sq1
	// shorter,without separate declaration:
	// areaIntf := Shaper(sq1)
	// or even:
	// areaIntf := sq1
	fmt.Printf("The square has area: %f\n", areaIntf.Area())
}
```

这是 **多态** 的 Go 版本，多态是面向对象编程中一个广为人知的概念：根据当前的类型选择正确的方法，或者说：同一种类型在不同的实例上似乎表现出不同的行为。


在Go语言中，一个类只需要实现了接口要求的所有函数，我们就说这个类实现了该接口。



有两个相似的函数来进行字符串的格式化：fmt.Printf，它会把结果写到标准输出，和fmt.Sprintf，它会把结果以字符串的形式返回。得益于使用接口，我们不必可悲的因为返回结果在使用方式上的一些浅显不同就必需把格式化这个最困难的过程复制一份。实际上，这两个函数都使用了另一个函数fmt.Fprintf来进行封装。fmt.Fprintf这个函数对它的计算结果会被怎么使用是完全不知道的。

```go
package fmt

func Fprintf(w io.Writer, format string, args ...interface{}) (int, error)
func Printf(format string, args ...interface{}) (int, error) {
    return Fprintf(os.Stdout, format, args...)
}
func Sprintf(format string, args ...interface{}) string {
    var buf bytes.Buffer
    Fprintf(&buf, format, args...)
    return buf.String()
}
```
Fprintf的前缀F表示文件（File）也表明格式化输出结果应该被写入第一个参数提供的文件中。在Printf函数中的第一个参数os.Stdout是*os.File类型；在Sprintf函数中的第一个参数&buf是一个指向可以写入字节的内存缓冲区，然而它 并不是一个文件类型尽管它在某种意义上和文件类型相似。

即使Fprintf函数中的第一个参数也不是一个文件类型。它是io.Writer类型，这是一个接口类型定义如下：
```go
package io

// Writer is the interface that wraps the basic Write method.
type Writer interface {
    // Write writes len(p) bytes from p to the underlying data stream.
    // It returns the number of bytes written from p (0 <= n <= len(p))
    // and any error encountered that caused the write to stop early.
    // Write must return a non-nil error if it returns n < len(p).
    // Write must not modify the slice data, even temporarily.
    //
    // Implementations must not retain p.
    Write(p []byte) (n int, err error)
}
```
io.Writer类型定义了函数Fprintf和这个函数调用者之间的约定。一方面这个约定需要调用者提供具体类型的值就像*os.File和*bytes.Buffer，这些类型都有一个特定签名和行为的Write的函数。另一方面这个约定保证了Fprintf接受任何满足io.Writer接口的值都可以工作。Fprintf函数可能没有假定写入的是一个文件或是一段内存，而是写入一个可以调用Write函数的值。

因为fmt.Fprintf函数没有对具体操作的值做任何假设，而是仅仅通过io.Writer接口的约定来保证行为，所以第一个参数可以安全地传入一个只需要满足io.Writer接口的任意具体类型的值。一个类型可以自由地被另一个满足相同接口的类型替换，被称作可替换性（LSP里氏替换）。这是一个面向对象的特征。



接口指定的规则非常简单：表达一个类型属于某个接口只要这个类型实现这个接口。所以：
```go
var w io.Writer
w = os.Stdout           // OK: *os.File has Write method
w = new(bytes.Buffer)   // OK: *bytes.Buffer has Write method
w = time.Second         // compile error: time.Duration lacks Write method

var rwc io.ReadWriteCloser
rwc = os.Stdout         // OK: *os.File has Read, Write, Close methods
rwc = new(bytes.Buffer) // compile error: *bytes.Buffer lacks Close method
```


#### flag.Value接口

一个标准的接口类型flag.Value是怎么帮助命令行标记定义新的符号的。思考下面这个会休眠特定时间的程序：
```go
var period = flag.Duration("period", 1*time.Second, "sleep period")

func main() {
    flag.Parse()
    fmt.Printf("Sleeping for %v...", *period)
    time.Sleep(*period)
    fmt.Println()
}
```
在它休眠前它会打印出休眠的时间周期。fmt包调用time.Duration的String方法打印这个时间周期是以用户友好的注解方式，而不是一个纳秒数字：
```
$ go build gopl.io/ch7/sleep
$ ./sleep
Sleeping for 1s...
```
默认情况下，休眠周期是一秒，但是可以通过 -period 这个命令行标记来控制。flag.Duration函数创建一个time.Duration类型的标记变量并且允许用户通过多种用户友好的方式来设置这个变量的大小，这种方式还包括和String方法相同的符号排版形式。这种对称设计使得用户交互良好。
```
$ ./sleep -period 50ms
Sleeping for 50ms...
$ ./sleep -period 2m30s
Sleeping for 2m30s...
$ ./sleep -period 1.5h
Sleeping for 1h30m0s...
$ ./sleep -period "1 day"
invalid value "1 day" for flag -period: time: invalid duration 1 day
```

#### sort.Interface接口

一个内置的排序算法需要知道三个东西：序列的长度，表示两个元素比较的结果，一种交换两个元素的方式；这就是sort.Interface的三个方法：
```go
package sort

type Interface interface {
    Len() int
    Less(i, j int) bool // i, j are indices of sequence elements
    Swap(i, j int)
}
```

为了对序列进行排序，我们需要定义一个实现了这三个方法的类型，然后对这个类型的一个实例应用sort.Sort函数。思考对一个字符串切片进行排序，这可能是最简单的例子了。下面是这个新的类型StringSlice和它的Len，Less和Swap方法
```go
type StringSlice []string
func (p StringSlice) Len() int           { return len(p) }
func (p StringSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p StringSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }
```
现在我们可以通过像下面这样将一个切片转换为一个StringSlice类型来进行排序：
```go
sort.Sort(StringSlice(names))
```
对字符串切片的排序是很常用的需要，所以sort包提供了StringSlice类型，也提供了Strings函数能让上面这些调用简化成sort.Strings(names)。


#### http.Handler接口

```go
package http

type Handler interface {
    ServeHTTP(w ResponseWriter, r *Request)
}

func ListenAndServe(address string, h Handler) error
```

ListenAndServe函数需要一个例如“localhost:8000”的服务器地址，和一个所有请求都可以分派的Handler接口实例。它会一直运行，直到这个服务因为一个错误而失败（或者启动失败），它的返回值一定是一个非空的错误。

想象一个电子商务网站，为了销售它的数据库将它物品的价格映射成美元。下面这个程序可能是能想到的最简单的实现了。它将库存清单模型化为一个命名为database的map类型，我们给这个类型一个ServeHttp方法，这样它可以满足http.Handler接口。这个handler会遍历整个map并输出物品信息。

```go
func main() {
    db := database{"shoes": 50, "socks": 5}
    log.Fatal(http.ListenAndServe("localhost:8000", db))
}

type dollars float32

func (d dollars) String() string { return fmt.Sprintf("$%.2f", d) }

type database map[string]dollars

func (db database) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    for item, price := range db {
        fmt.Fprintf(w, "%s: %s\n", item, price)
    }
}
```
如果我们启动这个服务，
```
$ go build http1
$ ./http1 &
```
连接服务器,我们得到下面的输出：
```
shoes: $50.00
socks: $5.00
```

目前为止，这个服务器不考虑URL只能为每个请求列出它全部的库存清单。更真实的服务器会定义多个不同的URL，每一个都会触发一个不同的行为。让我们使用/list来调用已经存在的这个行为并且增加另一个/price调用表明单个货品的价格，像这样/price?item=socks来指定一个请求参数。

```go
func (db database) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    switch req.URL.Path {
    case "/list":
        for item, price := range db {
            fmt.Fprintf(w, "%s: %s\n", item, price)
        }
    case "/price":
        item := req.URL.Query().Get("item")
        price, ok := db[item]
        if !ok {
            w.WriteHeader(http.StatusNotFound) // 404
            fmt.Fprintf(w, "no such item: %q\n", item)
            return
        }
        fmt.Fprintf(w, "%s\n", price)
    default:
        w.WriteHeader(http.StatusNotFound) // 404
        fmt.Fprintf(w, "no such page: %s\n", req.URL)
    }
}
```


#### 类型判断

```go
type Square struct {
	side float32
}

type Circle struct {
	radius float32
}

type Shaper interface {
	Area() float32
}

func main() {
	var areaIntf Shaper
	sq1 := new(Square)
	sq1.side = 5

	areaIntf = sq1
	// Is Square the type of areaIntf?
	if t, ok := areaIntf.(*Square); ok {
		fmt.Printf("The type of areaIntf is: %T\n", t)
	}
	if u, ok := areaIntf.(*Circle); ok {
		fmt.Printf("The type of areaIntf is: %T\n", u)
	} else {
		fmt.Println("areaIntf does not contain a variable of type Circle")
	}
}
```

```go
switch t := areaIntf.(type) {
case *Square:
	fmt.Printf("Type Square %T with value %v\n", t, t)
case *Circle:
	fmt.Printf("Type Circle %T with value %v\n", t, t)
case nil:
	fmt.Printf("nil value: nothing to check?\n")
default:
	fmt.Printf("Unexpected type %T\n", t)
}
```


#### 方法和类型的反射

反射是用程序检查其所拥有的结构，尤其是类型的一种能力；这是元编程的一种形式。反射可以在运行时检查类型和变量，例如它的大小、方法和 `动态`
的调用这些方法。这对于没有源代码的包尤其有用。这是一个强大的工具，除非真得有必要，否则应当避免使用或小心使用。

变量的最基本信息就是类型和值：反射包的 `Type` 用来表示一个 Go 类型，反射包的 `Value` 为 Go 值提供了反射接口。

两个简单的函数，`reflect.TypeOf` 和 `reflect.ValueOf`，返回被检查对象的类型和值。例如，x 被定义为：`var x float64 = 3.4`，那么 `reflect.TypeOf(x)` 返回 `float64`，`reflect.ValueOf(x)` 返回 `<float64 Value>`

```go
// blog: Laws of Reflection
package main

import (
	"fmt"
	"reflect"
)

func main() {
	var x float64 = 3.4
	fmt.Println("type:", reflect.TypeOf(x))
	v := reflect.ValueOf(x)
	fmt.Println("value:", v)
	fmt.Println("type:", v.Type())
	fmt.Println("kind:", v.Kind())
	fmt.Println("value:", v.Float())
	fmt.Println(v.Interface())
	fmt.Printf("value is %5.2e\n", v.Interface())
	y := v.Interface().(float64)
	fmt.Println(y)
}
```

输出：

```
type: float64
value: 3.4
type: float64
kind: float64
value: 3.4
3.4
value is 3.40e+00
3.4
```

#### Printf 和反射

fmt 包中的 Printf（以及其他格式化输出函数）都会使用反射来分析它的 `...` 参数。

Printf 的函数声明为：

```go
func Printf(format string, args ... interface{}) (n int, err error)
```



### 读写数据

#### 读取用户的输入

读取用户的键盘（控制台）输入，从键盘和标准输入 `os.Stdin` 读取输入，最简单的办法是使用 `fmt` 包提供的 Scan 和 Sscan 开头的函数。

```go
// 从控制台读取输入:
package main
import "fmt"

var (
   firstName, lastName, s string
   i int
   f float32
   input = "56.12 / 5212 / Go"
   format = "%f / %d / %s"
)

func main() {
   fmt.Println("Please enter your full name: ")
   fmt.Scanln(&firstName, &lastName)
   // fmt.Scanf("%s %s", &firstName, &lastName)
   fmt.Printf("Hi %s %s!\n", firstName, lastName) // Hi Chris Naegels
   fmt.Sscanf(input, format, &f, &i, &s)
   fmt.Println("From the string we read: ", f, i, s)
    // 输出结果: From the string we read: 56.12 5212 Go
}
```

#### 文件读写

##### 读文件

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
)

func main() {
    inputFile, inputError := os.Open("input.dat")
    if inputError != nil {
        fmt.Printf("An error occurred on opening the inputfile\n" +
            "Does the file exist?\n" +
            "Have you got acces to it?\n")
        return // exit the function on error
    }
    defer inputFile.Close()

    inputReader := bufio.NewReader(inputFile)
    for {
        inputString, readerError := inputReader.ReadString('\n')
        fmt.Printf("The input was: %s", inputString)
        if readerError == io.EOF {
            return
        }      
    }
}
```

##### 查找重复的行

bufio包，它使处理输入和输出方便又高效。Scanner类型是该包最有用的特性之一，它读取输入并将其拆成行或单词；通常是处理行形式的输入最简单的方法。

程序使用短变量声明创建bufio.Scanner类型的变量input。

input := bufio.NewScanner(os.Stdin)
该变量从程序的标准输入中读取内容。每次调用input.Scan()，即读入下一行，并移除行末的换行符；读取的内容可以调用input.Text()得到。Scan函数在读到一行时返回true，不再有输入时返回false。

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    counts := make(map[string]int)
    files := os.Args[1:]
    if len(files) == 0 {
        countLines(os.Stdin, counts)
    } else {
        for _, arg := range files {
            f, err := os.Open(arg)
            if err != nil {
                fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
                continue
            }
            countLines(f, counts)
            f.Close()
        }
    }
    for line, n := range counts {
        if n > 1 {
            fmt.Printf("%d\t%s\n", n, line)
        }
    }
}

func countLines(f *os.File, counts map[string]int) {
    input := bufio.NewScanner(f)
    for input.Scan() {
        counts[input.Text()]++
    }
    // NOTE: ignoring potential errors from input.Err()
}
```

##### ReadFile函数（来自于io/ioutil包），其读取指定文件的全部内容

```go
package main

import (
    "fmt"
    "io/ioutil"
    "os"
    "strings"
)

func main() {
    counts := make(map[string]int)
    for _, filename := range os.Args[1:] {
        data, err := ioutil.ReadFile(filename)
        if err != nil {
            fmt.Fprintf(os.Stderr, "dup3: %v\n", err)
            continue
        }
        for _, line := range strings.Split(string(data), "\n") {
            counts[line]++
        }
    }
    for line, n := range counts {
        if n > 1 {
            fmt.Printf("%d\t%s\n", n, line)
        }
    }
}
```

ReadFile函数返回一个字节切片（byte slice），必须把它转换为string，才能用strings.Split分割。


#### 用切片读写文件

切片提供了 Go 中处理 I/O 缓冲的标准方式，下面 `cat` 函数，在一个切片缓冲内使用无限 for 循环（直到文件尾部 EOF）读取文件，并写入到标准输出（`os.Stdout`）。

```go
func cat(f *os.File) {
	const NBUF = 512
	var buf [NBUF]byte
	for {
		switch nr, err := f.Read(buf[:]);  {
		case nr < 0:
			fmt.Fprintf(os.Stderr, "cat: error reading: %s\n", err.Error())
			os.Exit(1)
		case nr == 0: // EOF
			return
		case nr > 0:
			if nw, ew := os.Stdout.Write(buf[0:nr]); nw != nr {
				fmt.Fprintf(os.Stderr, "cat: error writing: %s\n", ew.Error())
			}
		}
	}
}
```


#### 从命令行读取参数

os 包中有一个 string 类型的切片变量 `os.Args`，用来处理一些基本的命令行参数，它在程序启动后读取命令行输入的参数。

```go
// os_args.go
package main

import (
	"fmt"
	"os"
	"strings"
)

func main() {
	who := "Alice "
	if len(os.Args) > 1 {
		who += strings.Join(os.Args[1:], " ")
	}
	fmt.Println("Good Morning", who)
}
```

### Go 中的密码学

- `hash` 包：实现了 `adler32`、`crc32`、`crc64` 和 `fnv` 校验；
- `crypto` 包：实现了其它的 hash 算法，比如 `md4`、`md5`、`sha1` 等。以及完整地实现了 `aes`、`blowfish`、`rc4`、`rsa`、`xtea` 等加密算法。




### 错误处理与测试

Go 没有像 Java 和 .NET 那样的 `try/catch` 异常机制：不能执行抛异常操作。但是有一套 `defer-panic-and-recover` 机制。

当程序处于错误状态时可以用 `os.Exit(1)` 来中止运行。


#### 定义错误

任何时候当你需要一个新的错误类型，都可以用 `errors`（必须先 import）包的 `errors.New` 函数接收合适的错误信息来创建，像下面这样：

```go
err := errors.New("math - square root of negative number")
```

```go
// errors.go
package main

import (
	"errors"
	"fmt"
)

var errNotFound error = errors.New("Not found error")

func main() {
	fmt.Printf("error: %v", errNotFound)
}
// error: Not found error
```

可以把它用于计算平方根函数的参数测试：

```go
func Sqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, errors.New ("math - square root of negative number")
	}
   // implementation of Sqrt
}
```

你可以像下面这样调用 Sqrt 函数：

```go
if f, err := Sqrt(-1); err != nil {
	fmt.Printf("Error: %s\n", err)
}
```

#### 用 fmt 创建错误对象

通常你想要返回包含错误参数的更有信息量的字符串，例如：可以用 `fmt.Errorf()` 来实现：它和 fmt.Printf() 完全一样，接收一个或多个格式占位符的格式化字符串和相应数量的占位变量。和打印信息不同的是它用信息生成错误对象。

比如在前面的平方根例子中使用：

```go
if f < 0 {
	return 0, fmt.Errorf("math: square root of negative number %g", f)
}
```

第二个例子：从命令行读取输入时，如果加了 help 标志，我们可以用有用的信息产生一个错误：

```go
if len(os.Args) > 1 && (os.Args[1] == "-h" || os.Args[1] == "--help") {
	err = fmt.Errorf("usage: %s infile.txt outfile.txt", filepath.Base(os.Args[0]))
	return
}
```

### 运行时异常和 panic

当发生像数组下标越界或类型断言失败这样的运行错误时，Go 运行时会触发*运行时 panic*，伴随着程序的崩溃抛出一个 `runtime.Error` 接口类型的值。这个错误值有个 `RuntimeError()` 方法用于区别普通错误。

`panic` 可以直接从代码初始化：当错误条件（我们所测试的代码）很严苛且不可恢复，程序不能继续运行时，可以使用 `panic` 函数产生一个中止程序的运行时错误。`panic` 接收一个做任意类型的参数，通常是字符串，在程序死亡时被打印出来。Go 运行时负责中止程序并给出调试信息。

```go
package main

import "fmt"

func main() {
	fmt.Println("Starting the program")
	panic("A severe error occurred: stopping the program!")
	fmt.Println("Ending the program")
}
```

输出如下：

```
Starting the program
panic: A severe error occurred: stopping the program!
panic PC=0x4f3038
runtime.panic+0x99 /go/src/pkg/runtime/proc.c:1032
       runtime.panic(0x442938, 0x4f08e8)
main.main+0xa5 E:/Go/GoBoek/code examples/chapter 13/panic.go:8
       main.main()
runtime.mainstart+0xf 386/asm.s:84
       runtime.mainstart()
runtime.goexit /go/src/pkg/runtime/proc.c:148
       runtime.goexit()
---- Error run E:/Go/GoBoek/code examples/chapter 13/panic.exe with code Crashed
---- Program exited with code -1073741783
```

panic 会导致栈被展开直到 defer 修饰的 recover() 被调用或者程序中止。



### Go 中的单元测试和基准测试

对一个包做（单元）测试，需要写一些可以频繁（每次更新后）执行的小块测试单元来检查代码的正确性。于是我们必须写一些 Go 源文件来测试代码。测试程序必须属于被测试的包，并且文件名满足这种形式 `*_test.go`，所以测试代码和包中的业务代码是分开的。

`_test` 程序不会被普通的 Go 编译器编译，所以当放应用部署到生产环境时它们不会被部署；只有 gotest 会编译所有的程序：普通程序和测试程序。

测试文件中必须导入 "testing" 包，并写一些名字以 `TestZzz` 打头的全局函数，这里的 `Zzz` 是被测试函数的字母描述，如 TestFmtInterface，TestPayEmployees 等。

测试函数必须有这种形式的头部：

```go
func TestAbcde(t *testing.T)
```

T 是传给测试函数的结构类型，用来管理测试状态，支持格式化测试日志，如 t.Log，t.Error，t.ErrorF 等。在函数的结尾把输出跟想要的结果对比，如果不等就打印一个错误。成功的测试则直接返回。

用下面这些函数来通知测试失败：

1）```func (t *T) Fail()```

标记测试函数为失败，然后继续执行（剩下的测试）。

2）```func (t *T) FailNow()```

标记测试函数为失败并中止执行；文件中别的测试也被略过，继续执行下一个文件。

3）```func (t *T) Log(args ...interface{})```

args 被用默认的格式格式化并打印到错误日志中。

4）```func (t *T) Fatal(args ...interface{})```

结合 先执行 3），然后执行 2）的效果。

运行 go test 来编译测试程序，并执行程序中所有的 TestZZZ 函数。如果所有的测试都通过会打印出 PASS。

gotest 可以接收一个或多个函数程序作为参数，并指定一些选项。

结合 --chatty 或 -v 选项，每个执行的测试函数以及测试状态会被打印。

例如：

```bash
go test fmt_test.go --chatty
=== RUN fmt.TestFlagParser
--- PASS: fmt.TestFlagParser
=== RUN fmt.TestArrayPrinter
--- PASS: fmt.TestArrayPrinter
...
```

testing 包中有一些类型和函数可以用来做简单的基准测试；测试代码中必须包含以 `BenchmarkZzz` 打头的函数并接收一个 `*testing.B` 类型的参数，比如：

```go
func BenchmarkReverse(b *testing.B) {
	...
}
```

命令 ```go test –test.bench=.*``` 会运行所有的基准测试函数；代码中的函数会被调用 N 次（N是非常大的数，如 N = 1000000），并展示 N 的值和函数执行的平均时间，单位为 ns（纳秒，ns/op）。如果是用 testing.Benchmark 调用这些函数，直接运行程序即可。


### 协程（goroutine）与通道（channel）

**不要通过共享内存来通信，而通过通信来共享内存。**

主函数返回时，所有的goroutine都会被直接打断，程序退出。

#### 如何用命令行指定使用的核心数量

使用 `flags` 包，如下：

```go
var numCores = flag.Int("n", 2, "number of CPU cores to use")
```
在 main() 中：
```go
flag.Parse()
runtime.GOMAXPROCS(*numCores)
```


#### 协程间的信道


```go
var ch1 chan string
ch1 = make(chan string)
```

当然可以更短： `ch1 := make(chan string)`。


这个操作符直观的标示了数据的传输：信息按照箭头的方向流动。

流向通道（发送）

`ch <- int1` 表示：用通道 ch 发送变量 int1（双目运算符，中缀 = 发送）

从通道流出（接收），三种方式：

`int2 = <- ch` 表示：变量 int2 从通道 ch（一元运算的前缀操作符，前缀 = 接收）接收数据（获取新值）；假设 int2 已经声明过了，如果没有的话可以写成：`int2 := <- ch`。

`<- ch` 可以单独调用获取通道的（下一个）值，当前值会被丢弃，但是可以用来验证，所以以下代码是合法的：

```go
if <- ch != 1000{
	...
}
```

通道的发送和接收都是原子操作：它们总是互不干扰的完成的。


```go
func Count(ch chan int) {
    // 通过ch <- 1语句向对应的channel中写入一个数据
    ch <- 1
    fmt.Println("Counting")
}

func main() {
    // 定义了一个包含10个channel的数组
    chs := make([]chan int, 10)
    // 把数组中的每个channel分配给10个不同的goroutine
    for i := 0; i < 10; i++ {
        chs[i] = make(chan int)
        go Count(chs[i])
    }

    for _, ch := range(chs) {
        <-ch
    }
}
```

在这个channel被读取前，这个操作是阻塞的。在所有的goroutine启动完成后，我们通过<-ch语句从10个channel中依次读取数据。在对应的channel写入数据前，这个操作也是阻塞的。这样，我们就用channel实现了类似锁的功能，进而保证了所有goroutine完成后主函数才返回。


将一个数据写入（发送）至channel的语法很直观，如下：

```go
ch <- value
```

向channel写入数据通常会导致程序阻塞，直到有其他goroutine从这个channel中读取数据。从channel中读取数据的语法是:

```go
value := <-ch
```

如果channel之前没有写入数据，那么从channel中读取数据也会导致程序阻塞，直到channel中被写入数据为止。

- select

早在Unix时代，select机制就已经被引入。通过调用select()函数来监控一系列的文件句柄，一旦其中一个文件句柄发生了IO动作，该select()调用就会被返回。后来该机制也被用于实现高并发的Socket服务器程序。Go语言直接在语言级别支持select关键字，用于处理异步IO问题。

```go
select {
    case <-chan1:
        // 如果chan1成功读到数据，则进行该case处理语句
    case chan2 <- 1:
        // 如果成功向chan2写入数据，则进行该case处理语句
    default:
        // 如果上面都没有成功，则进入default处理流程
}
```

如果多个case同时就绪时，select会随机地选择一个执行，这样来保证每一个channel都有平等的被select的机会。

channel的零值是nil。也许会让你觉得比较奇怪，nil的channel有时候也是有一些用处的。因为对一个nil的channel发送和接收操作会永远阻塞，在select语句中操作nil的channel永远都不会被select到。


- 缓冲机制

创建一个带缓冲的channel：

```go
c := make(chan int, 1024)
```

在调用make()时将缓冲区大小作为第二个参数传入即可，比如上面这个例子就创建了一个大小为1024的int类型channel，即使没有读取方，写入方也可以一直往channel里写入，在缓冲区被填完之前都不会阻塞。


- 超时机制

Go语言没有提供直接的超时处理机制，但我们可以利用select机制。虽然select机制不是专为超时而设计的，却能很方便地解决超时问题。因为select的特点是只要其中一个case已经完成，程序就会继续往下执行，而不会考虑其他case的情况。

基于此特性，我们来为channel实现超时机制：

```go
// 首先，我们实现并执行一个匿名的超时等待函数
timeout := make(chan bool, 1)
go func() {
    time.Sleep(1e9) // 等待1秒钟
    timeout <- true
}()

// 然后我们把timeout这个channel利用起来
select {
    case <-ch:
        // 从ch中读取到数据
    case <-timeout:
        // 一直没有从ch中读取到数据，但从timeout中读取到了数据
}
```

这样使用select机制可以避免永久等待的问题，因为程序会在timeout中获取到一个数据后继续执行，无论对ch的读取是否还处于等待状态，从而达成1秒超时的效果。这种写法看起来是一个小技巧，但却是在Go语言开发中避免channel通信超时的最有效方法。在实际的开发过程中，这种写法也需要被合理利用起来，从而有效地提高代码质量。



- 全局唯一性操作

```go
var a string
var once sync.Once
func setup() {
    a = "hello, world"
}
func doprint() {
    once.Do(setup)
    print(a)
}
func twoprint() {
    go doprint()
    go doprint()
}
```
once的Do()方法可以保证在全局范围内只调用指定的函数一次（这里指setup()函数），而且所有其他goroutine在调用到此语句时，将会先被阻塞，直至全局唯一的 once.Do()调用结束后才继续。



关闭后的channel有以下特点：

- 对一个关闭的channel再发送值就会导致panic；
- 对一个关闭的channel进行接收会一直能获取到值，直到channel为空；
- 对一个关闭的channel并且没有值的channel执行接收操作会得到对应类型的零值；
- 关闭一个已经关闭的channel会导致panic。


Channel还支持close操作，用于关闭channel，随后对基于该channel的任何发送操作都将导致panic异常。
对一个已经被close过的channel进行接收操作依然可以接受到之前已经成功发送的数据；如果channel中已经没有数据的话将产生一个零值的数据。

不管一个channel是否被关闭，当它没有被引用时将会被Go语言的垃圾自动回收器回收。


#### 通道、超时和计时器（Ticker）

`time` 包中有一些有趣的功能可以和通道组合使用。

其中就包含了 `time.Ticker` 结构体，这个对象以指定的时间间隔重复的向通道 C 发送时间值：

```go
type Ticker struct {
    C <-chan Time // the channel on which the ticks are delivered.
    // contains filtered or unexported fields
    ...
}
```

时间间隔的单位是 ns（纳秒，int64），在工厂函数 `time.NewTicker` 中以 `Duration` 类型的参数传入：`func NewTicker(dur) *Ticker`。

在协程周期性的执行一些事情（打印状态日志，输出，计算等等）的时候非常有用。

调用 `Stop()` 使计时器停止，在 `defer` 语句中使用。这些都很好的适应 `select` 语句:

```go
ticker := time.NewTicker(updateInterval)
defer ticker.Stop()
...
select {
case u:= <-ch1:
    ...
case v:= <-ch2:
    ...
case <-ticker.C:
    logState(status) // call some logging function logState
default: // no value ready to be received
    ...
}
```

#### 原子操作

所谓的原子操作就是并发编程中“最小的且不可并行化”的操作。

一般情况下，原子操作都是通过“互斥”访问来保证的，通常由特殊的CPU指令提供保护。当然，如果仅仅是想模拟下粗粒度的原子操作，我们可以借助于sync.Mutex来实现：

```go
import (
    "sync"
)

var total struct {
    sync.Mutex
    value int
}

func worker(wg *sync.WaitGroup) {
    defer wg.Done()

    for i := 0; i <= 100; i++ {
        total.Lock()
        total.value += i
        total.Unlock()
    }
}

func main() {
    var wg sync.WaitGroup
    wg.Add(2)
    go worker(&wg)
    go worker(&wg)
    wg.Wait()

    fmt.Println(total.value)
}
```

用互斥锁来保护一个数值型的共享资源，麻烦且效率低下。标准库的sync/atomic包对原子操作提供了丰富的支持。我们可以重新实现上面的例子：

```go
import (
    "sync"
    "sync/atomic"
)

var total uint64

func worker(wg *sync.WaitGroup) {
    defer wg.Done()

    var i uint64
    for i = 0; i <= 100; i++ {
        atomic.AddUint64(&total, i)
    }
}

func main() {
    var wg sync.WaitGroup
    wg.Add(2)

    go worker(&wg)
    go worker(&wg)
    wg.Wait()
}
```

atomic.AddUint64函数调用保证了total的读取、更新和保存是一个原子操作，因此在多线程中访问也是安全的。


原子操作配合互斥锁可以实现非常高效的单件模式。互斥锁的代价比普通整数的原子读写高很多，在性能敏感的地方可以增加一个数字型的标志位，通过原子检测标志位状态降低互斥锁的使用次数来提高性能。


#### 生产者消费者模型

Go语言实现生产者消费者并发很简单：

```go
// 生产者: 生成 factor 整数倍的序列
func Producer(factor int, out chan<- int) {
    for i := 0; ; i++ {
        out <- i*factor
    }
}

// 消费者
func Consumer(in <-chan int) {
    for v := range in {
        fmt.Println(v)
    }
}
func main() {
    ch := make(chan int, 64) // 成果队列

    go Producer(3, ch) // 生成 3 的倍数的序列
    go Producer(5, ch) // 生成 5 的倍数的序列
    go Consumer(ch)    // 消费 生成的队列

    // 运行一定时间后退出
    time.Sleep(5 * time.Second)
}
```

我们开启了2个Producer生产流水线，分别用于生成3和5的倍数的序列。然后开启1个Consumer消费者线程，打印获取的结果。我们通过在main函数休眠一定的时间来让生产者和消费者工作一定时间。正如前面一节说的，这种靠休眠方式是无法保证稳定的输出结果的。

我们可以让main函数保存阻塞状态不退出，只有当用户输入Ctrl-C时才真正退出程序：

```go
func main() {
    ch := make(chan int, 64) // 成果队列

    go Producer(3, ch) // 生成 3 的倍数的序列
    go Producer(5, ch) // 生成 5 的倍数的序列
    go Consumer(ch)    // 消费 生成的队列

    // Ctrl+C 退出
    sig := make(chan os.Signal, 1)
    signal.Notify(sig, syscall.SIGINT, syscall.SIGTERM)
    fmt.Printf("quit (%v)\n", <-sig)
}
```

我们这个例子中有2个生产者，并且2个生产者之间并无同步事件可参考，它们是并发的。因此，消费者输出的结果序列的顺序是不确定的，这并没有问题，生产者和消费者依然可以相互配合工作。


#### 发布订阅模型

发布订阅（publish-and-subscribe）模型通常被简写为pub/sub模型。在这个模型中，消息生产者成为发布者（publisher），而消息消费者则成为订阅者（subscriber），生产者和消费者是M:N的关系。在传统生产者和消费者模型中，是将消息发送到一个队列中，而发布订阅模型则是将消息发布给一个主题。

为此，我们构建了一个名为pubsub的发布订阅模型支持包：

```go
// Package pubsub implements a simple multi-topic pub-sub library.
package pubsub

import (
    "sync"
    "time"
)

type (
    subscriber chan interface{}         // 订阅者为一个管道
    topicFunc  func(v interface{}) bool // 主题为一个过滤器
)

// 发布者对象
type Publisher struct {
    m           sync.RWMutex             // 读写锁
    buffer      int                      // 订阅队列的缓存大小
    timeout     time.Duration            // 发布超时时间
    subscribers map[subscriber]topicFunc // 订阅者信息
}

// 构建一个发布者对象, 可以设置发布超时时间和缓存队列的长度
func NewPublisher(publishTimeout time.Duration, buffer int) *Publisher {
    return &Publisher{
        buffer:      buffer,
        timeout:     publishTimeout,
        subscribers: make(map[subscriber]topicFunc),
    }
}

// 添加一个新的订阅者，订阅全部主题
func (p *Publisher) Subscribe() chan interface{} {
    return p.SubscribeTopic(nil)
}

// 添加一个新的订阅者，订阅过滤器筛选后的主题
func (p *Publisher) SubscribeTopic(topic topicFunc) chan interface{} {
    ch := make(chan interface{}, p.buffer)
    p.m.Lock()
    p.subscribers[ch] = topic
    p.m.Unlock()
    return ch
}

// 退出订阅
func (p *Publisher) Evict(sub chan interface{}) {
    p.m.Lock()
    defer p.m.Unlock()

    delete(p.subscribers, sub)
    close(sub)
}

// 发布一个主题
func (p *Publisher) Publish(v interface{}) {
    p.m.RLock()
    defer p.m.RUnlock()

    var wg sync.WaitGroup
    for sub, topic := range p.subscribers {
        wg.Add(1)
        go p.sendTopic(sub, topic, v, &wg)
    }
    wg.Wait()
}

// 关闭发布者对象，同时关闭所有的订阅者管道。
func (p *Publisher) Close() {
    p.m.Lock()
    defer p.m.Unlock()

    for sub := range p.subscribers {
        delete(p.subscribers, sub)
        close(sub)
    }
}

// 发送主题，可以容忍一定的超时
func (p *Publisher) sendTopic(
    sub subscriber, topic topicFunc, v interface{}, wg *sync.WaitGroup,
) {
    defer wg.Done()
    if topic != nil && !topic(v) {
        return
    }

    select {
    case sub <- v:
    case <-time.After(p.timeout):
    }
}
```

下面的例子中，有两个订阅者分别订阅了全部主题和含有"golang"的主题：

```go
import "path/to/pubsub"

func main() {
    p := pubsub.NewPublisher(100*time.Millisecond, 10)
    defer p.Close()

    all := p.Subscribe()
    golang := p.SubscribeTopic(func(v interface{}) bool {
        if s, ok := v.(string); ok {
            return strings.Contains(s, "golang")
        }
        return false
    })

    p.Publish("hello,  world!")
    p.Publish("hello, golang!")

    go func() {
        for  msg := range all {
            fmt.Println("all:", msg)
        }
    } ()

    go func() {
        for  msg := range golang {
            fmt.Println("golang:", msg)
        }
    } ()

    // 运行一定时间后退出
    time.Sleep(3 * time.Second)
}
```
在发布订阅模型中，每条消息都会传送给多个订阅者。发布者通常不会知道、也不关心哪一个订阅者正在接收主题消息。订阅者和发布者可以在运行时动态添加，是一种松散的耦合关系，这使得系统的复杂性可以随时间的推移而增长。在现实生活中，像天气预报之类的应用就可以应用这个并发模式。



### tcp 服务器

server:

```go
package main

import (
	"fmt"
	"net"
)

func main() {
	fmt.Println("Starting the server ...")
	// 创建 listener
	listener, err := net.Listen("tcp", "localhost:50000")
	if err != nil {
		fmt.Println("Error listening", err.Error())
		return //终止程序
	}
	// 监听并接受来自客户端的连接
	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Error accepting", err.Error())
			return // 终止程序
		}
		go doServerStuff(conn)
	}
}

func doServerStuff(conn net.Conn) {
	for {
		buf := make([]byte, 512)
		len, err := conn.Read(buf)
		if err != nil {
			fmt.Println("Error reading", err.Error())
			return //终止程序
		}
		fmt.Printf("Received data: %v", string(buf[:len]))
	}
}
```

client：

```go
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	//打开连接:
	conn, err := net.Dial("tcp", "localhost:50000")
	if err != nil {
		//由于目标计算机积极拒绝而无法创建连接
		fmt.Println("Error dialing", err.Error())
		return // 终止程序
	}

	inputReader := bufio.NewReader(os.Stdin)
	fmt.Println("First, what is your name?")
	clientName, _ := inputReader.ReadString('\n')
	// fmt.Printf("CLIENTNAME %s", clientName)
	trimmedClient := strings.Trim(clientName, "\r\n") // Windows 平台下用 "\r\n"，Linux平台下使用 "\n"
	// 给服务器发送信息直到程序退出：
	for {
		fmt.Println("What to send to the server? Type Q to quit.")
		input, _ := inputReader.ReadString('\n')
		trimmedInput := strings.Trim(input, "\r\n")
		// fmt.Printf("input:--%s--", input)
		// fmt.Printf("trimmedInput:--%s--", trimmedInput)
		if trimmedInput == "Q" {
			return
		}
		_, err = conn.Write([]byte(trimmedClient + " says: " + trimmedInput))
	}
}
```

### 编译调试

- 生成可执行文件

```
go build -o server.exe main.go
go build -o server main.go
```



- windows后台运行

```
go build -ldflags "-H windowsgui" .\main.go
```



### 其他

- rune类型

当需要处理中文或其他复合字符时，则需要要到rune类型。rune类型实际上是一个int32。

```go
s1 := "abc"
bytes1 := []byte(s1)
bytes1[0] = 'A'
fmt.Println(string(bytes1)) // Abc

s2 := "一二三"
runes2 := []rune(s2)
runes2[0] = '零'
fmt.Println(string(runes2)) // 零二三
```

Go语言除了for range语法对UTF8字符串提供了特殊支持外，还对字符串和[]rune类型的相互转换提供了特殊的支持。

```go
fmt.Printf("%#v\n", []rune("世界"))              // []int32{19990, 30028}
fmt.Printf("%#v\n", string([]rune{'世', '界'})) // 世界
```

[]rune其实是[]int32类型，这里的rune只是int32类型的别名，并不是重新定义的类型。rune用于表示每个Unicode码点，目前只使用了21个bit位。


- 类型转换

```go
// 整型转换成字符串
var i int = 123
stri := strconv.FormatInt(int64(i), 10)
fmt.Printf("stri:%v, 类型:%T\n", stri, stri) // stri:123, 类型:string

// 字符串转换成整型
num, _ := strconv.ParseInt(stri, 10, 64)
fmt.Printf("num:%v, 类型:%T\n", num, num) // num:123, 类型:int64
```


- 选择语句

条件表达式不限制为常量或者整数；

单个case中，可以出现多个结果选项；

与C语言等规则相反，Go语言不需要用break来明确退出一个case；

只有在case中明确添加fallthrough关键字，才会继续执行紧跟的下一个case。


- 不定参数

```go
func myfunc(args ...int) {
    for _, arg := range args {
        fmt.Println(arg)
    }
}

myfunc(1)
myfunc(1, 2, 3)
```

形如...type格式的类型只能作为函数的参数类型存在，并且必须是最后一个参数。

从内部实现机理上来说，类型...type本质上是一个数组切片，也就是[]type，这也是为什么上面的参数args可以用for循环来获得每个传入的参数。

- 任意类型的不定参数

如果你希望传任意类型，可以指定类型为 interface{}。

```go
func MyPrintf(args ...interface{}) {
    for _, arg := range args {
        switch arg.(type) {
            case int:
                fmt.Println(arg, "is an int value.")
            case string:
                fmt.Println(arg, "is a string value.")
            case int64:
                fmt.Println(arg, "is an int64 value.")
            default:
                fmt.Println(arg, "is an unknown type.")
        }
    }
}

func main() {
    var v1 int = 1
    var v2 int64 = 234
    var v3 string = "hello"
    var v4 float32 = 1.234

    MyPrintf(v1, v2, v3, v4)
}
```

- error接口

Go语言引入了一个关于错误处理的标准模式，即error接口，该接口的定义如下：

```go
type error interface {
    Error() string
}
```

```go
func Foo(param int)(n int, err error) {
    // ...
}

n, err := Foo(0)
if err != nil {
    // 错误处理
} else {
    // 使用返回值n
}
```

- panic和recover

Go语言引入了两个内置函数panic()和recover()以报告和处理运行时错误和程序中的错误场景：
```go
func panic(interface{})
func recover() interface{}
```

当在一个函数执行过程中调用panic()函数时，正常的函数执行流程将立即终止，但函数中之前使用defer关键字延迟执行的语句将正常展开执行，之后该函数将返回到调用函数，并导致逐层向上执行panic流程，直至所属的goroutine中所有正在执行的函数被终止。错误信息将被报告，包括在调用panic()函数时传入的参数，这个过程称为错误处理流程。

```go
panic(404)
panic("network broken")
panic(Error("file not exists"))
```

recover()函数用于终止错误处理流程。一般情况下，recover()应该在一个使用defer关键字的函数中执行以有效截取错误处理流程。

- 结构体

在Go语言中，未进行显式初始化的变量都会被初始化为该类型的零值，例如bool类型的零值为false，int类型的零值为0，string类型的零值为空字符串。

在Go语言中没有构造函数的概念，对象的创建通常交由一个全局的创建函数来完成，以NewXXX来命名，表示“构造函数”：

```go
func NewRect(x, y, width, height float64) *Rect {
    return &Rect{x, y, width, height}
}
```

通过嵌入结构体来扩展类型：

```go
type Point struct{ X, Y float64 }

type ColoredPoint struct {
    Point
    Color color.RGBA
}
```

- 匿名组合

确切地说，Go语言也提供了继承，但是采用了组合的文法，所以我们将其称为匿名组合：

```go
type Job struct {
    Command string
    *log.Logger   // 匿名组合了一个log.Logger指针
}

// 在合适的赋值后，我们在Job类型的所有成员方法中可以很舒适地借用所有log.Logger提供的方法。
func (job *Job)Start() {
    job.Log("starting now...")
    // 做一些事情... 
    job.Log("started.")
}
```




## Effective go

- 使用strings包的Join函数

+=连接原字符串、空格和下个参数，产生新字符串, 并把它赋值给s。s原来的内容已经不再使用，将在适当时机对它进行垃圾回收。如果连接涉及的数据量很大，这种方式代价高昂。一种简单且高效的解决方案是使用strings包的Join函数。

- 了解变量的生命周期

如果将指向短生命周期对象的指针保存到具有长生命周期的对象中，特别是保存到全局变量时，会阻止对短生命周期对象的垃圾回收（从而可能影响程序的性能）。


