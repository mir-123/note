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

# 2. 为什么时间戳要用 int64

- 范围足够大：int64 能够表示的数值范围足够涵盖大多数常见的时间戳范围，从 1970 年 1 月 1 日（Unix 纪元）到很久的未来。
- 精度和准确性：可以准确表示到微秒甚至纳秒级别的时间精度，满足大多数应用对时间精度的要求。
- 跨平台一致性：int64 在不同的操作系统和硬件架构上具有一致的行为和表示，使得代码在不同环境中的行为更可预测。
- 方便计算和比较：整数类型在进行时间的计算（如时间差）和比较操作时通常更直观和高效。
- 与数据库和网络通信的兼容性：许多数据库和网络协议也常使用 64 位整数来表示时间戳，方便数据的存储、传输和转换。

# 3. 为什么用到ip代理

1. 突破访问限制：某些网站或服务可能对特定地区的 IP 有限制，使用代理可以绕过这些限制。
2. 隐藏真实身份：用于保护个人隐私，防止被追踪或识别。
3. 网络爬虫和数据采集：避免因频繁请求被目标网站封禁真实 IP。

## vpn代理和ip代理的区别
VPN 代理和 IP 代理有以下一些区别：

1. 功能范围：
- VPN 代理通常会对整个设备的网络连接进行加密和重定向，使得所有的网络流量都通过 VPN 服务器进行传输。
- IP 代理主要是改变特定网络请求的源 IP 地址，并非对整个设备的网络流量进行处理。
2. 安全性：
- VPN 提供了更全面的网络安全保护，包括数据加密、防止流量监控等。
- IP 代理的安全性相对较局限，主要集中在隐藏 IP 方面。
3. 网络访问：
- 使用 VPN 可能会影响网络速度，因为所有数据都需要经过远程服务器中转。
- 一些简单的 IP 代理可能对网络速度的影响较小，但效果可能不太稳定。
4. 合法性：
- 在合法的用途和规定下，企业或个人可以使用 VPN。但在中国，私自搭建和使用未经批准的 VPN 是违法的。
- 合法的 IP 代理在符合规定的情况下可以使用，但非法的 IP 代理服务通常是被禁止的。
5. 应用场景：
- VPN 常用于远程办公、保护隐私、突破地域限制访问特定资源等。
- IP 代理更多地用于网络爬虫、部分游戏或特定网站的访问需求。
总之，虽然 VPN 代理和 IP 代理都能在一定程度上改变网络连接的特征，但它们在功能、安全性、合法性和应用场景等方面存在差异。

# 4. 一些常用到的操作
常用的操作写在此处是为了方便写到类似的函数还要到处找代码浪费时间
以下代码以家医后台黑名单页面为例

## 4.1. 数据库增删改表
```cgo
// 创建表
CREATE TABLE `blacklist` (
`mobile` varchar(11) NOT NULL,
`type` tinyint unsigned NOT NULL DEFAULT '0',
`adm_id` int unsigned NOT NULL,
`name` varchar(50) NOT NULL DEFAULT '',
`card` varchar(18) NOT NULL DEFAULT '',
`remark` varchar(255) NOT NULL DEFAULT '',
`created` int unsigned NOT NULL DEFAULT '0',
PRIMARY KEY (`mobile`)
) ENGINE=InnoDB;

// 在表里添加某个元素
AlTER TABLE `adm_user`
ADD COLUMN `status` tinyint unsigned NOT NULL DEFAULT 1 After `privilege`;

// 在表里删除某个元素
ALTER TABLE `work_group`
DROP COLUMN `doctor_message_count`,
```

## 4.2. 定义表、给表中的数据设置数据结构
```cgo
// 黑名单
const T_BlackList = "blacklist"

const (
	BlackListType      = iota // 【默认】
	BlackListTypeDie          // 去世
	BlackListTypeQuit         // 退订
	BlackListTypeMove         // 搬迁
	BlackListTypeOther        // 其他
)

type BlackList struct {
	AdmID   uint   // 操作人
	Type    uint8  // 类型
	Name    string // 姓名
	Mobile  string // 手机号 primaryKey
	Card    string // 身份证号
	Remark  string // 备注
	Created int64
}

func (BlackList) TableName() string {
	return T_BlackList
}
```

