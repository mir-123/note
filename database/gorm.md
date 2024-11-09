# 1. 数据类型
    Go 对每个原始数据类型都有默认值。int -> 0, string -> "", bool -> false 同样。所以如果需要添加空值，或者将空值加载到一个变量中，它应该是一个指针。指针的默认值为 nil，而复杂的数据类型，如slice、map，则保留引用。所以它们的默认值为 nil。

## 1. 字符串类型
```cgo
// 举例
res := struct {
		Content string // 一个常规字符串字段
		Reason  *string // 一个指向字符串的指针, allowing for null values
		Remark  sql.NullString // Uses sql.NullString to handle nullable strings
	}
```
### 1. string
    可以是一个空字符串，但不能为nil，使用时比较安全 （字符串类型）
### 2. *string
    可以为nil，使用的时候需要判断是否有值（字符串指针类型）
### 3. sql.NullString(未使用)
    sql.NullString 是 Go 的标准库 database/sql 中的一个类型，它用于处理数据库中可能为 NULL 的字符串类型。当字段值为 NULL 时，sql.NullString 的 Valid 字段为 false，而 String 字段为空字符串。（目前项目总没有用到过）
