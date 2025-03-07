# 1. CRUD 基础介绍
    用于存储数据库的更新信息

## 1.1. 创建表
    在数据库中创建新表 并定义列的基本信息
```cgo
CREATE TABLE `sms_up` (                         // 创建了一个名为 sms_up 的表
  `id` int unsigned NOT NULL AUTO_INCREMENT,    // 自增的无符号整数类型主键，不为空。
  `task_id` int unsigned NOT NULL DEFAULT '0',  // 无符号整数类型，默认值为 0 ，不为空。
  `mobile` varchar(20) NOT NULL,                // 长度为 20 的非空字符串。
  `content` varchar(255) NOT NULL DEFAULT '',   // 长度为 255 的非空字符串，默认值为空字符串。
  `created` int unsigned NOT NULL DEFAULT '0',  // 无符号整数类型，默认值为 0 ，不为空。
  PRIMARY KEY (`id`)                            // 指定了 id 列为主键
);
```

## 1.2. 删除表
    删除表包括表中的所有数据和相关的约束、索引等
```cgo
DROP TABLE `resident_community_import`;     // 删除名为resident_community_import的表
```

## 1.3. 更新表
    对已有的表进行更新 包括添加列、删除列、修改列

```cgo
// 指定要操作的表为：media_file
ALTER TABLE `media_file`
// 在usertype 的列之后添加 deleted 列，数据类型为无符号整数，不允许为空，默认值为 0 
ADD COLUMN `deleted` int UNSIGNED NOT NULL DEFAULT 0 AFTER `usertype`;
// 删除名为 app_id 的列。
DROP COLUMN `app_id`,
// 修改card列的定义
MODIFY COLUMN `card` varchar(20) NULL DEFAULT NULL AFTER `name`;

// 修改列名：将列名 call_in_ok 换为 call_in ，同时将 call_out_ok 换为 call_out
UPDATE statistic_call SET call_in_ok = call_in, call_out_ok = call_out; 

// 更新表名：将原表名 old_table_name 改为 new_table_name
ALTER TABLE old_table_name RENAME TO new_table_name;
```


# 2. 常用方法
    GORM操作数据库

## 2.1. Create()
    向数据库中插入新的数据记录。

### 2.1.1. 基础创建
```cgo
// 文档中的案例：
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}
result := db.Create(&user) // 通过数据的指针来创建

user.ID             // 返回插入数据的主键
result.Error        // 返回 error
result.RowsAffected // 返回插入记录的条数
```

```cgo
// 项目中的案例
// 创建一个新的 Question 结构体实例 q ，并尝试将其插入到数据库中指定的表 tables.Question 中。
q := Question{
    Status:   input.Status,
    Answer:   input.Answer,
    Question: input.Question,
    Created:  time.Now().Unix(),
}
// 数据库连接 将 q 结构体的内容插入到数据库表中 通过数据的指针来创建
tx := database.DB.Table(tables.Question).Create(&q)
// 检查插入操作影响的行数小于 1 ，表示创建失败 
if tx.RowsAffected < 1 {
    c.AbortWithStatusJSON(AccessDeniedHttpResponse("创建失败"))
    return
}

// 定义结构体 根据orm框架的命名约定 将表中的列名初始化
type Question struct {
	ID       uint
	Status   uint8
	Answer   string
	Question string
	Created  int64
}

// 对应的数据库表名 如果不写此可能会导致数据库中表明不匹配
func (Question) TableName() string {
	return tables.Question
}
```

### 2.1.2. 创建多条记录

```cgo
// 案例
users := []*User{
    {Name: "Jinzhu", Age: 18, Birthday: time.Now()},
    {Name: "Jackson", Age: 19, Birthday: time.Now()},
}
result := db.Create(users)

```

## 2.2. CreateInBatches()
    批量插入
