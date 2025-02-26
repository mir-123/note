

7. 搞不懂Take后面的指针到底是咋回事 现在看起来是带上&就没啥问题不带上可能会不对

```cgo

user := new(tables.User)
database.DB.Where("mobile = ?", input.Mobile).Take(&user)

tem := new(SmsTemplate)
database.DB.Where("id = ?", s.TemplateId).Take(tem)

id = 0
database.DB.Table(table).Where("`type`=?", input.Type).Select("id").Take(&id)
```



2. First()的报错机制

```cgo
database.DB.Table(tables.T_ExpertUserDetail).Where("user_id = ?", input.UserID).Select("id").Order("id desc").Take(&detailID)

为什么用Take可以 但是用First就会报错
```