# 1. 数据类型
    Go 是 静态类型的，这意味着 一旦定义了变量类型，它就只能存储该类型的数据。
## 1.1. bool 类型
    1. 表示布尔值，只有 true 或 false 两个取值
    2. 默认值为 false
```cgo
    var b1 bool = true //带有初始值的 类型声明
    var b2 = true      // 带初始值的 无类型声明
    var b3 bool        // 不带初始值的类型声明  默认为 false
    b4 := true         // 自动类型推断
    
    fmt.Println(b1) // true
    fmt.Println(b2) // true
    fmt.Println(b3) // false
    fmt.Println(b4) // true
```

## 1.2. 数值
     整数类型、浮点类型、复数类型 和 字符类型
### 1.2.1. 整数
    整数数据类型 用于 存储没有小数的整数，如 35、-50 或 1345000
#### ①. int
    用 int 关键字之一声明的有符号整数可以存储正数和负数

    因为有一位表示正负(0/1)，故取值范围应在(2的n-1次方)
    又因为0占正数的一个位置所以最大取值是(2的n-1次方)-1
##### a. int
    大小取决于平台（32位平台占32位，64位平台占64位）
##### b. int8
    8位：取值范围在[-128, 127]
##### c. int16
    16位：取值范围在[-32768, 32767]
##### d. int32
##### e. int64
##### f. rune32
    32位：是int32的别名用于处理 Unicode字符

#### ②. uint
    使用 uint 关键字声明的无符号整数只能存储非负值
##### a. uint
    大小取决于平台（32位平台占32位，64位平台占64位）
##### b. uint8
    8位：取值范围在[0, 255]
##### c. uint16
    16位：取值范围在[0, 65535]
##### d. uint32
##### e. uint64
##### f. byte
    8位：uint8 的一个 别名
##### g. uintptr
    取决于平台：表示指针类型

#### ③. 整数字面量
    整数字面值是表示整数常量的数字序列。可选前缀设置非十进制基数。为了便于阅读，下划线字符 _ 可能出现在 基本前缀之后 或 连续数字之间；这样的下划线不会改变字面值
- 0b 或 0B 表示二进制序列
- 0o 或 0O 表示八进制序列
- 0x 或 0X 表示十六进制。在十六进制文字中,字母a到f表示值10到15
- 单个 0 被视为十进制零

### 1.2.2. 浮点类型
    浮点数据类型 用于 存储带小数点的正数和负数
    浮点数在计算机中的表示方法遵循IEEE 754标准
    浮点类型的 默认类型是 float64。如果不指定类型，则类型为 float64
    float32 与 float64 类型的变量不能相互赋值。是两个不同的数据类型

#### 1.2.2.1 介绍
- float32：32位 = 4字节*8 小数部分只能精确到后面6位，加上小数点前的一位，总的有效数字为7位。
- float64：64位 = 8字节*8 小数部分只能精确到后面15位，加上小数点前的一位，总的有效数字为16位。
```cgo
// 比如 10000018这个数，用 float32 的类型来表示的话，由于其有效位是7位
// 将10000018 表示成科学计数法，就是 1.0000018 * 10^7，能精确到小数点后面6位。

    // 临界
    var myfloat float32 = 10000018
    fmt.Println("myfloat: ", myfloat) // myfloat: 1.0000018e+07
    
    // 越界：由于其类型是 float32，精度不足，导致最后比较的结果是不相等（从小数点后第七位开始不精确）
    var myfloat01 float32 = 100000182
    var myfloat02 float32 = 100000187
    fmt.Println("myfloat: ", myfloat01) // myfloat: 1.00000184e+08
    fmt.Println("myfloat: ", myfloat01+5) // myfloat: 1.0000019e+08
    fmt.Println(myfloat02 == myfloat01+5) // false
    // 由于精度的问题，就会出现这种很怪异的现象，myfloat == myfloat +1 会返回 true
```

#### 1.2.2.2 字面量
    浮点字面值是浮点常量的 十进制 或 十六进制 表示形式

- 十进制浮点字面值：由 整数部分、小数点、小数部分 和 指数部分
- 十六进制浮点字面值：由 0x/0X前缀、整数部分、小数点、小数部分 和 指数部分

### 1.2.3. 复数类型
    复数有 实部 和 虚部

- complex64 的实部和虚部为 32 位
- complex128 的实部和虚部为 64 位

```cgo
    var c1 complex64  // 实部和虚部均为32位
	c1 = 1 + 2i
	fmt.Println(c1)  // (1+2i)
	
	var c2 complex128  // 实部和虚部均为64位
	c2 = 2 + 3i
	fmt.Println(c2)  // (2+3i)
```

### 1.2.4. 字符类型
    和C一样 把字符当作整数进行处理
    字符默认的类型为 rune

- byte 类型，它是 uint8 的一个类型别名，用于处理 8 位编码的字符集。例如，ASCII 和 Latin-1
- rune 类型，它是 int32 的类型别名，用于处理 Unicode。由于 Go 对 Unicode 字符的处理采用 UTF-8 编码方式，因此，可以是 rune 类型处理 UTF-8 字符

```cgo
    var ch rune = '你'  
    fmt.Println(ch)                            // 20320  
    fmt.Printf("Type: %T value: %c\n", ch, ch) // Type: int32 value: 你 
     
    var as byte = 'a'  
    fmt.Println(as)                            // 97  
    fmt.Printf("Type: %T value: %c\n", as, as) // Type: uint8 value: a
```

#### 1.2.4.1. 字符字面量
```cgo
    // Go 中字符的字面值使用单引号 '*' 标识。默认采用 rune 类型进行处理。例如
    var ch = 'a'   // 英文字符
    var ch2 = '天' // 中文字符
```
#### 1.2.4.2. 转义字符序列
    有一些 特殊符号，例如换行符，无法采用上述规则书写，因为它们是 不可见 的（非打印字符）或者是 无法从键盘输入 的。
    为了使程序可以处理字符集中的每一个字符，Go语言同样提供了特殊的字符表示方法：转义字符序列。
    转义字符序列共有两种形式：字符转义序列 和 数字转义序（有 八进制转义序列 和 十六进制转义序列 两种）。
    转义字符使用 \ 开头。

- 转义字符序列：字符转义序列使用 反斜杠(\) 和 一个 基本符号 表示
- 数字转义序列：从字符集中查找字符编码对应的八进制数和十六进制数。然后，使用 \ 和跟随其后的字符编码表示

