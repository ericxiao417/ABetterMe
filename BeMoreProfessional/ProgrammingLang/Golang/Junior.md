# 系统参数

**GOPATH:** 是Go语言中使用的一个环境变量，它使用绝对路径提供项目的工作目录。【目前已经弃用，因为加入了go module】



**GOROOT:** 表示Go开发包的安装目录。



**go: command not found:**



- 错误原因：当前执行的程序在当前目录下如果不存在，windows则会在系统中已有的名为path的环境变量制定的目录中查找。如果仍未找到，就会报以上错误。所以进入到go的安装目录下，进入bin，执行go，会看到go的参数提示信息。

- 解决方法：将Go的安装目录路径添加到path中，这样在任何目录下都可以执行go。



**GO MODULE:** 是为了提升使用其他开发者的代码，即添加xx依赖包和模块的体验。在Go支持GO MODULE之后，编译时编译器会从工作目录（当前所在目录）开始逐级向上查找是否有go.mod文件。

使用：

```go
go mod init: 创建一个新模块，初始化go.mod文件
go get: 更改依赖项目版本，或者添加新的依赖项
go list -m all: 打印当前模块依赖
go mod tidy: 移除无用依赖
```

# 基本语法

Go语言是静态语言，因此变量是有明确类型的。

Python是动态语言，因此变量没有明确类型。

## 变量

Go语言的基本类型：

- bool
- string
- int, int8, int16, int32, int64
- uint, uint8, uint16, uint32, uint64, uintptr
- byte //uint8的别名
- rune //int32的别名，代表一个unicode码
- float32, float64
- complex64, complex128

**当一个变量被声明后，系统自动赋给它该类型的零值。**

- int: 0
- float: 0.0
- bool: false
- string:  ""
- 指针：nil

所有的内存在go中都是经过初始化的。

```go
var name type

var (
	a int
  b string
  c []float32
  d func() bool
  e struct{
    x int
  }
)

//简短模式，定义变量，同时显式初始化，只能用在函数内部，i，j必须是没有定义过的变量。
i, j := 0, 1

//变量值交换
a, b = b, a

//匿名变量，任何给匿名变量赋的值都会被抛弃。匿名变量不占用内存空间，不会分配内存。
a, _ := GetData()
```

### 字符类型

Go语言的字符类型有以下两种：

- 一种是uint8类型，或者叫做byte类型，代表了ASCII码的一个字符。
- 另一种是rune类型，代表UTF-8字符，当需要处理中文、日文或其他复合字符时，就要用rune类型。

==UTF-8和Unicode, ASCII的区别：==

ASCII是单字节编码，无法用来表示中文，中文编码至少需要2个字节。所以，中国制定了GB2312。但是世界上有很多其他语言，所以需要一种统一的编码。

Unicode把所有语言都统一到一套代码里，这样就不会有乱码问题了。

UTF-8是Unicode的升级版，是可变长编码。UTF-8可以把一个Unicode字符根据不同数字的大小，编码成1-6个字节。

在计算机内存中，统一使用Unicode编码。当需要保存到硬盘或者需要传输的时候，就转换成UTF-8编码。

### 类型转换

Go语言不存在隐式转换，所有的类型转换都必须显式的声明：valueOfTypeB = typeB(valueOfTypeA).

只有相同底层类型的变量之间才可以互相转换，例如将in16转换为int32。不同底层类型的变量相互转换会引发编译错误，例如将bool转换成int。

## 作用域

根据变量定义位置的不同，可以分为以下三个变量：

- 函数内部定义的变量叫做局部变量。局部变量不是一直都存在的，它只在定义它的函数被调用后存在，函数调用结束后这个局部参数就会被销毁。
- 函数外部定义的变量叫做全局变量。全局变量只需要在一个源文件中定义，就可以在所有源文件中使用。当然，不包含这个全局变量的源文件需要使用'import'来导入全局变量所在的源文件。
- 函数中定义的变量叫做形式参数。形式参数只会在函数调用时生效，函数调用结束后就会被销毁。在函数未被调用时，函数的形参并不占用内存，也没有实际值。

## 指针

指针是一种数据类型，就像整数，字符，切片等。一个指针变量可以指向任何一个值的内存地址，它所指向的值的内存地址在32和64位机器上分别占用4或8个字节，占用字节的大小与所指向的值的大小无关。当一个指针被定义后没有分配到任何变量时，它的默认值是nil。指针变量通常缩写为ptr。