### 2.2.1. 使用案例
```cgo
// 官方举例
var users = []User{{Name: "jinzhu_1"}, ...., {Name: "jinzhu_10000"}}
// 100 指的是 每一批插入的数据 
// 如果 users 里有 101 条记录，首先会处理前 100 条记录作为第一批进行插入。然后剩下的 1 条记录会作为下一批（第二批）进行插入。
db.CreateInBatches(users, 100)
```
```cgo
// 项目代码
// 将数据批量存入con 确认数据没有报错后再将数据批量存入数据库
	con := make([]*tables.Sms, 0)
	now := time.Now().Unix()
	for i, phone := range input.Phone {
		temp, err = json.Marshal(input.Param[i])
		if err != nil {
			log.Println("BMS", err)
			continue
		}
		keyword := &tables.Sms{
			Mobile:       phone,              // 手机号
			TemplateCode: input.TemplateCode, // 短信模板Code
			Param:        string(temp),       // 短信模板参数
			SendTime:     now,                // 短信发送时间
			Status:       1,
			BizID:        *rsp.Body.BizId,
		}
		con = append(con, keyword)
	}
	if len(con) > 0 {
		database.DB.CreateInBatches(con, 1000)
	}
```

## 2.3. First()
    获取第一条记录(主键升序)
### 2.3.1. 使用案例
```cgo
// 官方举例
// 获取第一条记录（主键升序）
db.First(&user)
// SELECT * FROM users ORDER BY id LIMIT 1;
```
```cgo
// 项目代码
type row struct {
    tables.PointUser
    Goods struct {
        Name []string //返回可以兑换的积分商品名称 前端随机展示其中一个
    } `gorm:"-"`
    Expired struct {
        Num uint32 //返回可以兑换的积分商品名称 前端随机展示其中一个
    } `gorm:"-"`
}
ori := new(row) // new 函数来创建变量时，它返回的就是一个指针
database.DB.Where("user_id = ?", userID).First(ori) // 此处因为 ori本身已经是个指针了所以就不再需要"&"了
```
    

## 2.4. Take()
    随机获取一条记录，没有指定排序字段(其实应该是按顺序查到就返回了，所以相当于First了属于是)

### 2.4.1. 使用案例
```cgo
// 官方举例
// 获取一条记录，没有指定排序字段
db.Take(&user)
// SELECT * FROM users LIMIT 1;
```
```cgo
// 项目代码
ori := new(tables.CateTag)
database.DB.Where("id = ?", input.ID).Take(ori) // 随机获取匹配条件的一条记录。

id = 0
database.DB.Table(table).Where("`type`=?", input.Type).Select("id").Take(&id)
// Take 方法需要能够正确地被赋值给 id 变量
// 如果不使用指针，id 变量的值不会被 能够直接修改传递的变量来存储查询到的结果值。
// 使用指针 &id 可以确保查询得到的值Take 方法更新，导致判断结果不准确。

```
### 2.4.2. Take() 和 Find() 的区别
- 行为：         
Find() 用于查询匹配条件的所有记录，并将结果填充到给定的切片中。             
Take() 用于获取匹配条件的第一条记录。           

- 返回结果：        
Find() 返回匹配条件的一个记录切片。           
Take() 返回单个匹配的记录。

- 错误处理：           
如果没有找到匹配的记录，Find() 不会返回错误，只是返回一个空切片。            
Take() 如果没有找到匹配的记录，会返回一个错误。

总的来说，如果您只关心匹配条件的第一条记录，使用 Take() ；如果您想要获取所有匹配的记录，使用 Find() 。

    以上是网上查找到的解释 我这边总结来说的话呢 Find找到的是个数组对象：[{aa:11},{bb:22}]、Take找到的是个对象：{aa:11}

### 2.4.2. Take()获取指定数目的数据(未验证)
```cgo
// 官方举例
var latestPosts []Post
db.Model(&Post{}).Order("created_at DESC").Take(10).Find(&latestPosts)
// 在这个例子中，Order("created_at DESC") 指定了按照创建时间降序排列，而 Take(10) 则限制了查询结果只返回10条记录。
```

## 2.5. Last()
    获取最后一条记录（主键降序）
