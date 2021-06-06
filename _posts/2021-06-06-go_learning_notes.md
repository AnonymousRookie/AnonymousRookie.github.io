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

- goimports

goimports工具是Go官方提供的一种工具，它能够为我们自动格式化 Go 语言代码并对所有引入的包进行管理，包括自动增删依赖的包引用、将依赖包按字母序排序并分类。


## VS Code go插件

在 VS Code 中，使用快捷键：command+shift+P，然后键入：go:install/update tools，将所有插件都勾选上，然后点击 OK 即开始安装。


## go语言基础知识

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

- 多重赋值

```go
// 交换2个变量的值
i, j = j, i
```

- 预定义常量iota

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


- 整形

类型 | 长度(字节) | 值范围
---|---|---
int8 | 1 | -128 ~ 127
uint8(即byte) | 1 | 0 ~ 255
int16 | 2 | -32 768 ~ 32 767
uint16 | 2 | 0 ~ 65 535
int32 | 4 | 2 147 483 648 ~ 2 147 483 647
uint32 | 4 | 0 ~ 4 294 967 295
int64 | 8 | -9 223 372 036 854 775 808 ~ 9 223 372 036 854 775 807
uint64 | 8 | 0 ~ 18 446 744 073 709 551 615
int | 平台相关 | 平台相关
uint | 平台相关 | 平台相关
uintptr | 同指针 | 在32位平台下为4字节，64位平台下为8字节


int和int32在Go语言里被认为是两种不同的类型，编译器也不会帮你自动做类型转换。

两个不同类型的整型数不能直接比较。


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

- 接口

在Go语言中，一个类只需要实现了接口要求的所有函数，我们就说这个类实现了该接口。


### 协程

channel是Go语言在语言级别提供的goroutine间的通信方式。我们可以使用channel在两个或多个goroutine之间传递消息。

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


一般channel的声明形式为：

```go
var chanName chan ElementType
```

ElementType指定这个channel所能传递的元素类型。


定义一个channel也很简单，直接使用内置的函数make()即可：

```go
ch := make(chan int)
```

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



## Effective go

- 使用strings包的Join函数

+=连接原字符串、空格和下个参数，产生新字符串, 并把它赋值给s。s原来的内容已经不再使用，将在适当时机对它进行垃圾回收。如果连接涉及的数据量很大，这种方式代价高昂。一种简单且高效的解决方案是使用strings包的Join函数。

- 了解变量的生命周期

如果将指向短生命周期对象的指针保存到具有长生命周期的对象中，特别是保存到全局变量时，会阻止对短生命周期对象的垃圾回收（从而可能影响程序的性能）。