每个变量在运行时都有一个内存地址，这个地址代表这个变量在内存中的位置。Go语言中使用在变量名前加==&==操作符来获取变量的内存地址，即**取地址操作**。格式为：

```go
ptr := &v //v的类型为T
```

**解释：**

v代表被取地址的变量，变量v的内存地址使用ptr来进行接收，ptr的类型为\*T, 叫做T类型的指针，\*代表指针。

变量、指针、地址的关系是：每个变量都有地址，指针的值，就是地址。

当使用&操作符对普通变量进行取地址操作并得到变量的指针后，可以对指针使用\*操作符，也就是指针取值。

```go
func main() {
  var house = "Malibu Point 100800, 90625"
  //对house进行取地址，ptr类型为*string
  ptr := &house
  fmt.Printf('ptr type : %T\n', ptr)
  //打印ptr的值
  fmt.Printf('address is : %p\n')
  //对ptr进行取值操作
  value := *ptr
  fmt.Printf('value type is : %T\n', value)
  fmt.Printf('value is : %sn', value)
}
```

取地址操作符&和取值操作符\*是一对互补操作。

## 变量逃逸分析

栈可用于内存分配，栈的分配和回收速度非常快。

堆在内存分配中，类似于往一个房间里摆放各种家具，有大有小。分配内存时，需要找一块足够装下家具的空间再摆放家具。经过反复摆放和腾空家具后，房间里的空间就会变得乱七八糟，此时再往这个房间摆放家具，就会发现，虽然有足够的空间，但是各个空间分布在不同的区域，没有一段连续的空间来摆放家具。此时，内存分配器就需要对这些空间进行调整优化。

变量逃逸分析，就是分析变量是分配到堆上还是栈上。用如下命令：go run -gcflags "-m -l" xxx.go

-gcflags 参数是编译参数，其中， -m表示进行内存分配分析，-l表示避免程序内联，也就是避免进行程序优化。

## 常量和const

```go
const pi = 3.1415926
const (
	a = 1
  b = 2
  c = 3
)
```

常量的值必须是能够在编译时就确定的，也就是固定不变的。

### iota常量生成器

常量声明可以用iota常量生成器初始化。它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式。在一个const声明语句中，在第一个声明的常量所在的行，iota将会被设置为0，然后在每一个有常量声明的行加1。

```go
type Weekday int

const (
	Sunday Weekday = iota
  Monday
  Tuesday
  Wednesday
  Thursday
  Friday
  Saturday
)
```

周日将被设置为0，周一为1，以此类推。

```go
//将NewInt定义为int类型
type NewInt int

//给int取一个别名叫做IntAlias
type IntAlias = int

func main() {
  //将a声明为NewInt类型
  var a NewInt
  fmt.Printf('a type is : %T\n', a)
   //将b声明为IntAlias类型
  var b IntAlias
  fmt.Printf('b type is : %T\n', b)
}
```

**输出结果：**

a type is : main.NewInt

b type is : int

==分析：==

1. 第2行将NewInt定义为int类型，它是一个新的类型，它本身依然具有int类型的特征。
2. 第5行给int起了一个别名叫做IntAlias, 它与int等效。

## 注释

单行注释：//

多行注释：/* ... */

# 语言容器

## 数组

```go
var a [3]int
```

默认情况下，数组的每个元素都会被初始化为元素类型的零值，对于数字来说就是0。

在数组的定义中，如果在数组长度的位置出现...省略号，则表示数组的长度是根据初始化值的个数来计算。

**比较两个数组：**数组类型包括数组的长度和元素的类型。只有这两个部分都相等，才能说两个数组相等。直接用==和!=来比较数组。

## 切片

切片slice是对数组的一个连续片段的引用，所以切片是一个引用类型。这个片段可以是整个数组，也可是是由起始和终止索引标识的一些项的子集，终止索引不包含在内，即前闭后开。

切片由三部分组成：地址、大小和容量。如果将数据比作切糕的话，切片就是你想要的那一块。切的过程包含从哪里开始，切多大，容量可以理解为装切糕的口袋的大小。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221225130819590.png" alt="image-20221225130819590" style="zoom:50%;" />

切片默认指向一片连续的内存区域，可以是数组，也可以是切片本身。

从连续内存区域生成切片是常见的操作，如：slice[start:end]。slice就是切糕对象，start是开始，end是结束，且end本身不包含在内。

