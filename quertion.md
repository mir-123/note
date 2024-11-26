3. defred不知道
4. config不知道 
5. env 不知道
7. 用helper文件里已经有的方法
9. 对称加密
10. MD5

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


8.  
```cgo
input := struct {
    Project uint8 // 项目
    Status  uint8 // 状态
}{}
if err := c.ShouldBindQuery(&input); err != nil {
c.AbortWithStatusJSON(UnprocessableEntityHttpResponse(err.Error()))
return
} 
// 为啥不用
type input struct {
		Project uint8 // 项目
		Status  uint8 // 状态
	}
```