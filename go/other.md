# 1. 两种定义结构体方法的区别
```cgo
input := struct {
    Project uint8 // 项目
    Status  uint8 // 状态
}{}
// 为啥不用
type input struct {
		Project uint8 // 项目
		Status  uint8 // 状态
	}
	
// 也可以这样 但是 一般不这么用 因为 type 开头的 一般是为可以直接用到这个类型
// 也可以说 下边这种设计方法是 直接定义了一种类似 string int 这样的类型
type name struct{  
    one string 
    two uint
} 
rows := make([]name, 0) // name类型可以直接在定义数组时用到

// 而以下的则是 定义了一个 值为初始值的对象
name := struct{  
    one string 
    two uint
}{} // 这样就类似于 name.one = "";name.two = 0
```