## 1.3. string
    string 数据类型 用于 存储字符（文本）序列。
    字符串值必须用 双引号 引起来
    string 类型的默认值位空字符串("")
### 1.3.1. 多行字符串
```cgo
    var mutiline string = `
    白日依山尽，
    黄河入海流
    `
```
### 1.3.2. 字符串底层实现
    字符串是通过字节数组实现
    1 byte = 8 bit(八位二进制表示一个字节)
    UTF-8 编码下一个中文汉字由 3~4 个字节组成

```cgo
    var str string = "hello小飞"
    
    // Type: string, value: hello小飞, len: 11
	fmt.Printf("Type: %T, value: %v, len: %v\n", str, str, len(str)) 
```

## 1.4. 类型转换
    Go 只提供了 强制类型转换，没有隐式类型转换。该语法只能在 两个类型之间支持相互转换 的时候使用

```cgo
    var a int8 = 10
    var b int16 = int16(a) // 不能直接用 int8 的变量赋值给 int16    
    b = int16(a)  // 可以这样
```

## 1.5. nil
    nil并不等同于其他语言的null，nil仅仅只是一些类型的零值，并且不属于任何类型
```cgo
// 各种数据类型的零值
// 数字: 0
// 布尔: false
// 字符: ""
// 数组:	固定长度的对应类型的零值集合 [0,0,0,0]、["", "", "", ""]
// 结构体: 内部字段都是零值的结构体
// 切片，映射表，函数，接口，通道，指针: nil

// nil对应的各种数据
// nil  : 指针类型，例如 *int、*string 等。 
// nil  : 切片类型，例如 []int、[]string 等。
// nil  : 映射类型，例如 map[string]int 。
// nil  : 函数类型。
// nil  : 接口类型。
// 0    : int。
// 0.0  : float64。
// false: bool。
// ""   : string。
```

# 2. 指针
    Go语言中的指针不能进行偏移和运算，因此我们说Go语言的指针是只读的。

## 2.1. 常用的方法
- &：用于取地址
- *：根据地址取值

```cgo
    res := struct {
		Content string // 值类型
		Status  *uint8 // 指针类型：允许为0
		Reason  *string // 指针类型：允许为""
	}{
		Content: v.Content,
		Status:  &status, // 指针类型的元素在被赋值时 应该用&value的形式
		Reason:  &v.AuditReason,
	}
```

## 2.2. 指针存在的意义
- 更高效的内存操作：通过指针可以直接操作内存地址，避免了大量数据的复制，从而提高程序的性能和效率，尤其是在处理大型数据结构或频繁传递数据的场景中。
- 实现动态数据结构：如链表、树等复杂的数据结构，需要通过指针来灵活地构建和管理节点之间的关系。
- 方便参数传递：如前面提到的，如果希望在函数内部修改外部变量的值，传递指针可以直接操作原始变量，而不是值的副本。
- 实现引用传递：某些情况下，需要多个函数或部分的代码共同操作同一块内存数据，指针提供了这种共享和协同操作的方式。
- 节省内存：对于大型数组或结构体，如果按值传递会消耗大量内存用于复制，使用指针只需传递一个地址。
- 实现底层操作和系统编程：在与操作系统、硬件交互或进行底层编程时，经常需要直接操作内存地址，指针提供了这种能力。

## 2.3. 为什么项目里要用到指针
    动态内存分配：使用 new 或 make 分配内存时，返回的是指针，通过指针来管理和操作动态分配的内存。

## 2.4. 创建
    关于指针有两个常用的操作符，一个是取地址符&，另一个是解引用符*。
```cgo
// 打印出来是地址
func main() {
   num := 2
   p := &num
   fmt.Println(p) // 0xc00001c088
}

// 解引用 打印出来是值
func main() {
	num := 2
	p := &num
	rawNum := *p
	fmt.Println(rawNum) // 2
}

// p是一个指针，对指针类型解引用就能访问到指针所指向的元素
func main() {
   var numPtr *int // 只在此处声明了指针 将返回 指针对应的空值 nil
   fmt.Println(numPtr) // <nil>
}

// 空指针，无法正常使用。要么使用取地址符将其他变量的地址赋值给该指针，要么就使用内置函数new手动分配
func main() {
   var numPtr *int // 声明了一个指向整数的指针 numPtr ，此时它的初始值为 nil 
   numPtr = new(int) // 为 numPtr 分配了内存，并将其指向一个新的整数空间。
   fmt.Println(numPtr) // 取到一个地址：0xc00008a050(上一步为其新分配的内存地址)
   // 而这个新分配的整数空间被初始化为其类型的零值，对于 int 类型，零值是 0 。 所以，如果您想要获取这个指针指向的值，可以使用 *numPtr ，其值为 0 
}

// 短变量 和上面的代码实现的效果是一样的
func main() {
   numPtr := new(int)
   fmt.Println(numPtr) // 0xc000200050
}

// new函数只有一个参数那就是类型，并返回一个对应类型的指针，函数会为该指针分配内存，并且指针指向对应类型的零值
func main() {
   fmt.Println(*new(string)) // 
   fmt.Println(*new(int)) // 0
   fmt.Println(*new([5]int)) // [0, 0, 0, 0, 0]
   fmt.Println(*new([]float64)) // []
}
```

## 2.5. 禁止指针运算
https://golang.halfiisland.com/essential/base/67.pointer.html#%E5%88%9B%E5%BB%BA


# 3. 类型转换

## 3.1. int 转 uint 数据出错
    int 首位表示正负 当为负值时 转换成uint 出现数据不正确的错误 
```cgo
// 代码示例
func getDeadlyPoints(userID uint, nowPoint uint32, expireTime int64) uint32 {
	// 获取过期时间(前年年初)
	res := struct {
		Point uint32 // 这两年挣的积分
	}{}
	
	// ... 次数省略其他的代码
	
	// 即将过期的积分 = 当前积分 - 这两年挣的积分
	// DeadlyPoints := int(nowPoint - res.Point) 这样改是不对的 需要将两个数据分别int之后相减
	DeadlyPoints := int(nowPoint) - int(res.Point)
	if DeadlyPoints < 0 {
		DeadlyPoints = 0
	}
	return uint32(DeadlyPoints) // 最后得到的数据 确保是 正数 在进行uint
}
```

## 3.2. float 转 int 数据出错
    在强制转换过程中，小数部分会被直接截断，只保留整数部分。
    float比int范围大 不会发生溢出，但是丢失精度会导致结果不同 
