# GO最佳实践：可维护GO程序的指南

> 原文地址 [https://dave.cheney.net/practical-go/presentations/qcon-china.html](https://dave.cheney.net/practical-go/presentations/qcon-china.html)

## 1.指导原则

如果我要用任何编程语言讨论最佳实践，我需要一些方法来定义我的最佳含义。如果您昨天参加我的主题演讲，您会看到Go团队负责人Russ Cox的这段话：

> 当您增加时间和其他程序员时，软件工程就是编程发生的事情。—罗斯·考克斯（Russ Cox）

Russ在软件编程和软件工程之间做出了区分。前者是您自己编写的程序，后者是随着时间的推移许多人会使用的产品。工程师会替换，团队会壮大，需求会改变，功能会添加，错误会修复。这就是软件工程的本质。

我可能是该会议室中最早使用Go的用户之一，但认为我的资历可以使我的观点更具分量是错误的。相反，我今天要提出的建议是根据我认为Go本身所基于的指导原则提供的。他们是：

* 简单
* 可读性
* 生产率

您会注意到，我没有说性能或并发性。有些语言比Go快一点，但是它们肯定不像Go那样简单。有些语言将并发作为其最高目标，但是它们不那么可读，也没有那么多产。

性能和并发性是重要的属性，但不如简单性、可读性和生产率重要。

### 1.1 简单

> 简单性是可靠性的前提。—埃格·迪克斯特拉（Essger W. Dijkstra）

为什么我们要追求简单？为什么Go程序必须简单很重要？

我们一直处于您说“我听不懂这段代码”的情况，是吗？我们都在开发那些害怕更改的程序，因为您担心它会破坏程序的另一部分。您不了解和不知道如何解决的部分。这很复杂。

> 构建软件设计的方法有两种：一种方法是使其简单到显然没有缺陷，另一种方法是使它变得如此复杂而没有明显的缺陷。第一种方法要困难得多。— CAR Hoare

复杂性将可靠的软件变成了不可靠的软件。复杂性是杀死软件项目的原因。因此，简单是Go的最高目标。无论我们编写什么程序，我们都应该能够同意它们很简单。

### 1.2 可读性

可读性是能够了解程序正在执行的操作的关键。如果您不了解程序在做什么，您如何希望对其进行维护？如果无法维护软件，则将其重写；否则，将被重写。那可能是您的公司最后一次投资Go了。

如果您是为自己编写程序，则可能只需要运行一次，或者您是唯一会看到该程序的人，然后为您工作。但是，如果这是一个将由多个人贡献的软件，或者将由人们使用足够长的时间以使需求，功能或它所运行的环境不断变化，那么您的目标必须是针对您的程序易于维护。

编写可维护代码的第一步是确保代码可读。

### 1.3 生产率

> 设计是安排代码在今天工作的艺术，并且永远是可变的

我要强调的最后一个基本原则是生产力。这里归结为：你花了多少时间做有用的工作，复用可以复用的轮子，而不是绝望地迷失在陌生的外部代码库中。

开玩笑说，Go 是在等待 C++ 程序编译时设计的。快速编译是 Go 的一个关键特性，也是吸引新开发人员的关键特点。虽然编译速度仍然是一个永恒的战场，但可以说在其他语言中需要几分钟的编译，在 Go 中只需几秒钟。这有助于 Go 开发人员与使用动态语言工作的同行一样高效，而没有这些语言固有的可靠性问题。

对于开发人员生产力问题更重要的是，Go 程序员意识到编写代码是为了阅读，因此将阅读代码的行为置于编写代码的行为之上。Go 甚至通过工具和自定义来强制所有代码以特定样式进行格式化。这消除了学习项目特定编码方式的摩擦，并有助于发现错误，因为它们 看起来不正确。

Go 程序员不会花几天时间调试难以理解的编译错误。他们不会在复杂的构建脚本或将代码部署到生产上浪费时间。最重要的是，他们不会花时间试图理解他们的同事所写的内容。

## 2. 标识符

### 2.1 标识符为了简洁，而非简短

好的名称要

* 简洁。不是最短，但是要高信噪比
* 有描述性
* 有可预见性

### 2.2 标示符长度

> 名称的声明与其使用之间的距离越大，名称应越长

名称长度取决于变量 / 函数存活时间、上下文距离。for循环里可以用i而不是index代表在slice里面的位置，但是函数的参数使用 pageIndex一定比 p 要好很多。

### 2.3 不要根据类型为变量命名

比如 UserMap =&gt; Users，taskChannel =&gt; tasks

### 2.4 采用一致的命名风格

推荐使用驼峰式 CamelName，避免下划线 Snake\_case；user的id如果叫做userID，就不要再使用UID、operatorID等名字了。 数据库一般叫做db，某些单字母变量传统上与循环和计数相关联。例如，i、j和k通常是简单for循环的循环归纳变量。 n通常与计数器或累加器相关联。 v是通用编码函数中值的常用简写，k通常用于映射的键，并且s经常用作类型参数的简写string。

### 2.5 使用一致的声明

* 声明和初始化时，使用:=. 
* 在声明但不初始化变量时，使用var. 
* chan、map和slice初始化使用make
* 没事儿就不要用new了

## **3. 注释**

> 好的代码有很多注释，坏的代码需要很多注释

注释对于 Go 程序的可读性非常重要。每条评论都应该做三件事中的一件——而且只做一件：

* 评论应该解释这件事的作用。\(非常适合评论public 方法\)
* 注释应解释如何做事。（非常适合方法内的注释）
* 评论应该解释为什么事情是这样的（非常适合方法内的某种特殊处理逻辑）

### 3.1 变量和常量的注释应该描述它们的内容而不是它们的目的

```go
const randomNumber = 6 // 根据无偏骰子确定，而不是：这个数用来产生随机数
```

### 3.2 方法应该有注释

* 在编写函数之前，请编写描述函数的注释。如果您发现写注释很困难，则表明您将要编写的代码将难以理解。
* 函数应该只做一件事。如果您发现自己注释了一段代码，因为它与函数的其余部分无关，请考虑将其提取到自己的函数中。
* 一句话描述函数的作用，再把它作为函数的名称：除了更容易理解之外，更小的函数更容易单独测试。一旦您将正交代码隔离到它自己的函数中，它的名称可能就是所需的所有文档。

## 4. 包设计

> 编写害羞的代码 - 模块不会向其他模块透露任何不必要的内容，并且不依赖其他模块的实现。

Go的编程哲学推崇少层级、比较大的包，代码高内聚、低耦合

### 4.1 好的包名，不要抽象

* 用它提供的东西命名你的包，而不是它包含的东西。比如“这个包让你使用HTTP” 而不是 “这个包提供HTTPRequest”
* 避免base, common, helper或 util这样的包名。宁可使用一点冗余，好过引入共同的依赖，或者合并为一个包。例如，net/http包没有client和server子包，而是有一个client.go和server.go文件，每个文件都保存各自的类型，以及一个transport.go用于公共消息传输代码的文件。

### **4.2 早return**

由于 Go 不使用异常进行控制流，遇到错误尽早返回，减少if-else嵌套

### **4.3 给零值含义**

* 值的类型决定了值的零值；对于数字类型它为零，对于string是“”，对于指针类型 是 nil，对于切片、映射和通道也是如此。
* sync.Mutex包含两个未导出的整数字段，表示互斥锁的内部状态。被声明时，这些字段将被设置为 0 ，使该类型无需显式初始化即可使用。
* 最好给struct 初始化状态一个默认的行为

  **4.4 避免包级别状态**

  编写可维护程序的关键是它们应该是松散耦合的——对一个包的更改不应该影响另一个不直接依赖第一个包的包

  Go中有两种很好的实现松耦合的方法

* 使用接口来描述您的函数或方法所需的行为。
* 避免使用全局状态

可变全局状态会在程序的独立部分之间引入紧密耦合，因为全局变量成为程序中每个函数的**不可见参数！**

如果要减少全局变量创建的耦合，

* 将相关变量作为字段移动到需要它们的结构上。
* 使用接口来减少行为和该行为的实现之间的耦合。

  \*\*\*\*

## **5. 项目结构**

### **5.1 考虑少而大的包**

Go 没有提供建立可见性的详细方法。缺乏Java的public，protected，private，和隐式default访问修饰符。没有等同于 C++ 的friend类概念。只有两个访问修饰符，public 和 private，由标识符的第一个字母的大写表示。

鉴于可用于控制对包符号的访问的控件有限，Go使用更少、更大的包。您的默认位置应该是不创建新包，而是为你的包创建一个广泛、浅薄的 API 表面。

> 如果您具有 Java 或 C\# 背景，请考虑此经验法则。- Java 包相当于单个.go源文件。- Go 包相当于整个 Maven 模块或 .NET 程序集。

什么时候应该把一个.go文件分成多个？何时应该考虑合并.go文件？

* 用一个.go文件开始每个包。为该文件指定与文件夹名称相同的名称。例如。package http应放置在http.go名为http.
* 随着包的增长，您可能决定将各种职责拆分为不同的文件。例如，messages.go包含the \`Request和Response类型，client.go包含Client类型，server.go包含Server类型。
* 如果您发现您的文件具有类似的import声明，请考虑合并它们。或者，确定导入集之间的差异并移动它们
* 不同的文件应该负责包的不同区域。messages.go可以负责请求和响应和关闭网络HTTP的编组，http.go可以含有低水平的网络处理逻辑，client.go和server.go执行要求的结构或路由的HTTP业务逻辑，等等。

> 源文件名首选名词。 使用internal包来减少你的公共 API

### 5.2 包设计原则

1. 考虑一个go文件启动包
2. 优先包内部测试（test文件）
3. package main 尽可能小：
   1. func main\(\) 应该解析标志，打开与数据库、记录器等的连接，然后将执行移交给高级对象。

      应该将尽可能多的业务逻辑移出主函数，最好是移出主包

> Go 包的层次结构没有意义，如同 net/http不是net的子包

## 6.API设计

* 函数参数避免次序（具有多个相同类型参数的 API 很难正确使用）

```go
type Source string
var from Source = "presentation.md"
from.CopyTo("/tmp/backup") // 比 CopyFile("/tmp/backup", "presentation.md") 好
```

* 最少参数

  推荐参数不为nil，为nil设置一个少参数的Default函数

```go
http.ListenAndServe("0.0.0.0:8080", http.DefaultServeMux) // 好于http.ListenAndServe("0.0.0.0:8080", nil)
```

* 普通参数 + 可变参数，避免\[\] var

```go
func anyPositive(first int, rest ...int) bool // 语义为“至少一个参数”
func anyPositive(inputs []int) bool // 语义为“一个,或多个参数”
```

* 接口隔离 + 最少职责 + 通用型

```go
// Save 将 doc 的内容写入文件 f. 
func Save(f *os.File, doc *Document)             // 错误，依赖*os.File和*Document类型
func Save(rwc io.ReadWriteCloser, doc *Document) // 错误，依赖*Document类型，以及不需要的rwc的 Read方法
func Save(wc io.WriteCloser, doc *Document)      // 错误，依赖*Document类型
func Save(w io.Writer）                 // 正确，只依赖一个可写的东西io.Writer，让*Document去实现Write()方法。
```

## 7. 错误处理

### 7.1 通过消除错误, 消除错误处理

```go
_, err = br.ReadString('\n')
    lines++
    if err != nil {
        break
    }
```

检查错误之前增加行数——这看起来很奇怪。ReadString如果在遇到换行符之前遇到文件结尾和文件结尾，将返回一个错误。如果文件中没有最后的换行符，就会发生这种情况。需要检查错误是否不是 io.EOF 优化： 面临着和正常逻辑纠缠的的错误处理时，请尝试将一些操作提取到辅助类型中

```go
func CountLines(r io.Reader) (int, error) {
    sc := bufio.NewScanner(r)
    lines := 0

    for sc.Scan() { // Scan在遇到第一个err时停止，并存储这个error
        lines++
    }
    return lines, sc.Err()
}
```

### 7.2 只处理一次错误

* fmt.Errorf模式：适用于注释错误消息，但代价是掩盖了原始错误的类型
* 用 github.com/pkg/errors 包装错误

## **8. 并发**

### **8.1 自己做工作**

如果你的 goroutine 在从另一个 goroutine 得到结果之前无法取得进展，通常自己做工作比委托它更简单。

### **8.2 将异步执行权给调用者**

将异步执行函数的决定留给该函数的调用者通常更容易。

```go
// ListDirectory 返回目录的内容。
func ListDirectory(dir string) ([]string, error) // 对大量目录，需要等到全部load后才能返回
func ListDirectory(dir string) chan string // 启动一个 goroutine 来填充chan，无法识别错误/正常关闭，无法停止goroutine
func ListDirectory(dir string, fn func(string)) // 提供回调函数，该函数在执行时在每个目录条目的上下文中调用
```

### **8.3 不要在不知道何时停止的情况下启动 goroutine**

```go
func main() {
    go serveDebug()
    go serveApp()
    select {}
} // serveDebug或serveApp失败后无法返回
```

通过chan解决这个问题

```go
func main() {
    done := make(chan error, 2)
    go func() {
        done <- serveDebug()
    }()
    go func() {
        done <- serveApp()
    }()

    for i := 0; i < cap(done); i++ {
        if err := <-done; err != nil {
            fmt.Println("error: %v", err)
        }
    }
}
```

多个goroutine优雅退出，考虑第三方包 [https://github.com/heptio/workgroup](https://github.com/heptio/workgroup)

