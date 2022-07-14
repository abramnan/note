# go语言相关概念

* **简短变量声明（:=）**

  简短变量声明包含两个步骤：声明和初始化（赋值），因为包含生命，所以同名情况下会把上面的覆盖掉。

  :=是一个变量声明语句，被称作声明

```go
in,err := os.Open(infile)   //1
out,err := os.Create(outfile)   //2

//上述这个err在1中已经声明了，所以在2中只有赋值操作
//简短变量声明中必须至少要声明一个变量，所以单个变量时，比如y:=10,那么y在前面一定没有出现过
```

* **指针**

  指针实际上就是对应变量的别名

```go
x := 1
p := &x         // p, of type *int, points to x
fmt.Println(*p) // "1"
*p = 2          // equivalent to x = 2
fmt.Println(x)  // "2"
```

​       p是指针，指向变量x的地址，*p和x基本等价，是x的别名

* **声明**

  变量声明的时，自动初始化为对应类型的默认值（0，false，nil等）

  数组/结构体类型对应的零值是**每个元素或者字段**对应该类型的零值

  接口和引用类型（Slice，指针，map，chan和函数）是nil

  ⚠️ go中数组比较特殊，声明的时候后每个元素都有值。

  ​	？？也就是说数组生命时就会赋予空间了？

  ​	？？那二维数组会是什么样的

* **new函数**

  new是一个预定义的函数（内置的函数）**不是关键字**，可以被覆盖

  go中new使用的比较少（可能是它不是关键字的原因，go的目标尽量减少关键字）

  所以代码中出现自定义的new时，这个new就不能用了（变量名/函数名）都可以

* **类型**

  一个类型声明语句创建了一个新的类型名称，和现有类型具有相同的底层结构。新命名的类型提供了一个方法，用来分隔不同概念的类型，这样即使它们底层类型相同也是不兼容的。

  ```Go
  type 类型名字 底层类型
  ```

  **作用体现在go中不同类型的数据之间是不能比较和运算的**

  例：

  ```Go
  // Package tempconv performs Celsius and Fahrenheit temperature computations.
  package tempconv
  
  import "fmt"
  
  type Celsius float64    // 摄氏温度
  type Fahrenheit float64 // 华氏温度
  
  const (
      AbsoluteZeroC Celsius = -273.15 // 绝对零度
      FreezingC     Celsius = 0       // 结冰点温度
      BoilingC      Celsius = 100     // 沸水温度
  )
  
  func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
  
  func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
  ```

  我们在这个包声明了两种类型：Celsius和Fahrenheit分别对应不同的温度单位。它们虽然有着相同的底层类型float64，但是它们是不同的数据类型，因此<font color = 'orange'>**它们不可以被相互比较或混在一个表达式运算**</font>。刻意区分类型，可以避免一些像无意中使用不同单位的温度混合计算导致的错误；因此需要一个类似Celsius(t)或Fahrenheit(t)形式的显式转型操作才能将float64转为对应的类型。Celsius(t)和Fahrenheit(t)是类型转换操作，它们并不是函数调用。类型转换不会改变值本身，但是会使它们的语义发生变化。另一方面，CToF和FToC两个函数则是对不同温度单位下的温度进行换算，它们会返回不同的值。

  对于每一个类型T，都有一个对应的类型转换操作T(x)，用于将x转为T类型（译注：如果T是指针类型，可能会需要用小括弧包装T，比如`(*int)(0)`）。只有当两个类型的底层基础类型相同时，才允许这种转型操作，或者是两者都是指向相同底层结构的指针类型，这些转换只改变类型而不会影响值本身。如果x是可以赋值给T类型的值，那么x必然也可以被转为T类型，但是一般没有这个必要。

  数值类型之间的转型也是允许的，并且在字符串和一些特定类型的slice之间也是可以转换的。这类转换可能改变值的表现。例如，将一个浮点数转为整数将丢弃小数部分，将一个字符串转为`[]byte`类型的slice将拷贝一个字符串数据的副本。在任何情况下，运行时不会发生转换失败的错误（译注: 错误只会发生在编译阶段）。

  底层数据类型决定了内部结构和表达方式，也决定是否可以像底层类型一样对内置运算符的支持。这意味着，Celsius和Fahrenheit类型的算术运算行为和底层的float64类型是一样的，正如我们所期望的那样。

* **常量**

  https://books.studygolang.com/gopl-zh/ch3/ch3-06.html

* **局部变量逃逸**

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
// f函数里的x变量必须在堆上分配，因为它在函数退出后依然可以通过包一级的global变量找到，虽然它是在函数内部定义的；用Go语言的术语说，这个x局部变量从函数f中逃逸了。相反，当g函数返回时，变量*y将是不可达的，也就是说可以马上被回收的。因此，*y并没有从函数g中逃逸，编译器可以选择在栈上分配*y的存储空间（译注：也可以选择在堆上分配，然后由Go语言的GC回收这个变量的内存空间），虽然这里用的是new方式。其实在任何时候，你并不需为了编写正确的代码而要考虑变量的逃逸行为，要记住的是，逃逸的变量需要额外分配内存，同时对性能的优化可能会产生细微的影响。
```

* **自增和自减**

  自增和自减是语句不是表达式（即x=i++是错的，于java的不同）

* **作用域**

  作用域和生命周期不一样，作用域是编译时期的概念，生命周期是运行时的概念

  经典bug

  ```Go
  var cwd string
  
  func init() {
      cwd, err := os.Getwd() // compile error: unused: cwd
      if err != nil {
          log.Fatalf("os.Getwd failed: %v", err)
      }
  }
  ```

  虽然cwd在外部已经声明过，但是`:=`语句还是将cwd和err重新声明为新的局部变量。因为内部声明的cwd将屏蔽外部的声明，因此上面的代码并不会正确更新包级声明的cwd变量。

  由于当前的编译器会检测到局部声明的cwd并没有使用，然后报告这可能是一个错误，但是这种检测并不可靠。因为一些小的代码变更，例如增加一个局部cwd的打印语句，就可能导致这种检测失效。

  ```Go
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

  有许多方式可以避免出现类似潜在的问题。最直接的方法是通过单独声明err变量，来避免使用`:=`的简短声明方式：

  ```Go
  var cwd string
  
  func init() {
      var err error
      cwd, err = os.Getwd()
      if err != nil {
          log.Fatalf("os.Getwd failed: %v", err)
      }
  }
  ```