```cgo
// 代码示例
float a := 0.5 
b := (int)a // b =0 直接截掉小数部分 ，没有进行四舍五入
```

# 4. 切片
> 在 Go 中，数组和切片两者看起来长得几乎一模一样，但功能有着不小的区别，数组是定长的数据结构，长度被指定后就不能被改变，而切片是不定长的，切片在容量不够时会自行扩容。

## 4.1. 数组
    如果事先就知道了要存放数据的长度，且后续使用中不会有扩容的需求，就可以考虑使用数组，Go 中的数组是值类型，而非引用，并不是指向头部元素的指针。
    数组作为值类型，将数组作为参数传递给函数时，由于 Go 函数是传值传递，所以会将整个数组拷贝。

### 4.1.1. 初始化
    数组在声明是长度只能是一个常量，不能是变量
```cgo
// 正确示例
var a [5]int

// 错误示例
l := 1
var b [l]int

// 初始化一个长度为 5 的整型数组
var nums [5]int

// 用元素初始化
nums := [5]int{1, 2, 3}

// 让编译器自动推断长度
// 等价于nums := [5]int{1, 2, 3, 4, 5}，省略号必须存在，否则生成的是切片，不是数组
nums := [...]int{1, 2, 3, 4, 5}

// 还可以通过new函数获得一个指针
nums := new([5]int)
``` 
    以上几种方式都会给nums分配一片固定大小的内存，区别只是最后一种得到的值是指针。                  
    在数组初始化时，需要注意的是，长度必须为一个常量表达式，否则将无法通过编译，常量表达式即表达式的最终结果是一个常量，错误例子如下：
```cgo
length := 5 // 这是一个变量
var nums [length]int

// length是一个变量，因此无法用于初始化数组长度，如下是正确示例：
const length = 5
var nums [length]int // 常量
var nums2 [length + 1]int // 常量表达式
var nums3 [(1 + 2 + 3) * 5]int // 常量表达式
var nums4 [5]int // 最常用的
```

### 4.1.2. 使用
```cgo
// 只要有数组名和下标，就可以访问数组中对应的元素。
fmt.Println(nums[0])

// 同样的也可以修改数组元素
nums[0] = 1

// 还可以通过内置函数len来访问数组元素的数量
len(nums)

// 内置函数cap来访问数组容量，数组的容量等于数组长度，容量对于切片才有意义。
cap(nums)
```

### 4.1.3. 切割
```cgo
// 切割数组的格式为arr[startIndex:endIndex]，切割的区间为左闭右开，例子如下：
nums := [5]int{1, 2, 3, 4, 5}
nums[1:] // 子数组范围[1,5) -> [2 3 4 5]
nums[:5] // 子数组范围[0,5) -> [1 2 3 4 5]
nums[2:3] // 子数组范围[2,3) -> [3]
nums[1:3] // 子数组范围[1,3) -> [2 3]

// 数组在切割后，就会变为切片类型
func main() {
  arr := [5]int{1, 2, 3, 4, 5}
  // %T	输出值对应的 Go 语言类型值
  fmt.Printf("%T\n", arr)       // [5]int
  fmt.Printf("%T\n", arr[1:2])  // []int
}

// 若要将数组转换为切片类型，不带参数进行切片即可，转换后的切片与原数组指向的是同一片内存，修改切片会导致原数组内容的变化
func main() {
  arr := [5]int{1, 2, 3, 4, 5}
  slice := arr[:]
  slice[0] = 0
  // %v	输出值原本的形式，多用于数据结构的输出
  fmt.Printf("array: %v\n", arr)    // array: [0 2 3 4 5]
  fmt.Printf("slice: %v\n", slice)  // slice: [0 2 3 4 5]
}

// 如果要对转换后的切片进行修改，建议使用下面这种方式进行转换
func main() {
  arr := [5]int{1, 2, 3, 4, 5}
  slice := slices.Clone(arr[:])
  slice[0] = 0
  fmt.Printf("array: %v\n", arr)    // array: [1 2 3 4 5]
  fmt.Printf("slice: %v\n", slice)  // slice: [0 2 3 4 5]
}
```

## 4.2. 切片
    切片在 Go 中的应用范围要比数组广泛的多，它用于存放不知道长度的数据，且后续使用过程中可能会频繁的插入和删除元素。

### 4.2.1. 初始化
    切片与数组在外貌上的区别，仅仅只是少了一个初始化长度。
    通常情况下，推荐使用make来创建一个空切片，只是对于切片而言，make函数接收三个参数：类型，长度，容量。
    切片的长度代表着切片中元素的个数，切片的容量代表着切片总共能装多少个元素，切片与数组最大的区别在于切片的容量会自动扩张，而数组不会
```cgo
// 切片的初始化方式有以下几种
var nums []int // 值
nums := []int{1, 2, 3} // 值
nums := make([]int, 0, 0) // 值  第一个参数：类型、第二个参数：长度、第三个参数：容量
nums := new([]int) // 指针
```
    切片的底层实现依旧是数组，是引用类型，可以简单理解为是指向底层数组的指针。
    通过var nums []int这种方式声明的切片，默认值为nil，所以不会为其分配内存，而在使用make进行初始化时，建议预分配一个足够的容量，可以有效减少后续扩容的内存消耗。

### 4.2.2. 使用
    切片的基本使用与数组完全一致，区别只是切片可以动态变化长度，下面看几个例子。
```cgo
// 切片可以通过append函数实现许多操作，函数签名如下，slice是要添加元素的目标切片，elems是待添加的元素，返回值是添加后的切片。
func append(slice []Type, elems ...Type) []Type
// 首先创建一个长度为 0，容量为 0 的空切片，然后在尾部插入一些元素，最后输出长度和容量。
nums := make([]int, 0, 0)
nums = append(nums, 1, 2, 3, 4, 5, 6, 7)
fmt.Println(len(nums), cap(nums)) // 7 8 可以看到长度与容量并不一致。

// 新 slice 预留的 buffer 容量 大小是有一定规律的。 
// 在 golang1.18 版本更新之前网上大多数的文章都是这样描述 slice 的扩容策略的： 当原 slice 容量小于 1024 的时候，新 slice 容量变成原来的 2 倍；原 slice 容量超过 1024，新 slice 容量变成原来的 1.25 倍。 
// 在 1.18 版本更新之后，slice 的扩容策略变为了： 当原 slice 容量(oldcap)小于 256 的时候，新 slice(newcap)容量为原来的 2 倍；原 slice 容量超过 256，新 slice 容量 newcap = oldcap+(oldcap+3*256)/4

```
### 4.2.3. 插入元素
    切片元素的插入也是需要结合append函数来使用