## 4.3. 定义增删改查的函数
```cgo
// 接口对应的路由
admData.POST("black", controllers.BlackCreateAdm)
admData.GET("black", controllers.BlackIndexAdm)
admData.PUT("black", controllers.BlackUpdateAdm)
admData.DELETE("black", controllers.BlackDeleteAdm)
```
```cgo
// 新增黑名单用户
func BlackCreateAdm(c *gin.Context) {
	input := struct {
		Mobile string `binding:"required"` // 手机号
		Type   uint8  `binding:"required"` // 所属类型
		Name   string // 姓名
		Card   string // 身份证号
		Remark string // 备注
	}{}
	err := c.ShouldBindJSON(&input)
	if err != nil {
		c.AbortWithStatusJSON(UnprocessableEntityHttpResponse(err.Error()))
		return
	}

	rec := new(tables.BlackList)
	database.DB.Table(tables.T_BlackList).Where("mobile = ?", input.Mobile).Take(&rec)
	if rec.Mobile != "" {
		c.AbortWithStatusJSON(AccessDeniedHttpResponse("该手机号已存在"))
		return
	}

	black := tables.BlackList{
		AdmID:   GetRequestAdmin(c).ID,
		Mobile:  input.Mobile,
		Type:    input.Type,
		Name:    input.Name,
		Card:    input.Card,
		Remark:  input.Remark,
		Created: time.Now().Unix(),
	}
	tx := database.DB.Create(&black)
	if tx.RowsAffected < 1 {
		c.AbortWithStatusJSON(AccessDeniedHttpResponse("添加失败"))
		return
	}
}

// 获取黑名单用户
func BlackIndexAdm(c *gin.Context) {
	input := struct {
		Name   string
		Mobile string
		Card   string
		Page   string
	}{}
	if err := c.ShouldBindQuery(&input); err != nil {
		c.AbortWithStatusJSON(UnprocessableEntityHttpResponse(err.Error()))
		return
	}

	blackArr := make([]tables.BlackList, 0)
	tx := database.DB.Table(tables.T_BlackList)
	if input.Name != "" {
		tx.Where("name = ?", input.Name)
	}
	if input.Mobile != "" {
		tx.Where("mobile = ?", input.Mobile)
	}
	if input.Card != "" {
		tx.Where("card = ?", input.Card)
	}
	tx.Find(&blackArr)
	// 没有操作日志要返回空数组 故此处不做判空
	paginate := Paginate(tx, &blackArr, input.Page, 20)
	c.JSON(http.StatusOK, paginate)
}

// 修改黑名单用户
func BlackUpdateAdm(c *gin.Context) {
	input := struct {
		Mobile string `binding:"required"` // 手机号
		Type   uint8  // 所属类型
		Name   string // 姓名
		Card   string // 身份证号
		Remark string // 备注
	}{}
	err := c.ShouldBindJSON(&input)
	if err != nil {
		c.AbortWithStatusJSON(UnprocessableEntityHttpResponse(err.Error()))
		return
	}

	tx := database.DB.Table(tables.T_BlackList).Where("mobile = ?", input.Mobile).Updates(input)
	if tx.RowsAffected < 1 {
		c.AbortWithStatusJSON(AccessDeniedHttpResponse("更新失败, 请重试"))
		return
	}
}

// 删除黑名单用户
func BlackDeleteAdm(c *gin.Context) {
	input := struct {
		Mobile string `binding:"required"` // 手机号
	}{}
	err := c.ShouldBindJSON(&input)
	if err != nil {
		c.AbortWithStatusJSON(UnprocessableEntityHttpResponse(err.Error()))
		return
	}

	tx := database.DB.Table(tables.T_BlackList).Where("mobile = ?", input.Mobile).Delete(nil)
	if tx.RowsAffected < 1 {
		c.AbortWithStatusJSON(AccessDeniedHttpResponse("黑名单删除失败"))
		return
	}
}
```