```go
var a = [3]int{1, 2, 3}
fmt.Println(a, a[1:2])
******************************
output: 【1， 2， 3】， 【2】
```

从数组或者切片生成新的切片有如下特性：

1. 取出的元素数量：end - start
2. 取出元素不包括end，切片最后一个元素用slice[len(slice)]来获取。
3. 当start缺省时，表示从0开始。
4. 当end缺省时，表示到最后。
5. 当start和end同时缺省时，与切片本身等效。
6. 两者同为0时，等效于空切片，用于重置切片，清空切片的所有元素。

**声明新切片：**

```go
var strList []string
```

与数组的声明不同的是，切片没有声明元素的个数。

切片是动态结构，只能与nil判定是否相等，无法判断两个切片是否相等。可以用append()向切片追加元素。

**动态的创建切片：**使用make()。

```go
make([]int, 2, 10)
```

2: 是size，就是切的这一块切糕的大小。

10：是cap，表示口袋的大小。

size指的是为这个类型分配多少个元素，cap为预分配的元素数量，这个值不影响size，只是能提前分配空间，降低多次分配空间造成的性能问题。

==使用make()生成切片，发生了一定的内存分配操作。但是，给定start和end的切片只是将新的切片结构指向已经分配好的内存，不会发生内存分配操作。==

### 切片的扩容

Go语言使用append()对切片动态添加元素。如果空间不足以容纳足够多的元素，切片就会进行扩容，此时新切片的长度会发生变化。切片在扩容时，容量的扩展规律是按容量的2倍进行扩充。

切片扩容就类似于公司搬家。公司创建初期，只需要很小的空间就可以足够容纳所有的东西。随着业务的拓展，东西越来越多，原来的小空间容纳不下，公司只能搬家，每次搬家就要把所有的东西转移到新的办公点。

- 公司的东西就是切片中的元素。
- 办公地就是分配好的内存地址。
- 搬家就是重新分配内存地址。
- 无论搬多少次家，公司名不会变，代表外部使用切片的变量名不会修改。
- 由于搬家后地址发生变化，因此内存地址也发生了变化。

### 切片的copy()

Go语言内置的copy()函数可以将一个切片复制到另一个切片中，如果加入的两个切片不一样大，就会按照较小的那个切片的元素进行复制。

**copy(destSlice, srcSlice []T)int**

将srcSlice复制到destSlice。

```go
slice1 := []in{1, 2, 3, 4, 5}
slice2 := []int{5, 4, 3}
copy(slice2, slice1)
copy(slice1, slice2)
**********************************
output: 
slice1: [1, 2, 3, 4, 5]
slice2: [1, 2, 3]
```

### 切片删除元素

```go
a := []int{1, 2, 3, 4}
//删除第一个元素
a = a[1:]
//删除开头N个元素
a = a[N:]

//删除中间元素
a = []int{1, 2, 3}
a = append(a[:i], a[i+1:]...)
a = append(a[:i], a[i+N:]...)

//删除尾部元素
a := []int{1, 2, 3}
a = a[:len(a) - 1]
a = a[:len(a) - N]
```

Go语言中删除切片元素的本质，就是以被删除元素为分界点，将前后两个部分的内存重新连接起来。

### 遍历

用关键字range来遍历。

```go
a := []int{1, 2, 3, 4, 5}
for index, value := range a {
  fmt.Println(index, value)
}
```

当迭代切片时，关键字range会返回两个值，第一个值是当前的索引，第二个值是该索引对应元素值的一个副本。**需要强调的是，**range返回的是每个元素的副本，而不是直接对该元素的引用。

## Map

是go语言中的无序键值对的集合。在Python中叫字典，在java中叫hashmap。

var mapname map[keytype]valuetype

mapname := make(map[keytype]valuetype)

在声明的时候，不需要知道map的长度，因为map是可以动态增长的，未初始化的map的值是nil，使用len()可以获取键值对的数目。

当一个key对应多个value时，可以将value定义为某个类型的切片，这样就可以解决问题。

### 遍历map

遍历map时，得到的是无序的结果。如果要得到有序的结果，必须要**先排序**。例子如下：

```go
scene := make(map[string]int)

scene['route'] = 66
scene['brazil'] = 4
scene['china'] = 960

var sceneList []string

for key := range scene {
  sceneList = append(sceneList, key)
}

sort.Strings(sceneList)
fmt.Println(sceneList)
```

### 删除和清空元素

delete(map, key).

map为要删除的map实例，key为要删除的键。