```cgo
nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// 从头部插入元素
nums = append([]int{-1, 0}, nums...)
fmt.Println(nums) // [-1 0 1 2 3 4 5 6 7 8 9 10]

// 从中间下标 i 插入元素
nums = append(nums[:i+1], append([]int{999, 999}, nums[i+1:]...)...)
fmt.Println(nums) // i=3，[1 2 3 4 999 999 5 6 7 8 9 10]

// 从尾部插入元素，就是append最原始的用法
nums = append(nums, 99, 100)
fmt.Println(nums) // [1 2 3 4 5 6 7 8 9 10 99 100]

```
### 4.2.4. 删除元素
    切片元素的删除也是需要结合append函数来使用
```cgo
nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// 从头部删除 n 个元素
nums = nums[n:]
fmt.Println(nums) //n=3 [4 5 6 7 8 9 10]

// 从尾部删除 n 个元素
nums = nums[:len(nums)-n]
fmt.Println(nums) //n=3 [1 2 3 4 5 6]

// 从中间下标 i 删除 n 个元素
nums = append(nums[:i], nums[i+n:]...) // nums[:2]->1, 2, nums[5:]->6, 7, 8, 9, 10
fmt.Println(nums) // i=2，n=3，[1 2 6 7 8 9 10]

// 删除所有元素
nums = nums[:0]
fmt.Println(nums) // []

```
### 4.2.5. 拷贝
    切片在拷贝时需要确保目标切片有足够的长度
```cgo
func main() {
  dest := make([]int, 0)
  src := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
  fmt.Println(src, dest)        // [1 2 3 4 5 6 7 8 9] []
  fmt.Println(copy(dest, src))  // 0
  fmt.Println(src, dest)        // [1 2 3 4 5 6 7 8 9] []
}

// 将长度修改为 10，输出如下
dest := make([]int, 10)
[1 2 3 4 5 6 7 8 9] [0 0 0 0 0 0 0 0 0 0]
9
[1 2 3 4 5 6 7 8 9] [1 2 3 4 5 6 7 8 9 0]

```
### 4.2.6. 遍历
    切片的遍历与数组完全一致
```cgo
func main() {
   slice := []int{1, 2, 3, 4, 5, 7, 8, 9}
   for i := 0; i < len(slice); i++ {
      fmt.Println(slice[i])
   }
}

// for range循环
func main() {
  slice := []int{1, 2, 3, 4, 5, 7, 8, 9}
  for index, val := range slice {
    fmt.Println(index, val)
  }
}
```
## 4.3. 多维切片
```cgo
var nums [5][5]int
for _, num := range nums {
   fmt.Println(num)
}
fmt.Println()
slices := make([][]int, 5)
for _, slice := range slices {
   fmt.Println(slice)
}

// 输出结果
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]

[]
[]
[]
[]
[]

// 可以看到，同样是二维的数组和切片，其内部结构是不一样的。数组在初始化时，其一维和二维的长度早已固定，而切片的长度是不固定的，切片中的每一个切片长度都可能是不相同的，所以必须要单独初始化，切片初始化部分修改为如下代码即可。
slices := make([][]int, 5)
for i := 0; i < len(slices); i++ {
   slices[i] = make([]int, 5)
}
// 最终输出结果
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]

[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
```
## 4.4. 拓展表达式
    只有切片才能使用拓展表达式
```cgo
// 切片与数组都可以使用简单表达式来进行切割，但是拓展表达式只有切片能够使用，该特性于 Go1.2 版本添加，主要是为了解决切片共享底层数组的读写问题，主要格式为如下，需要满足关系low<= high <= max <= cap，使用拓展表达式切割的切片容量为max-low
slice[low:high:max]

// low与high依旧是原来的含义不变，而多出来的max则指的是最大容量，例如下方的例子中省略了max，那么s2的容量就是cap(s1)-low
s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
s2 := s1[3:4] // cap = 9 - 3 = 6

// 那么这么做就会有一个明显的问题，s1与s2是共享的同一个底层数组，在对s2进行读写时，有可能会影响的s1的数据，下列代码就属于这种情况
s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
s2 := s1[3:4]                          // cap = 9 - 3 = 6
// 添加新元素，由于容量为6.所以没有扩容，直接修改底层数组
s2 = append(s2, 1)
fmt.Println(s2)
fmt.Println(s1)

// 输出结果
[4 1]
[1 2 3 4 1 6 7 8 9]

// 可以看到明明是向s2添加元素，却连s1也一起修改了，拓展表达式就是为了解决此类问题而生的，只需要稍微修改一下就能解决该问题
func main() {
   s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
   s2 := s1[3:4:4]                        // cap = 4 - 3 = 1
   // 容量不足，分配新的底层数组
   s2 = append(s2, 1)
   fmt.Println(s2)
   fmt.Println(s1)
}

// 现在得到的结果就是正常的
[4 1]
[1 2 3 4 5 6 7 8 9]
```
## 4.5. clear
```cgo
// 在 go1.21 新增了clear内置函数，clear 会将切片内所有的值置为零值
package main

import (
    "fmt"
)

func main() {
    s := []int{1, 2, 3, 4}
    clear(s)
    fmt.Println(s)
}

// 输出
[0 0 0 0]

// 如果想要清空切片，可以
func main() {
  s := []int{1, 2, 3, 4}
    s = s[:0:0]
  fmt.Println(s)
}

// 限制了切割后的容量，这样可以避免覆盖原切片的后续元素。
```


# 5. HTTP 协议中的请求方法

## 5.1. get请求

### 5.1.1. get接口传递数组参数
```cgo
// 举例：可以批量选择Tag

// 可以接受 Tag[]:1,Tag[]:2 这样的数据
input := struct {
    Tag   []uint `form:"Tag[]"`
}{}

// 如果参数中Tag的长度 大于0 将在数据库中 查找 tag.id 在 input.Tag 数组中的 数据
if len(input.Tag) > 0 {
    tx.Where("tag.id IN (?)", input.Tag)
}

```

### 5.1.2. GET请求为什么被认为不安全