### 2.5.1. 使用案例
````cgo
// 官方举例
// 获取最后一条记录（主键降序）
db.Last(&user)
// SELECT * FROM users ORDER BY id DESC LIMIT 1
````

## 2.6. Update()
    用于更新单个字段的值。它需要指定要更新的字段和新的值
### 2.6.1. 使用案例
````cgo
// 举例
db.Model(&user).Update("name", "new_name")
````
```cgo
// 项目中案例 仅仅更新 mid
database.DB.Table(table).Where("id=?", oa.ID).Update("mid", oa.Mid) 
```

## 2.7. Updates()
    用于批量更新多个字段的值。它接受一个结构体或映射作为参数，其中包含要更新的字段和新的值。
### 2.7.1. 使用案例
````cgo
// 举例
db.Model(&user).Updates(User{Name: "new_name", Age: 20})
````
```cgo
// 项目中案例 更新表中的数据大于一个
up := struct {
    Status uint8  // 录制状态
    File   string // 录制视频的入口文件
}{123, "123456"}
tx := database.DB.Table(aa).Where("aaas=?", ss[0]).Updates(up)
```

## 2.8. Save()
    如果记录存在则更新，不存在则创建。它会根据主键的值来判断是更新还是创建。如果主键有值且在数据库中存在对应的记录，就执行更新操作；如果主键为空或者在数据库中没有对应的记录，就执行插入操作。
### 2.8.1. 使用案例
```cgo
// 项目中 好像很少用到 就用了一个ai的举例
row := new(tables.GroupCount)
database.DB.Table(tables.T_GroupCount).Where("group_id =? and date =?", v.RoomId, date).Take(&row)

newData := row
if row.ID == 0 {
    // 准备新增数据
    newData.GroupID = v.RoomId;
    newData.Date = date;
    newData.Count = 1;
} else {
    // 更新
    newData.Count++
}
database.DB.Save(&newData)
```

## 2.9. Clauses()
```cgo
database.DB.Table(liveRoom).Clauses(clause.Insert{Modifier: "IGNORE"}).Create(&l)
// 解释：Clauses(clause.Insert{Modifier: "IGNORE"}) 
// 表示设置插入语句的子句，Modifier: "IGNORE" 
// 通常用于在执行插入操作时，如果遇到重复的主键或唯一键值，忽略当前插入操作而不报错。

.Clauses(clause.OnConflict{
		Columns:   []clause.Column{{Name: "title"}},
		DoNothing: true,
	}).Create(&con)
// 解释：clause.OnConflict 用于处理在执行插入操作时可能出现的冲突情况
// Columns: []clause.Column{{Name: "title"}} 表示在冲突时，检查 title 列是否冲突
// DoNothing: true 表示如果发生冲突，则不执行任何操作，直接忽略冲突的记录
// 这里的 Create(&con) 表示创建 con 对象，并将其插入到数据库中

```

# 3. 构建、执行相关方法
    用于构建和执行复杂的数据库查询操作。

## 3.1. Where()
    用于添加查询条件到数据库操作中。可以接受不同类型的参数来构建查询条件。
### 3.1.2. 使用案例
```cgo
// 查找 name 字段等于 jinzhu 的用户
db.Where("name =?", "jinzhu").Find(&users)

// 根据结构体中的字段值构建条件
db.Where(&User{Name: "jinzhu", Age: 20}).Find(&users)

// 查找 name 字段值为 "jinzhu" 且 age 字段值为 20 的用户记录
db.Where(map[string]interface{}{"name": "jinzhu", "age": 20}).Find(&users)
```
```cgo
// 项目中使用的常常用字符串作为条件
user := new(tables.User)
database.DB.Where("mobile = ?", input.Mobile).Take(&user)
user.Name = input.Name // 此处将要修改 user 所以上面用到了指针 &user

tem := new(SmsTemplate)
database.DB.Where("id = ?", s.TemplateId).Take(tem)
num := tem.ParamsCount() // 此处仅仅是获取了tem 并没有对其进行修改 所以不必用指针
```

## 3.2. Select()
    从数据库中选择的字段
