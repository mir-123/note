
6. `gorm:"-" json:"Expired,omitempty"`

7. 搞不懂Take后面的指针到底是咋回事 现在看起来是带上&就没啥问题不带上可能会不对

```cgo

user := new(tables.User)
database.DB.Where("mobile = ?", input.Mobile).Take(&user)

tem := new(SmsTemplate)
database.DB.Where("id = ?", s.TemplateId).Take(tem)

id = 0
database.DB.Table(table).Where("`type`=?", input.Type).Select("id").Take(&id)
```

2. Preload 不懂
```cgo
type row struct {
		tables.SeatUser
		Mobile []struct {
			tables.SeatTel
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
```