#### 5.1.2.1. get为什么不安全
- 参数暴露在 URL 中：GET 请求的参数是附加在 URL 中的，这意味着这些参数会在浏览器的地址栏中可见，并且可能会被服务器、代理服务器、浏览器等记录在访问日志中。如果参数包含敏感信息（如密码、个人身份信息等），就存在信息泄露的风险。
- 可缓存性：浏览器和代理服务器可能会缓存 GET 请求的响应。这可能导致一些敏感数据被意外存储和传播。
- 不可更改性：GET 请求通常被认为是只读的操作，不应用于修改服务器上的数据。然而，如果服务器端没有正确处理，可能会因为 GET 请求而意外地修改数据，这违反了 GET 请求的预期用途和最佳实践。
- 长度限制：URL 通常有长度限制，这可能会限制能够传递的数据量。

#### 5.1.2.2. post为什么更安全
- 不可缓存性：默认情况下，POST 请求的响应通常不会被浏览器和中间代理服务器缓存，减少了数据被意外存储和传播的可能性。
- 数据量限制较小：POST 请求可以发送更大量的数据，因为它不受 URL 长度的限制。

#### 5.1.2.3. 浏览器和代理服务器为什么会缓存get请求的响应
    浏览器和代理服务器会缓存 GET 请求的响应，主要是出于性能和效率的考虑。

1. 缓存的目的是为了减少重复的网络请求，加快页面加载速度，降低服务器负载。当浏览器或代理服务器接收到一个 GET 请求时，如果之前已经处理过相同的请求并且响应满足一定的缓存规则（例如响应头中的 Cache-Control、Expires 等字段指定了可缓存的条件和时间），它们就会直接使用缓存的响应，而不再向服务器重新发送请求。

2. 对于 GET 请求，由于其通常被认为是 用于获取不会改变的数据，所以更适合进行缓存。而 POST 请求通常用于修改数据或执行具有副作用的操作，其响应的结果可能因每次请求的参数不同而变化，因此不太适合被缓存。

3. 但缓存机制也可能导致一些问题，比如数据更新不及时，所以在实际开发中，需要通过正确设置响应头来控制缓存策略，以确保用户获取到最新和准确的数据。

#### 5.1.2.4. 在什么情况下 项目中要尽量避免用到get
- 涉及敏感信息的传递：如用户密码、个人身份信息、金融交易数据等。因为这些信息会暴露在 URL 中，增加了安全风险。
- 数据修改或写入操作：例如创建、更新或删除数据。GET 请求不应用于执行这些可能改变服务器状态的操作，应使用 POST、PUT 或 DELETE 方法。
- 大量数据的传输：由于 URL 长度的限制，GET 请求不适合传输大量的数据。
- 不确定性或动态的请求参数：如果请求参数的值经常变化，或者其组合和顺序不固定，使用 GET 可能导致难以管理和理解的 URL。
- 违反幂等性原则的操作：幂等性意味着多次执行相同的 请求应该产生相同的结果。对于非幂等性的操作，不应使用 GET。
- 不符合缓存策略：如果数据需要实时更新且不应被缓存，使用 GET 可能导致缓存问题，此时应选择其他方法。

## 5.2. HEAD请求
    HTTP 的 HEAD 请求是与 GET 请求类似的一种请求方法，但 HEAD 请求只返回响应头信息，而不返回响应体内容。
    这意味着使用 HEAD 请求，您可以获取关于资源的元数据，例如服务器返回的响应状态码、Content-Type（内容类型）、Content-Length（内容长度）、Last-Modified（最后修改时间）等，而无需下载资源的实际数据。

### 5.2.1. 使用场景：
    1. 检查资源是否存在，而无需获取其内容。             
    2. 在下载大文件之前，先获取其元信息（如文件大小），以决定是否要下载。          
    3. 监测链接的有效性，而不传输大量数据。

    在一些场景中可以将 GET 请求改为 HEAD 请求
    如果您只需要获取资源的元数据（如响应头中的信息）而不需要获取资源的实际内容，那么将 GET 请求改为 HEAD 请求是合适的。
```cgo
// 代码举例
link.GET("/code", controllers.UserController{}.GiveCode)
// 上术是个普通的get请求 不需要获取具体内容的话 可以将其改为HEAD请求
link.HEAD("/code", controllers.UserController{}.GiveCode)
```

## 5.3. &http.Client.Get(url)
    只有在 由于各种情况没有访问到地址的情况下 才会 err != nil   
    其他情况只要能返回 状态码的 应该都是 err == nil 的情况    
    注意：具体的还不太清楚 目前只测试出 一部分状况           
```cgo    
// 项目中的具体代码如下：
client := &http.Client{
    Timeout: 10 * time.Second,
}
// file.File: https://123456789.com
resp, err := client.Get(file.File)
if err != nil {
    // 当网络连接异常、请求构建错误、HTTP 协议错误、请求超时或其他与请求执行相关的底层通信问题发生
    log.Println("GCFB31", file.ID, err)
    return
}
defer resp.Body.Close()

if resp.StatusCode != 200 {
    if resp.StatusCode == 404 {
        return
    }
    log.Println("GCFB32", file.ID, "http:", resp)
    return
}
```

# 6. fmt和log
    两者都能将数据以某种形式展示出来。           
    在一些情况下，log 的性能可能略优于 fmt ，特别是在频繁的日志记录场景中。

## 6.1. fmt
- fmt 主要用于格式化输出数据，更侧重于将数据以特定的格式打印到标准输出（通常是终端）
- 默认是标准输出

## 6.2. log
- 更侧重于记录日志信息，包括时间戳、日志级别等。
- 支持不同的日志级别，如 Info、Warning、Error 等。
- 输出的日志通常包含更多的上下文信息，如时间戳。
- 可以将日志输出到不同的目的地，例如文件。

## 6.3. 总结
    总结来说的话 个人用log会更多一些 所以一般在调试的时候也会是用log而很少用到fmt

# 7. defer啥时候调用
    后进先出的形式 在每次函数执行结束时 执行 也就是说，当包含 defer 语句的函数即将返回时，无论是正常返回还是因为出现异常而返回，defer 注册的语句都会被执行。

## 7.1. defer会经常用到的地方
    在许多实际的 Go 编程场景中，特别是涉及资源管理（如文件操作、数据库连接、网络连接等）、解锁互斥锁、关闭通道等操作时，defer 被广泛使用以确保资源的正确释放和清理，避免资源泄漏和错误。