### 3.2.1. 使用案例
```cgo
// 从数据库中选择 name 和 age 字段，并将结果填充到 users 切片中
db.Select("name, age").Find(&users) 

// 计算记录的总数，并将结果命名为 total
db.Select("COUNT(*) as total").Find(&result)

// 从 User 表中选择 name 字段，从 Order 表中选择 order_number 字段
db.Model(&User{}).
    Select("users.name, orders.order_number").
    Joins("JOIN orders ON users.id = orders.user_id").
    Find(&results)
```
```cgo
// 项目中的用法
id = 0
database.DB.Table(table).Where("`type`=?", input.Type).Select("id").Take(&id)
// Take 方法需要能够直接修改传递的变量来存储查询到的结果值。使用指针 &id 可以确保查询得到的值能够正确地被赋值给 id 变量
```

## 3.3. Joins()
    用于在查询中添加关联条件，以连接多个表获取相关数据。      
    Joins 方法接受一个字符串参数，用于指定关联的表和关联条件。您可以使用标准的 SQL 风格的关联语法来编写这个字符串。

- JOIN 意味着只返回在连接条件下两个表中相互匹配的行（两个表中 指定列值相等的行）  
- LEFT JOIN 则会返回左表（即写在 LEFT JOIN 左边的表）中的所有行（所有数据），即使在右表中没有匹配的行
### 3.3.1. 使用案例
```cgo
// 项目中的用法

// left join 在这段代码中，uids 切片将会存储从数据库查询中获取的 user_id 字段的值。
uids := make([]uint, 0)
database.DB.Table(tables.T_Doctor+" as d").
    Joins("left join "+tables.T_User+" as u on d.user_id=u.id").  // 将T_Doctor作为左边的表，返回的将是T_Doctor的所有行
    Where("u.name = ? and d.hospital_id=?", i.DoctorName, hos).
    Select("user_id"). // 指定只选择 user_id 字段
    Limit(2). // 用于限制查询结果返回的行数为 2 行。也就是说，无论数据库中实际有多少匹配的记录，此查询只会返回最多 2 条记录。
    Find(&uids) // 将结果填充到 uids 切片中
    
// join 从数据库中查询并统计相关数据，按照特定条件进行连接、分组和字段选择，最终将结果存储在 con 切片中。
con := make([]count, 0)
database.DB.Table(tables.T_ResidentContract + " as ctr").
    Joins("join " + doctor + " as doc on ctr.doctor_id = doc.user_id"). // 只返回这两个表中ctr.doctor_id==doc.user_id的行
    Group("doc.hospital_id"). // 按照 doc.hospital_id 字段对结果进行分组
    Select("doc.hospital_id as id, count(ctr.doctor_id) as cot"). // 选择要返回的字段，将 doc.hospital_id 重命名为 id ，并计算 ctr.doctor_id 的数量并重命名为 cot
    Find(&con)
```

## 3.4. Preload()
    在查询主模型数据的同时，预先加载关联的模型数据。
    Preload 方法可以接受一个字符串参数来指定要预加载的关联关系名称。
    目前只能理解Preload比join让人感觉逻辑更清晰这一个优点（多层嵌套）