Go语言并没有提供任何清空map的方法，唯一的办法就是make一个新的map。不用担心垃圾回收的效率，Go语言中的并行垃圾回收效率比写一个清空函数要高效的多。

## List

列表是一种非连续的存储容器，由多个节点组成，列表有多种实现方式，如单链表、双链表。在Go语言中，列表用container/list来实现，内部的实现原理是双链表，列表能高效的进行任意位置的元素插入和删除。

```go
l := list.New()
//尾部添加
l.PushBack('first')
//头部添加
l.PushFront(67)
//尾部添加元素后保存句柄
element := l.PushBack('fist')
//在fist之后添加high
l.InsrtAfter('high', element)
//在fist之前添加noon
l.InsertBefore('noon', element)
//使用l
l.Remove(element)
```

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20221230112554504.png" alt="image-20221230112554504" style="zoom:50%;" />

## nil

在Go语言中，布尔类型的零值是false，数值类型的是0，字符串类型的是"", 而指针、切片、map、通道、函数和接口都是nil。

特点：

- nil不可以比较
- nil不是关键字
- nil没有默认类型
- 不同类型的nil是不能比较的
- 不同类型nil的指针是一样的
- 两个相同类型的nil可能也无法比较
- nil是map，slice，pointer，channel，func，interface的零值

# 流程控制

## switch

Go语言改进了switch的语法设计，case与case之间是独立的模块，不需要通过break跳出当前case代码块以避免执行到下一行，如下：

```go
var a = 'hello'
switch a {
case 'hello':
  		fmt.Println(1)
case 'world':
  		fmt.Println(2)
default:
  		fmt.Println(3)
}
```

### 一分支多值

当出现多个case要放在一起的时候，可以如下：

```go
var a = 'num'
switch a {
  case 'mum', 'daddy':
  fmt.Println(1)
}
```

### 分支表达式

case后不仅仅是常量，也可以是表达式，如下：

```go
var r int = 11
swtich {
  case r > 7 && r < 10:
  fmt.Println(r)
}
```

### fallthrough

在Go语言中，case是一个独立的代码块，执行完毕后不会再执行下一个case代码块，但是为了兼容一些移植代码，还是加入了fallthrough这个关键字，如下：

```go
var s = 'hello'
switch {
  case s == 'hello':
    fmt.Println('hello')
    fallthrough
  case s != 'hello':
  	fmt.Println('world')
}
```

output：hello world

## goto

Go语言中goto语句通过标签进行代码见的无条件跳转。

```go
func main() {
  for x := 0; x < 10; x++ {
    for y := 0; y < 10; y++ {
      if y == 2 {
        goto breakHere
      }
    }
  }
  breakHere:
  fmt.Println('done')
}
```

# 函数

在Go语言中，函数也是一种数据类型，和其他类型的数据类型一样可以保存在变量中，如下：

```go
func fire() {
    fmt.Println('fire')
}

func main() {
    var f func()
    f = fire
    f()
}
```

Go语言支持普通函数、匿名函数、闭包。Go语言的函数是**一等公民**，也就是说：

- 函数本身可以作为值进行传递。
- 支持匿名函数和闭包。
- 函数可以满足接口。

Go语言里有三种类型的函数：普通的带有名字的函数、匿名函数或者lambda函数、方法。

## 普通函数

```go
func hypot(x, y float64) float64 {
  return math.Squre(x*x + y*y)
}
fmt.Println(hypot(3, 4))
```

x和y是形参，3和4是调用函数时传入的实参。

## 匿名函数

匿名函数的定义格式如下：

```go
func(参数列表)(返回参数列表) {
    函数体
}
```

### 在定义时调用匿名函数

```go
func(data int) {
    fmt.Println('hello', data)
}(100)
```

以上代码表示对匿名函数进行调用，传递参数 100。

### 将匿名函数赋值给变量

```go
f := func(data int) {
    fmt.Println('hello', data)
}
f(100)
```

### 匿名函数用作回调函数

```go
//遍历切片的每个元素，通过给定的函数进行元素访问
func visit(list []int, f func(int)) {
    for _, v := range list {
        f(v)
    }
}

func main() {
    //使用匿名函数打印切片内容
    visit([]int{1, 2, 3, 4}, func(v int) {
        fmt.Println(v)
    })
}
```

### 回调函数