## 7.2. 循环读入不同的文件 需要再循环内 多次执行对不同文件的defer file.Close()吗
    在循环中读入不同的文件时，通常需要在每次循环中为当前正在操作的文件执行 defer file.Close() 。这样可以确保在每次循环迭代结束时，当前文件能被正确关闭，及时释放资源，并且即使在循环内部出现错误或提前返回的情况下，也能保证文件被关闭。

    需要注意的是，如果循环的执行频率很高或者文件操作很频繁，可能会因为大量的 defer 操作带来一些性能开销。在这种情况下，可以考虑在循环结束后手动关闭文件，但要确保在任何情况下都能正确关闭文件以避免资源泄漏。
```cgo
// 项目代码
// 说明：在这个 for 循环中使用单独的 func() 这个匿名函数中，可以方便地使用 defer 语句来确保资源的正确释放，比如关闭文件和 HTTP 响应体。
for _, file := range files {
		// ...此处省略一些其他的代码

		func() {
			resp, err := client.Get(file.File)
			if err != nil {
				// 打印除404外的其他错误
				if resp.StatusCode != 404 {
					log.Println("GCFB3", file.ID, "http code:", resp.StatusCode)
				}
				return
			}
			defer resp.Body.Close() // 此处的defer会在匿名函数执行完毕时执行（无论正常结束还是因错误提前结束）

			// 创建文件
			crea, err := os.Create(filePath)
			if err != nil {
				log.Println("GCFB4", file.ID, err)
				return
			}
			defer crea.Close()
		}()
	}
```


# 8. config文件的作用
    在 Gin 项目中，config 文件通常用于存储项目的配置信息，例如数据库连接字符串、服务器端口、环境变量、应用的各种设置等。
    配置文件中的信息和方法一般会在主函数运行时执行

## 8.1. 配置服务器端口
    在config文件中写上 port = 8080 在主函数运行的时候调用就可以了
```cgo
// 举例config文件：
// 一个全局的配置文件
type config struct {
	Swipe struct {
		Port int
	}
}

var Config config

func InitializeConfig() {
	Config.Swipe.Port = 8082              // 设置端口号
}

// 举例主函数调用部分
r := router.Router()
// 8082的项目从本地配置文件里拿
r.Run(":" + strconv.Itoa(config.Config.Swipe.Port))
```

## 8.2. 环境变量
    全局变量 可以被运行的程序访问到 可以包含 路径信息、配置选项等
    可以存在ENV文件中 env:environment环境 因此猜测是环境变量

## 8.3. 数据库连接字符串

```cgo
// 类似这样吧
{
  "database": {
    "connection_string": "postgres://username:password@localhost:5432/database_name"
  }
}
// 在项目中找到的是以下这样的
{
  "DB": {
    "User": "root",
    "Password": "123456",
    "Host": "127.0.0.1",
    "Port": "3306",
    "Database": "jy"
  },
  "Server": {
    "Port": "80",
  },
}
```

# 9. 加密

## 9.1. MD5
    md5只能加密不能解密
    是一种广泛使用的哈希函数，用于生成数据的固定长度（128 位）的哈希值。

- 优点：                         
    1. 对于给定的输入数据，其生成的 MD5 值通常是唯一的，不同的数据产生不同的哈希值。（但是也有例外 有可能存在不同的数据产生相同的哈希值）
    2. 一般用于验证数据在传输或存储过程中是否被篡改。（数据传递后 接收者将数据与md5生成的数据进行对比）
- 缺点：                
    1. 尽量不要用此来加密敏感信息：密码、身份信息等         
    2. 无法通过哈希值推出原始数据           

## 9.2. 对称加密
    加密和解密使用相同的密钥。
    常见的对称加密算法有 AES（高级加密标准）等

- 优点：    
  1. 计算效率高：加密和解密的速度通常比非对称加密快，适合处理大量数据。
  2. 算法相对简单：实现和理解相对容易。
- 缺点：    
  1. 密钥分发问题：通信双方需要安全地共享密钥，如果密钥在分发过程中被窃取或泄露，加密就会被破解。
  2. 扩展性差：当涉及多个参与者时，密钥管理变得复杂。
  3. 缺乏不可否认性：因为双方都拥有相同的密钥，所以无法确定消息的发送方。

## 9.3. 非对称性加密
    也称为公钥加密，使用一对密钥：公钥和私钥。           
    公钥可以公开，用于加密数据；私钥保密，用于解密用对应公钥加密的数据。
    常见的非对称加密算法有 RSA、ECC（椭圆曲线加密算法）等。

- 优点：
  1. 解决密钥分发问题：无需在通信双方之间安全地预先共享密钥。
  2. 提供数字签名和身份验证：可以用于确认消息的来源和完整性，实现不可否认性。
  3. 更安全的密钥管理：私钥不需要在网络上传输，降低了密钥泄露的风险。
- 缺点：
  1. 计算开销大：加密和解密的速度通常比对称加密慢得多，不适合处理大量数据的实时加密。
  2. 密钥长度较长：导致计算和存储成本增加。

# 10. 映射
>map 本身的设计目标是在单线程或低并发场景下提供高效的键值对存储和访问操作。它的这种特性使得在常见的非并发场景中能够具有出色的性能和简洁的使用方式。
> 
>在高并发场景下，如果不正确地使用 map 导致了并发读写冲突和错误，这更多地是使用者没有根据场景选择合适的数据结构和并发控制方式，而不是 map 本身的缺陷。

## 10.1. 初始化
    有两种初始化的方法
```cgo
// 第一种：字面量 map[keyType]valueType{}
mp := map[int]string{
   0: "a",
   1: "a",
   2: "a",
   3: "a",
   4: "a",
}
mp := map[string]int{
   "a": 0,
   "b": 22,
   "c": 33,
}

// 第二种：内置函数make，对于map而言，接收两个参数，分别是类型与初始容量
mp := make(map[string]int, 8)
mp := make(map[string][]int, 10)
mapTemp := make(map[string]bool) // 可以用来去重

```

## 10.2. 访问
    访问一个map的方式就像通过索引访问一个数组一样。
```cgo
func main() {
	mp := map[string]int{
		"a": 0,
		"b": 1,
		"c": 2,
		"d": 3,
	}
	fmt.Println(mp["a"]) // 0
	fmt.Println(mp["b"]) // 1
	fmt.Println(mp["d"]) // 3
	fmt.Println(mp["f"]) // 0 即使map中不存在"f"这一键值对，但依旧有返回值
	
	// 在访问map的时候其实有两个返回值，第一个返回值对应类型的值，第二个返回值一个布尔值，代表键是否存在
	if val, exist := mp["f"]; exist {
      fmt.Println(val)
   } else {
      fmt.Println("key不存在")
   }
} 
```