### 3.4.1. 使用案例
```cgo
// 项目中使用案例一： 
// 在查询主数据时，预先加载关联的 Doctor 数据
type row struct {
    tables.LiveDoctor
    Doctor struct {
        UserID           uint   `json:"-"` // 标签中出现 json:"-" 时，表示在将该结构体序列化为 JSON 时，这个字段将被忽略，不会出现在生成的 JSON 数据中
        HospitalProvince uint16 // 医院所在省份ID
        Name             string // 医生名
        Hospital         string // 医院名称
        Department       string // 部门名称
        Title            string // 职称: 主任医师
        Certificate      string // 证书图片地址
    } `gorm:"foreignKey:UserID;references:UserID"` // foreignKey:UserID 表示当前模型中的 UserID 字段是外键。references:UserID 表示这个外键引用的是另一个表（通常是 User 表）中的 UserID 字段。通过这种方式，GORM 能够理解表之间的关联关系，并在进行数据库操作（如查询关联数据、级联删除等）时正确处理这些关系
}
tx := database.DB.Preload("Doctor", func(db *gorm.DB) *gorm.DB {
    return db.Table(tables.T_HealthDoctor).Select("user_id,hospital_province,name,hospital,department,title,certificates")
}).Order("user_id desc")

// 使用案例二：
// 在查询主数据时，预先加载关联的 Doctor 数据
type row struct {
    tables.SeatUser // references:ID 表示这个外键引用了 tables.SeatUser 结构体中的 ID 字段
    Mobile []struct {
        tables.SeatTel // foreignKey:UserID 表示在 tables.SeatTel 结构体中，有一个名为 UserID 的字段被定义为外键
    } `gorm:"foreignKey:UserID;references:ID"`
    Cate []struct {
        tables.SeatCate
    } `gorm:"foreignKey:UserID;references:ID"`
}
rows := make([]row, 0)
if len(result) > 0 {
    database.DB.Table(tables.T_SeatUser).Where("id IN (?)", result).
        Preload("Mobile").
        Preload("Cate").
        Find(&rows)
    for i := 0; i < len(rows); i++ {
        rows[i].Card = helper.Anonymous(rows[i].Card, false)
    }
}

// 解释一下foreignKey和references
// foreignKey:UserID 表示当前模型中的 UserID 字段是外键。
// references:UserID 表示这个外键引用的是另一个表（通常是 User 表）中的 UserID 字段。
// 通过这种方式，GORM 能够理解表之间的关联关系，并在进行数据库操作（如查询关联数据、级联删除等）时正确处理这些关系

```

# 4. 数据库查询语句
    基本格式：
    `select + 列名（*代表所有的列） from + 表名 + where + 条件语句`

## 4.1. 执行过的命令：
```cgo

// call表中查找 tag 不为空的
SELECT * FROM `call` where tag IS NOT NULL AND tag != "";

// 查找指定的 call_id
SELECT * FROM `call` where call_id = 1734056538.204524;

```

# 5. 标签

## 5.1. gorm库的常见标签
> primaryKey: 将字段表记为主键         
> unique: 使字段具有唯一性约束         
> not null: 字段不允许为空       
> default:value : 字段默认值             
```cgo
package main

import (
    "fmt"
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

type User struct {
    gorm.Model
    ID        uint   `gorm:"primaryKey"` // 将 ID 字段标记为主键
    Name      string `gorm:"not null"`   // 名称字段不允许为空
    Age       int    `gorm:"default:18"` // 年龄字段默认值为 18
    Email     string `gorm:"unique"`      // 邮箱字段具有唯一性
    ignore    string `gorm:"-"`           // 忽略该字段
}

func main() {
    dsn := "your_database_dsn"
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err!= nil {
        panic("failed to connect database")
    }
    defer db.Close()

    // 自动迁移模式创建表
    db.AutoMigrate(&User{})

    // 创建用户
    user := User{Name: "John"}
    db.Create(&user)

    var foundUser User
    db.First(&foundUser, user.ID)
    fmt.Println(foundUser)
}
```

## 5.2. encoding/json 包的常见标签类型
> string：强制将字段编码为字符串。            
> -：在 JSON 编码时忽略该字段     
```cgo
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name    string `json:"name"`         // 编码为 JSON 时使用 "name" 作为键
    Age     int    `json:"age,omitempty"` // 将Age在json状态下表示为age 为 0 则在 JSON 中省略
    Address string `json:"-"`            // 编码为 JSON 时忽略该字段
}

func main() {
    p1 := Person{Name: "Alice", Age: 25}
    p2 := Person{Name: "Bob", Age: 0, Address: "Somewhere"}

    jsonData1, _ := json.Marshal(p1)
    jsonData2, _ := json.Marshal(p2)

    fmt.Println(string(jsonData1)) // {"name":"Alice","age":25}
	fmt.Println(string(jsonData2)) // {"name":"Bob"}
}
```