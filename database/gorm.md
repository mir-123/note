# 1. update.sql文件
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




# 2. 操作数据库
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

## 2.2. Select()
    指定查询某些字段

## 2.3. Pluck()
    指定查询某个字段
    