## 10.3. 存值
    map存值的方式也类似数组存值一样已存在的键会覆盖原有的值
```cgo
// 一般情况下是这样的
func main() {
   mp := make(map[string]int, 10)
   mp["a"] = 1
   mp["b"] = 2
   if _, exist := mp["b"]; exist {
      mp["b"] = 3
   }
   fmt.Println(mp)
}

// 但是也存在特殊情况 特殊情况下键为math.NaN()
func main() {
	mp := make(map[float64]string, 10)
	mp[math.NaN()] = "a"
	mp[math.NaN()] = "b"
	mp[math.NaN()] = "c"
	_, exist := mp[math.NaN()]
	fmt.Println(exist) // false
	fmt.Println(mp) // map[NaN:c NaN:a NaN:b]
}
// 针对上述情况官方给出的解释是：因为NaN是IEE754标准所定义的，其实现是由底层的汇编指令UCOMISD完成，这是一个无序比较双精度浮点数的指令，该指令会考虑到NaN的情况，因此结果就是任何数字都不等于NaN，NaN也不等于自身，这也造成了每次哈希值都不相同。关于这一点社区也曾激烈讨论过，但是官方认为没有必要去修改，所以应当尽量避免使用NaN作为map的键。
```

## 10.4. 删除
    删除一个键值对需要用到内置函数delete
```cgo
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   fmt.Println(mp) // map[a:0 b:1 c:2 d:3]
   delete(mp, "a")
   fmt.Println(mp) // map[b:1 c:2 d:3]
}

// 需要注意的是，如果值为NaN，甚至没法删除该键值对。
func main() {
   mp := make(map[float64]string, 10)
   mp[math.NaN()] = "a"
   mp[math.NaN()] = "b"
   mp[math.NaN()] = "c"
   fmt.Println(mp) // map[NaN:c NaN:a NaN:b]
   delete(mp, math.NaN())
   fmt.Println(mp) // map[NaN:c NaN:a NaN:b]
}
```

## 10.5. 遍历
    通过for range可以遍历map
```cgo
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   for key, val := range mp {
      fmt.Println(key, val)
   }
}
// 打印出来的数据是这样的：
c 2
d 3
a 0
b 1

// 可以看到结果并不是有序的，也印证了map是无序存储。值得一提的是，NaN虽然没法正常获取，但是可以通过遍历访问到，例如
func main() {
   mp := make(map[float64]string, 10)
   mp[math.NaN()] = "a"
   mp[math.NaN()] = "b"
   mp[math.NaN()] = "c"
   for key, val := range mp {
      fmt.Println(key, val)
   }
}
// 打印出来的数据：
NaN a
NaN c
NaN b
```

## 10.6. 清空
    只需要一个clear就可以清空
```cgo
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
	}
	clear(m)
	fmt.Println(m) // map[]
}
```

## 10.7. set
    Set是一种无序的，不包含重复元素的集合，Go中并没有提供类似的数据结构实现，但是map的键正是无序且不能重复的，所以也可以使用map来替代set。
```cgo
func main() {
	set := make(map[int]struct{}, 10)
	for i := 0; i < 10; i++ {
		set[rand.Intn(100)] = struct{}{}
	}
	fmt.Println(set) // map[0:{} 18:{} 25:{} 40:{} 47:{} 56:{} 59:{} 81:{} 87:{}]
}
// 一个空的结构体不会占用内存
```

## 10.8. 注意
    map并不是一个并发安全的数据结构，Go团队认为大多数情况下map的使用并不涉及高并发的场景，引入互斥锁会极大的降低性能，map内部有读写检测机制，如果冲突会触发fatal error
```cgo
func main() {

   group.Add(10) // 表示十个并发任务
   
   // 创建一个初始容量为 10 的字符串键、整数值的 map
   mp := make(map[string]int, 10)
   
   for i := 0; i < 10; i++ {
   // 启动一个循环，创建 10 个并发任务
      go func() {
         // 写操作
         for i := 0; i < 100; i++ {
            // 多次向 map 中写入相同的键值对，可能导致并发冲突
            mp["helloworld"] = 1
         }
         // 读操作
         for i := 0; i < 10; i++ {
            // 多次从 map 中读取相同的键对应的值，可能导致并发冲突
            fmt.Println(mp["helloworld"])
         }
         // 通知 WaitGroup 这个并发任务已完成
         group.Done()
      }()
   }
   // 阻塞等待之前添加的 10 个任务全部完成（即 10 次 group.Done() 被调用）
   group.Wait()
}
fatal error: concurrent map writes
```

# 11. 结构体
    结构体可以存储一组不同类型的数据，是一种复合类型。
    Go并非是一个传统OOP的语言，但是Go依旧有着OOP的影子，通过结构体和方法也可以模拟出一个类
    OOP: 面向对象程序设计 Object Oriented Programming
```cgo
type Programmer struct {
	Name     string
	Age      int
	Job      string
	Language []string
}
```

## 11.1. 声明
    结构体本身以及其内部的字段都遵守大小写命名的暴露方式。对于一些类型相同的相邻字段，可以不需要重复声明类型
```cgo
// 字段名不能与方法名重复
type Rectangle struct {
	height, width, area int
	color               string
}
```

## 11.2. 实例化
    Go不存在构造方法，大多数情况下采用如下的方式来实例化结构体。
```cgo
// 初始化的时候就像map一样指定字段名称再初始化字段值
programmer := Programmer{
   Name:     "jack",
   Age:      19,
   Job:      "coder",
   Language: []string{"Go", "C++"},
}

// 可以省略字段名称，当省略字段名称时，就必须初始化所有字段，通常不建议使用这种方式，因为可读性很糟糕
programmer := Programmer{
   "jack",
   19,
   "coder",
   []string{"Go", "C++"}
 }
 
// 如果实例化过程比较复杂，你也可以编写一个函数来实例化结构体，就像下面这样，你也可以把它理解为一个构造函数
type Person struct {
	Name    string
	Age     int
	Address string
	Salary  float64
}
func NewPerson(name string, age int, address string, salary float64) *Person {
	return &Person{
        Name: name, 
        Age: age, 
        Address: address, 
        Salary: salary
	}
}

// Go并不支持函数与方法重载，所以你无法为同一个函数或方法定义不同的参数。如果你想以多种方式实例化结构体，要么创建多个构造函数，要么建议使用options模式。
```