编程分为两类：系统编程(system programming)和应用编程(application programming)。所谓系统编程，简单来说，就是编写库；而应用编程就是利用写好的各种库来编写具有某种功能的程序，也就是应用。系统程序员会给自己写的库留下一些接口API，以供应用程序员使用。所以在抽象层的图示里，库位于应用的底下。



当程序跑起来时，一般情况下，应用程序会时常通过API调用库里预先准备好的函数。但是有些库函数library function，却要求应用先传给他一个函数，好在合适的时候调用，以完成目标任务。这个被传入的、后又被调用的函数，就叫作回调函数callback function。



For example, 有一家旅馆提供叫醒服务，但是要求旅客自己决定叫醒的方式，比如打电话，敲门。这里，“叫醒”这个行为是旅馆提供的，相当于库函数。但是叫醒的方式是由旅客决定并告诉旅馆的，也就是回调函数。而旅客告诉旅馆怎么叫醒的动作，也就是把回调函数传入库函数的动作，称为登记回调函数to register a callback function。

reference: https://www.zhihu.com/question/19801131

### 使用匿名函数实现操作封装

TODO



## 闭包closure

闭包就是引用了自由变量的函数，被引用的自由变量和函数一同存在，即使离开了自由变量的环境也不会被释放或者删除，在闭包中可以继续使用这个自由变量，因此，函数 + 引用环境 = 闭包。

### 什么是闭包

```go
//case 1
func foo1(x *int) func() {
    return func() {
        *x = *x + 1
        fmt.Printf('foo1 val = %d\n', *x)
    }
}

//case 2
func foo2(x int) func() {
    return func() {
        x = x + 1
        fmt.Printf('foo2 val = %d\n', x)
    }
}

//case 3
func foo3() {
    values := []int{1, 2, 3, 5}
    for _, val := range values {
        fmt.Printf('foo3 val = %d\n', val)
    }
}

//case 4
func show(v interface{}) {
    fmt.Printf('foo4 val = %v\n', v)
}
func foo4() {
    values := []int{1, 2, 3, 5}
    for _, val := range values {
        go show(val)
    }
}

//case 5
func foo5() {
    values := []int{1, 2, 3, 5}
    for _, val := range values {
        go func() {
            fmt.Printf('foo5 val = %v\n', val)
        }()
    }
}

//case 6
var foo6Chan = make(chan int, 10)
func foo6() {
    for val := range foo6Chan {
        go func() {
            fmt.Printf('foo6 val = %d\n', val)
        }()
    }
}

//case 7
func foo7(x int) []func() {
    var fs []func()
    values := []int{1, 2, 3, 5}
    for _, val := range values {
        fs = append(fs, fucn() {
            fmt.Printf('foo7 val = %d\n', x + val)
        })
    }
    return fs
}
```

#### 值传递

Go没有引用传递，只有值传递。即使foo1()在参数上加了*，依旧是值传递，只不过传递的是指针的数值。

如下图：

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/202301021102578.png" alt="image-20230102110205518" style="zoom: 50%;" />

我们的目的是传递x变量，首先，创建了一个传参地址（临时地址变量），它存放了x的地址值，调用函数的时候给它这个传参地址，函数呢，则会创建一个入参地址，是传参地址的一份拷贝。函数拿到了这个地址值，可以通过寻址拿到这个x变量，此时函数如果直接修改了x变量，可以认为是‘本地修改’或者‘永久修改’了这个变量的值。

举个例子：一个叫做‘函数’的人想找一个叫做‘x’的人，函数跑过来问认识x的我，我拿出地址簿，给他出示了x这个人的家庭地址，函数记性不太好，所以拿了一个本子把x的家庭地址记在了他的本子上。其中，我的记录着x家庭地址的地址簿，就是传参地址。函数抄录了x的家庭地址的本子，就是入参地址。x的家庭地址，就对应了x变量的地址值。



**闭包 = 函数 + 环境**



**函数，** 指的是在闭包实现的时候，往往通过调用一个外部函数返回其内部函数来实现的。内部函数，可能是内部实名函数，匿名函数或者一段lambda表达式。用户得到了一个闭包，也等同于得到了这个内部函数，每次执行闭包就等同于执行内部函数。

**环境，** 指的是，如果外部函数的所有变量可见性都是local的，即生命周期在外部函数结束时也结束的，那么闭包的环境也是封闭的。相反，那么闭包不再封闭，全局可见的变量的修改，也会对闭包内的这个变量产生影响。



<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/202301021109748.png" alt="image-20230102110911690" style="zoom: 67%;" />