* **数组**

  （可称为数值类型，因为可以通过==进行比较）

  数组的长度是数组类型的一个组成部分，因此[3]int和[4]int是两种不同的数组类型。数组的长度必须是常量表达式，因为数组的长度需要在编译阶段确定。

  数组的类型可以理解为是**[2]int**这样的，所以[2]int 和 [3]int 虽然都是整型数组，但是具体类型是不一样的，所以用==等比较运算符会报编译错误。

  如果一个数组的元素类型是可以相互比较的，那么数组类型也是可以相互比较的，这时候我们可以直接通过==比较运算符来比较两个数组，只有当两个数组的所有元素都是相等的时候数组才是相等的。不相等比较运算符!=遵循同样的规则。

  ```Go
  a := [2]int{1, 2}
  b := [...]int{1, 2}
  c := [2]int{1, 3}
  fmt.Println(a == b, a == c, b == c) // "true false false"
  d := [3]int{1, 2}
  fmt.Println(a == d) // compile error: cannot compare [2]int == [3]int
  ```

  

* **数组指针和指针数组**

  * 数组指针是**一个**指向数组的指针

  ```go
  // 语法：var arrPtr *[size] Type
  var arrPtr * [4]int        // 创建一个指针 arrPtr，指向一个数组
  var arr = [4]int{1,2,3,4}  // 创建一个数组并初始化
  arrPtr = &arr              // 将数组 arr的地址赋值给arrPtr
  fmt.Println("将 arr 的内存地址赋值给数组指针 arrPtr,   arrPtr=",ptr)
  
  ```

  > **通过指针访问数组**
  >
  > 访问数组的元素可以通过下标来访问，比如：arr [0] 即可访问数组 arr 的第一个元素。
  >
  > 但是我们学习了指针数组，所以尝试使用指针数组来访问元素内容，在 Golang 中，取地址的操作为 * (寻址运算符) 。
  >
  > 因此，我们先取存储的地址 *arrPtr，访问到数组 ，然后再下标取值 *arrPt[0]
  >
  > 代码如下：*arrPtr[0]
  > 实际上，这段代码编译就会报错，因为在 Golang 中 * 寻址运算符和 [] 中括号运算符的优先级是不同的！
  >
  > [] 中括号是初等运算符
  >
  > 寻址运算符是单目运算符
  > 初等运算符的优先级是大于单目运算符的，因此先参与计算的是 arrPtr[0]，arrPtr[0] 其实就是数组的第一个元素，就是数字 1。
  > 数字 1 必然是 int 类型，而不是一个地址，因此针对数字 1 使用 * 寻址运算符自然也就发生了错误。
  >
  > 解决问题的办法很简单，就是添加一个小括号，更改运算优先级即可：(*arrPtr)[0]

  <font color = 'orange'>不过因为 * 在 Golang 中，建立了 arrPtr := &arr 这种类似地址关系后，* 允许不写。所以，访问时候可以直接写成 arrPtr[0]。事实上在工作开发过程中，这种写法反而更加常见**。</font>实战代码：

  ```go
  fmt.Println("(*arrPtr)[0] 通过指针访问数组的第一个元素：", (*arrPtr)[0])
  fmt.Println("arrPtr[0] 通过指针访问数组的第一个元素：", arrPtr[0])
  //输出：
  //(*arrPtr)[0] 通过指针访问数组的第一个元素： 1
  //arrPtr[0] 通过指针访问数组的第一个元素： 1**注意：仅对访问下标时，寻址运算符允许不写！**
  ```

  **注意：仅对访问下标时，寻址运算符允许不写！**

  

  * 指针数组是一个由指针组成的数组，一堆指针

  ```go
  // 语法：var ptrArr [size] *Type
  var ptrArr [4]*int  
  a, b, c, d := 1, 2, 3, 4
  
  arr2 := [4]int{a, b, c, d}  // 拷贝四个变量的值为函数组元素
  fmt.Println("数组 arr2 :", arr2)
  
  ptrArr = [4]*int{&a, &b, &c, &d} // 存的都是内存地址
  fmt.Println("指针数组 ptrArr :", ptrArr)
  
  ```

  ???指针数组里的指针可以在指向一个数组吗???

  ？？为什么数组指针会有size？？

* slice

  引用类型，不能通过==进行比较，要通过特定的函数来比较。（原因还没看明白）

  https://books.studygolang.com/gopl-zh/ch4/ch4-02.html

* 结构体

  ？？结构体套结构体会什么结果？？

* 