### 11.2.1. 选项模式
    选项模式是Go语言中一种很常见的设计模式，可以更为灵活的实例化结构体，拓展性强，并且不需要改变构造函数的函数签名
```cgo
// 不太懂...
// 假设有下面这样一个结构体
type Person struct {
	Name     string
	Age      int
	Address  string
	Salary   float64
	Birthday string
}

// 声明一个PersonOptions类型，它接受一个*Person类型的参数，它必须是指针，因为我们要在闭包中对Person赋值。
type PersonOptions func(p *Person) // todo 根据猜测 这种应该类似于 创建一个表 然后将表return

// 接下来创建选项函数，它们一般是With开头，它们的返回值就是一个闭包函数。
func WithName(name string) PersonOptions {
	return func(p *Person) {
		p.Name = name
	}
}

func WithAge(age int) PersonOptions {
	return func(p *Person) {
		p.Age = age
	}
}

func WithAddress(address string) PersonOptions {
	return func(p *Person) {
		p.Address = address
	}
}

func WithSalary(salary float64) PersonOptions {
	return func(p *Person) {
		p.Salary = salary
	}
}

// 实际声明的构造函数签名如下，它接受一个可变长 PersonOptions 类型的参数。
func NewPerson(options ...PersonOptions) *Person {
    // 优先应用options
	p := &Person{}
    for _, option := range options {
        option(p)
    }
	
	// 默认值处理
	if p.Age < 0 {
		p.Age = 0
	}
	
    return p
}

// 这样一来对于不同实例化的需求只需要一个构造函数即可完成，只需要传入不同的Options函数即可
func main() {
	pl := NewPerson(
		WithName("John Doe"),
		WithAge(25),
		WithAddress("123 Main St"),
		WithSalary(10000.00),
	)

	p2 := NewPerson(
		WithName("Mike jane"),
		WithAge(30),
	)
}

// 函数式选项模式在很多开源项目中都能看见，gRPC Server的实例化方式也是采用了该设计模式。函数式选项模式只适合于复杂的实例化，如果参数只有简单几个，建议还是用普通的构造函数来解决。
```

## 11.3. 组合
    结构体之间的关系是通过组合来表示的，可以显式组合，也可以匿名组合，后者使用起来更类似于继承，但本质上没有任何变化
```cgo
// 显式组合的方式
type Person struct {
   name string
   age  int
}

type Student struct { // 显试
   p      Person
   school string
}
type Student2 struct { // 隐试
   Person
   school string
}

type Employee struct {
   p   Person
   job string
}

// 在使用时需要显式的指定字段p
student := Student{
   p:      Person{name: "jack", age: 18},
   school: "lili school",
}

// 在使用时需要显式的指定字段p
student2 := Student2{
   person:      Person{name: "jack", age: 18},
   school: "lili school",
}

// 以下两个都是打印出"jack" 的
fmt.Println(student2.p.name)
fmt.Println(student2.name)
```

## 11.4. 指针
    对于结构体指针而言，不需要解引用就可以直接访问结构体的内容
```cgo
// 在编译的时候会转换为(*p).name ，(*p).age，其实还是需要解引用，不过在编码的时候可以省去，算是一种语法糖。
p := &Person{
   name: "jack",
   age:  18,
}
fmt.Println(p.age,p.name)
```

## 11.5. 标签
    结构体标签是一种元编程的形式，结合反射可以做出很多奇妙的功能
```cgo
// 格式如下
`key1:"val1" key2:"val2"`

// 标签是一种键值对的形式，使用空格进行分隔。结构体标签的容错性很低，如果没能按照正确的格式书写结构体，那么将会导致无法正常读取，但是在编译时却不会有任何的报错，下方是一个使用示例。
type Programmer struct {
    Name     string `json:"name"`
    Age      int `yaml:"age"`
    Job      string `toml:"job"`
    Language []string `properties:"language"`
}
// 结构体标签最广泛的应用就是在各种序列化格式中的别名定义，标签的使用需要结合反射才能完整发挥出其功能。
// 反射是什么 不太清楚
```

## 11.6. 内存对齐
    Go结构体字段的内存分布遵循内存对齐的规则，这么做可以减少CPU访问内存的次数，相应的占用的内存要多一些，属于空间换时间的一种手段。
```cgo
// 假设有如下结构体
type Num struct {
	A int64
	B int32
	C int16
	D int8
    E int32
}
// 已知这些类型的占用字节数
int64 8个字节
int32 4个字节
int16 2个字节
int8  1个字节

// 整个结构体的内存占用似乎是8+4+2+1+4=19个字节吗
// 当然不是这样，根据内存对齐规则而言，结构体的内存占用长度至少是最大字段的整数倍，不足的则补齐。
// 因此实际上是占用24个字节，其中有5个字节是无用的。
8个字节: A(int64)
8个字节: B(int32) + C(int16) + D(int8) + 1个空字节
8个字节: E(int32) + 4个空字节

// 再来看下面这个结构体
type Num struct {
	A int8  1字节
	B int64 8字节
	C int8  1字节
}
// 它的内存占用也是24个字节，尽管它只有三个字段，足足浪费了14个字节。
8个字节: A(int8)  1 + 7个空字节
8个字节: B(int64) 8
8个字节: C(int8)  1 + 7个空字节

// 将上述结构体进行调整就可以节省8字节
type Num struct {
	A int8
	C int8
	B int64
}
// 从理论上来说，让结构体中的字段按照合理的顺序分布，可以减少其内存占用。不过实际编码过程中，并没有必要的理由去这样做，它不一定能在减少内存占用这方面带来实质性的提升，但一定会提高开发人员的血压和心智负担，尤其是在业务中一些结构体的字段数可能多大几十个或者数百个，所以仅做了解即可。
```

## 11.7. 空结构体
    空结构体没有字段，不占用内存空间，我们可以通过unsafe.SizeOf函数来计算占用的字节大小
```cgo
// 空结构体的使用场景有很多，比如之前提到过的，作为map的值类型，可以将map作为set来进行使用，又或者是作为通道的类型，表示仅做通知类型的通道。
func main() {
   type Empty struct {}
   fmt.Println(unsafe.Sizeof(Empty{})) // 0
}
```