第一组实验：

```go
x := 133
f1 := foo1(&x)
f2 := foo2(x)
f1()
f2()
f1()
f2()
```

定义了x=133之后，我们获取到了f1=foo1(&x)和f2=foo2(x)。这里，f1和f2就是闭包的函数，也就是foo1()和foo2()的内部匿名函数；而闭包的环境即外部函数foo1()和foo2()的变量x，因为内部匿名函数只引用到了x，所以这里简化为x。



闭包的函数做的事情归纳为：1. 将环境的变量x + 1。2. 打印环境变量x。

闭包的环境则是其外部函数获取到的变量x。



因此，第一组实验的结果是：

```go
f1() // foo1 val = 134
f2() // foo2 val = 134
f1() // foo1 val = 135
f2() // foo2 val = 135
```

第二组实验：

```go
x = 233
f1() // foo1 val = 234
f2() // foo2 val = 136
f1() // foo1 val = 235
f2() // foo2 val = 137
```

Ref: https://zhuanlan.zhihu.com/p/92634505





### 在闭包内部修改引用的变量

闭包对它作用域上部的变量可以进行修改，修改引用的变量会对变量进行实际修改，如下：

```go
str := 'hello world'
//创建一个匿名函数
foo := func() {
    str = 'hello dude'
}

foo()
```

**分析：** 在匿名函数中并没有定义str，str的定义在匿名函数之前，此时，str就被引用到了匿名函数中形成了闭包。上面代码输出：hello dude。

# 函数参数

## go的参数传递

### 常规传递

使用普通变量作为函数参数的时候，在参数传递的时候，只是对变量值的拷贝，即将实参的值赋值给变参，当函数对变参进行处理时，并不会影响原来实参的值。

### 指针参数

函数的参数也可以是指针变量。使用指针变量作参数时，是将实参的内存地址赋值给变参，这时变参的修改也会影响到实参的值。

### 数组元素作为参数

使用数组元素作为参数时，其使用的方法和普通变量一样，也是值拷贝。

```go
func aa(a int) {
    a += 100
    fmt.Printf('打印内部的累加后的值是：%d\n', a)
}

func main() {
    var s = []int{1, 2, 3, 4, 5, 6}
    aa(s[3])
    fmt.Println(s[3])
}
*************************************************
output: 4
```

### 数组名作为参数

Go在将数组名字作为函数参数时，参数传递是对数组的复制，在形参中对数组元素的修改不会影响实参数组元素的原来的值。

```GO
func aa(s [6]int) {
    s[3] += 100
}

func main() {
    var s = []int{1, 2, 3, 4, 5, 6}
    aa(s)
    fmt.Println(s)
}
*********************************************
output: 1, 2, 3, 4, 5, 6
```

### slice作为参数

在使用slice作为参数时，进行参数传递时是一个地址拷贝，即将底层数组的内存地址复制给参数slice，对slice元素的操作就是对底层数组的操作。

```go
func aa(s1 []int) {
    s1[0] += 100
}

func main() {
    var a = [5]int{1, 2, 3, 4, 5}
    var s []int = a[0:]
    aa(s)
    fmt.Println(s)
}

output: 101, 2, 3, 4, 5
```

## #小结

Go语言中所有的传递都是值传递，都是拷贝一个副本。因为拷贝的内容有时候是非引用类型（int, string, struct等），这样就在函数中无法修改原来的内容。有的是引用类型（指针，map，slice，chan等），这样就可以修改原内容数据。

## 可变参数

可变参数是指函数传入的参数个数是可变的，为了做到这点，首先需要将函数定义为可以接受可变参数的类型：

```go
func myFunc(args ...int) {
    for _, arg := range args {
        fmt.Println(arg)
    }
}
```

可变参数必须是函数的最后一个参数，它是一个语法糖。

可变参数是一个切片。

### 任意类型的可变参数

```go
func myFunc(args ...interface{}) {
    //...
}
```

用interface{}来传递任意类型的数据是Go语言的管理用法，使用interface{}仍然是类型安全的。

## defer

当有多个defer行为被注册时，他们会以逆序，类似于栈的顺序执行。

# struct

Go语言可以通过自定义的方式形成新的数据类型，结构体就是这些类型中的一个复合类型。

```go
type a struct {
    age int
    length int
    sex string
}
```

结构体的定义只是一种内存布局的描述，只有当结构体实例化时，才会真正的分配内存。

## 实例化

