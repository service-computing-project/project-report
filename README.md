# 项目报告

|   课程名称    |  服务计算  |   任课老师    |    潘茂林    |
| :-----------: | :--------: | :-----------: | :----------: |
|   **年级**    | **2018级** |   **专业**    | **软件工程** |
| **成员1姓名** | **孙意林** | **成员1学号** | **18342088** |
| **成员2姓名** |  **李佳**  | **成员2学号** | **18342048** |
| **成员3姓名** | **孙浩男** | **成员3学号** | **18342087** |
| **成员4姓名** |            | **成员4学号** |              |
| **成员5姓名** |            | **成员5学号** |              |
| **开始日期**  | 2020.12.8  | **完成日期**  |  2020.12.22  |

## 项目名称

xx博客

## 开发与运行环境

### 前端



### 后端

操作系统：Ubuntu 16.04/20.04

编程环境：go version>=1.13.8

数据库：MongoDB

## 小组分工

### 前端

| 姓名 | 工作 |
| :--: | :--: |
|      |      |
|      |      |



### 后端

|  姓名  |                             工作                             |
| :----: | :----------------------------------------------------------: |
| 孙意林 | 构建数据库，完成数据库服务器、后端服务器的部署，完成后端底层数据库操作框架的构建与具体实现，Travis CI 部署，撰写实验报告 |
|  李佳  | 服务端架构（路由，模型）、API设计及文档撰写、User、Like模块的控制器逻辑、API root 获取简单 API 服务列表 |
| 孙浩男 | Content、Notification模块控制器逻辑，基于jwt的Token实现与验证，撰写实验报告 |

## 项目部署说明

### 前端



### 后端

后端部署使用go get，具体命令如下：

```bash
> go get -insecure github.com/service-computing-project/project_app
> $GOPATH/bin/project_app
```

### 数据库部署

后端中集成了一个阿里云服务器上的数据库，如需修改数据库，请修改service中`database.go`文件

## API说明

以下是API的概览：

- User
  - 用户注册：用于用户信息的注册和写入数据库
  - 用户登陆：使用用户名和密码登陆入系统
  - 退出登陆：退出当前使用的用户名和密码
  - 更新用户名：用于更新用户名，只能在登陆后使用
  - 获取用户信息：通过用户ID获取用户信息，当为self时获取自己用户信息
- Content
  - 获取指定内容：通过文章ID获取指定文章信息
  - 删除指定内容：通过文章ID删除指定内容
  - 获取公共内容：获取所有设置为公共信息的内容和信息的发送者
  - 获取指定用户所有内容：通过用户ID获取指定用户的公共内容，self时获取自己的全部内容
  - 增加文本内容：用于用户发布自己的文章
- Like
  - 获取点赞列表：通过文章ID获取给这篇文章点赞的用户名
  - 对某个内容点赞：通过文章ID对其进行点赞
  - 取消点赞：通过文章ID取消对其的点赞
- Notification
  - 获取用户所有通知：通过用户ID获取其所有通知
  - 删除某条通知：通过通知ID删除该通知

更详细的设计方案详见[API文档](./API.md)

## 前端详细说明





## 后端详细说明

### 数据库

#### 连接数据库

连接数据库只需要使用`mgo.Dial`进行连接然后设置连接模式即可

```go
func DBservice() (*mgo.Session, error) {
	session, err := mgo.Dial("mongodb://47.103.210.109:27017")
	if err != nil {
		fmt.Println(err)
		return nil, err
	}
	session.SetMode(mgo.Monotonic, true)

	return session, nil
}
```

#### 设置的数据库

本项目中共使用了四个数据库

- 用户数据库

```go
type User struct {
	ID           bson.ObjectId `bson:"_id"`          // 用户ID
	Pwd          string        `bson:"password"`     //用户密码
	Email        string        `bson:"email"`        // 用户唯一邮箱
	Info         UserInfo      `bson:"info"`         // 用户个性信息
	LikeCount    int64         `bson:"likeCount"`    // 被点赞数
	ContentCount int64         `bson:"contentCount"` // 内容数量
}
```

- 文章数据库

```go
type Content struct {
	ID          bson.ObjectId `bson:"_id"`
	Detail      string        `bson:"detail"`      // 详情介绍
	OwnID       bson.ObjectId `bson:"ownId"`       // 作者ID [索引]
	PublishDate int64         `bson:"publishDate"` // 发布日期
	LikeNum     int64         `bson:"likeNum"`     // 点赞数
	Public      bool          `bson:"public"`      // 是否公开
	Tag         []string      `bson:"tag"`         // 标签
}
```

- 点赞数据库

```go
type Like struct {
	ID        bson.ObjectId `bson:"_id"`
	UserID    bson.ObjectId `bson:"userId"`    // 用户ID
	ContentID bson.ObjectId `bson:"contentId"` // 内容ID
}
```

- 通知数据库

```go
type NotificationDetail struct {
	ID         bson.ObjectId `bson:"_id"`
	CreateTime int64         `bson:"time"`
	Content    string        `bson:"content"`   // 通知内容
	SourceID   bson.ObjectId `bson:"sourceId"`  // 源ID （点赞用户）
	TargetID   bson.ObjectId `bson:"targetId"`  // 目标ID （被点赞用户）
	ContentID  bson.ObjectId `bson:"contentId"` //点赞文章ID
	Type       string        `bson:"type"`      // 类型：暂时只有like
}
```



### 数据库操作

- 增加数据：`m.DB.Insert`
- 修改数据：`m.DB.UpdateId`
- 删除数据：`m.DB.RemoveId`
- 查找数据
  - 通过ID：`m.DB.FindId`
  - 通过选择器：`m.DB.Find`

### 控制器
>controller中实现对后端接受到不同Method和Path请求的处理，主要操作包括session验证、Token签发和验证、用户信息判断、设置请求的返回信息等。分为User、Content、Like、Notification四个模块
>

#### User
>User中实现对于用户注册、登录、更改用户名、获取个人信息等请求的处理，同时增加对于OPTIONS域验证请求的支持

主要请求处理过程如下：
- 用户注册 
  - 用户注册的跨域验证 OPTIONS /user/register
    - 返回请求成功的200信息码
  - 用户注册 POST /user/register 
    - 读取Request中的Body的json参数
    - 使用用户请求信息调用model中的方法进行用户注册
    - 根据注册成功与否返回success等信息


```go
// RegisterReq POST /user/register 注册请求
type RegisterReq struct {
	Name     string `json:"username"`
	Email    string `json:"email"`
	Password string `json:"password"`
}

//OptionsRegister OPTIONS /user/register 用户注册
func (c *UsersController) OptionsRegister() {
	return
}

//PostRegister POST /user/register 用户注册
func (c *UsersController) PostRegister() (res models.CommonRes) {
	req := RegisterReq{}
	if err := c.Ctx.ReadJSON(&req); err != nil {
		res.State = models.StatusBadReq
	}
	if err := c.Model.Register(req.Name, req.Password, req.Email); err != nil {
		res.State = err.Error()
	} else {
		//c.Session.Set("name", req.Name)
		res.State = models.StatusSuccess
	}
	return
}
```


- 用户登录
  - 用户登陆的跨域验证 Options /user/login 
    - 返回请求成功的200信息码
  - 登陆请求 POST /user/login 
    - 读取Request中body的json参数
    - 根据用户请求中的用户名和密码信息调用model中的Login检查是否存在
    - 签发Token和设置Session的用户ID信息
    - 根据登陆成功与否返回状态信息及Token

>**Token签发**：
>用户的Token在登录时签发，用于之后获取部分API信息时的身份验证、由客户端Cookie存储，Token在签发时设置了其中对应的用户信息以及有效时长，返回值为加密格式，由服务端接受并使用服务端私钥解密和验证Token信息

```go
// LoginReq POST /user/login 登陆请求
type LoginReq struct {
	Name     string `json:"username"`
	Password string `json:"password"`
}

//OptionsLogin Options /user/login 用户登陆
func (c *UsersController) OptionsLogin() {
	return
}

//PostLogin POST /user/login 用户登陆
func (c *UsersController) PostLogin() (res models.CommonRes) {
	req := LoginReq{}
	err := c.Ctx.ReadJSON(&req)
	if err != nil || req.Name == "" || req.Password == "" {
		res.State = models.StatusBadReq
		return
	}
	userID, err := c.Model.Login(req.Name, req.Password)
	if err != nil {
		res.State = err.Error()
	} else {
		token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
			"name":     req.Name,
			"password": req.Password,
			"exp":      time.Now().Add(time.Hour * 1).Unix(),
		})
		// 这里的密钥和前面的必须一样
		tokenString, _ := token.SignedString([]byte("My Secret"))
		res.Data = tokenString
		c.Session.Set("myid", userID)
		fmt.Println(c.Session)
		res.State = models.StatusSuccess
	}
	return
}
```

- 用户登出：
  - 退出登陆 POST /user/logout 
    - 根据Session检查用户是否处于登录状态
    - 删除对应的Session信息
    - 根据登出操作的成功与否回执

```go
//PostLogout POST /user/logout 退出登陆
func (c *UsersController) PostLogout() (res models.CommonRes) {
	fmt.Println(c.Session)
	if c.Session.Get("myid") == nil {
		res.State = models.StatusNotLogin
		return
	}
	c.Session.Delete("myid")
	res.State = models.StatusSuccess
	return
}
```

- 更新用户名
	- 更新用户名的跨域请求 OPTIONS /user/name
		- 返回成功的200信号码
	- 更新用户名 POST /user/name  (Token required)
		- 首先检查用户的Session的id项是否为空来判断用户的登陆状态
		- 之后获取Request的Header部分的Token信息，解码并查看信息是否有效
		- 验证通过后调用model函数更新用户名
		- 根据执行成功情况返回状态

```go
//NameReq POST /user/name 更新用户名
type NameReq struct {
	Name string `json:"name"`
}

//OptionsName OPTIONS /user/name
func (c *UsersController) OptionsName() {
	return
}

//PostName POST /user/name 更新用户名 (Token required)
func (c *UsersController) PostName() (res models.CommonRes) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
		return
	}
	token, err := request.ParseFromRequest(c.Ctx.Request(), request.AuthorizationHeaderExtractor,
		func(token *jwt.Token) (i interface{}, e error) {
			return []byte("My Secret"), nil
		})

	if err != nil || !token.Valid {
		res.Data = err.Error()
		res.State = models.StatusBadReq
		return
	}
	req := NameReq{}
	err := c.Ctx.ReadJSON(&req)
	if err != nil || req.Name == "" {
		res.State = models.StatusBadReq
		return
	}
	err = c.Model.SetUserName(c.Session.GetString("id"), req.Name)
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

- 获取用户信息
	 - 获取用户信息 GET /user/info/{userID:string}
	 	- 首先根据参数self进行转换
		- 验证session来判断登陆状态
		- 调用model函数来从数据库获取对应信息
		- 将获得信息回执给请求者

```go
//GetInfo GET /user/info/{userID:string}
func (c *UsersController) GetInfoBy(id string) (res models.UserInfoRes) {
	if id == "self" {
		if c.Session.Get("id") == nil {
			res.State = models.StatusNotLogin
			return
		}
		id = c.Session.GetString("id")
	} else if !bson.IsObjectIdHex(id) {
		res.State = models.StatusBadReq
		return
	}
	userinfores, err := c.Model.GetUserInfo(id)
	res = userinfores
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

#### Content

>Content中包含用户发送的博客内容信息的请求处理，主要涉及了内容的分页显示、Session的验证、Token验证、跨域OPTIONS请求处理等
>

- 浏览器跨域OPTIONS请求
	- 使用统一的Options函数处理
	- 使用统一的OptionsBy函数处理

>由于Content下的请求的Path均为/api/content/*或/api/content/{*}，所以只需要实现两个对应的Options请求，即可解决所有请求的跨域OPTIONS域请求

```go
//Options options
func (c *ContentController) Options() {
	return
}

//OptionsBy options
func (c *ContentController) OptionsBy(contentID string) {
	return
}
```

- 获取指定内容
	- 获取指定内容 -GET /api/content/detail/{contentID:string} 
		- 根据用户的Session来判断登录状态
		- 调用model的函数来从数据库中获取对应内容信息
		- 返回信息以及请求状态


```go
//GetDetailBy GET /api/content/detail/{contentID:string} 获取指定内容
func (c *ContentController) GetDetailBy(contentID string) (res models.ContentDetailres) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
	}
	if contentID == "" {
		res.State = models.StatusBadReq
		return
	}
	contentdetailres, err := c.Model.GetDetailByID(contentID)
	res = contentdetailres
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

- 删除指定内容
	- 删除指定内容 DELETE /api/content/{contentID:string} 
		- 首先判断Session来获取登录状态
		- 之后判断删除内容的ID是否合法
		- 获取Request中Header的Token，并解码判断是否过期
		- 调用model的函数从数据库中删除对应信息
		- 返回执行情况success等 

```go
//DeleteBy DELETE /api/content/{contentID:string}  删除指定内容
func (c *ContentController) DeleteBy(contentID string) (res models.CommonRes) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
	}
	if contentID == "" {
		res.State = models.StatusBadReq
		return
	}
	token, err := request.ParseFromRequest(c.Ctx.Request(), request.AuthorizationHeaderExtractor,
		func(token *jwt.Token) (i interface{}, e error) {
			return []byte("My Secret"), nil
		})

	if err != nil || !token.Valid {
		res.Data = err.Error()
		res.State = models.StatusBadReq
		return
	}
	err = c.Model.RemoveContent(contentID)
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

- 获取公共内容
	- 获取公共内容 GET /api/content/public
		- 根据session获取登录情况
		- 获取Request的body参数来读取分页信息
		- 根据分页信息来从数据库筛选数据
		- 返回公共内容和状态信息  

```go
type PageParams struct {
	Page    int `url:"page"`
	PerPage int `url:"per_page"`
}

//GetPublic GET /api/content/public  获取公共内容
func (c *ContentController) GetPublic() (res models.ContentPublicList) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
	}
	params := PageParams{}
	err := c.Ctx.ReadQuery(&params)
	if err != nil && !iris.IsErrPath(err) {
		res.State = models.StatusBadReq
		return
	}
	if params.Page < 1 || params.PerPage < 1 {
		res.State = models.StatusBadReq
		return
	}
	contentpublicres, err := c.Model.GetPublic(params.Page, params.PerPage)
	res = contentpublicres
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}

```

- 获取用户的全部发布内容
	- 获取指定用户的所有内容 GET /api/content/usercontent/{userID:string} 
		- 首先读取请求的参数userID和body中的分页信息
		- 根据分页信息来查询数据，调用model的ReadQuery
		- 根据userID来判断读取自身的内容还是他人内容(决定了是否获取private消息)
		- 返回获得的数据和状态信息

> 实现了API的分页功能


```go 
//GetUsercontentBy GET /api/content/usercontent/{userID:string} 获取指定用户的所有内容
func (c *ContentController) GetUsercontentBy(userID string) (res models.ContentListByUser) {
	var contentlistbyuserres models.ContentListByUser
	var err error
	params := PageParams{}
	err = c.Ctx.ReadQuery(&params)
	if err != nil && !iris.IsErrPath(err) {
		res.State = models.StatusBadReq
		return
	}
	if params.Page < 1 || params.PerPage < 1 {
		res.State = models.StatusBadReq
		return
	}
	if userID == "" {
		res.State = models.StatusBadReq
		return
	} else if userID == "self" {
		if c.Session.Get("id") == nil {
			res.State = models.StatusNotLogin
			return
		}
		userID = c.Session.GetString("id")
		contentlistbyuserres, err = c.Model.GetContentSelf(userID, params.Page, params.PerPage)
	} else {
		contentlistbyuserres, err = c.Model.GetContentByUser(userID, params.Page, params.PerPage)
	}

	res = contentlistbyuserres
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

- 发布文本Content
	- 增加文本内容的跨域OPTIONS处理  OPTIONS /api/content/text
		- 返回正确的发布信息即可 
	- 增加文本内容  POST /api/content/text 
		- 首先根据session判断登陆状态
		- 进行token的解码和检查
		- 调用model中的函数发布新的Content
		- 返回状态信息

```go
//TextReq POST /api/content/text 增加文本内容
type TextReq struct {
	Detail   string   `json:"detail"`
	Tags     []string `json:"tags"`
	IsPublic bool     `json:"isPublic"`
}

//OptionsText  OPTIONS /api/content/text 增加文本内容
func (c *ContentController) OptionsText() {
	return
}

//PostText  POST /api/content/text 增加文本内容
func (c *ContentController) PostText() (res models.CommonRes) {
	id := c.Session.Get("id")
	if id == nil {
		res.State = models.StatusNotLogin
		return
	}
	//token check
	token, err := request.ParseFromRequest(c.Ctx.Request(), request.AuthorizationHeaderExtractor,
		func(token *jwt.Token) (i interface{}, e error) {
			return []byte("My Secret"), nil
		})

	if err != nil || !token.Valid {
		res.Data = err.Error()
		res.State = models.StatusBadReq
		return
	}
	req := TextReq{}
	err := c.Ctx.ReadJSON(&req)
	if err != nil || req.Detail == "" {
		res.State = models.StatusBadReq
		return
	}
	err1 := c.Model.AddContent(req.Detail, req.Tags, id.(string), req.IsPublic)
	if err1 != nil {
		res.State = err1.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```


- 更新文本
	 - 首先进行跨域的OPTIONS处理 OPTIONS /api/content/update
	  	- 返回正确的200信息码
	- 更新文本内容 POST /api/content/update
		- 与发布文本的操作基本相同
		- 参数增加了一个ContentID，并且在查询操作中增加了对于Content的OwnID的判断
		- 返回正确的状态信息

```go
//TextUpdateReq 文本内容
type TextUpdateReq struct {
	ID       string   `json:"contentID"`
	Detail   string   `json:"detail"`
	Tags     []string `json:"tags"`
	IsPublic bool     `json:"isPublic"`
}

//OptionsUpdate OPTIONS /api/content/update 更新文本内容
func (c *ContentController) OptionsUpdate() {
	return
}

//PostUpdate  POST /api/content/update 更新文本内容
func (c *ContentController) PostUpdate() (res models.CommonRes) {
	id := c.Session.Get("id")
	if id == nil {
		res.State = models.StatusNotLogin
		return
	}
	//token check
	token, err := request.ParseFromRequest(c.Ctx.Request(), request.AuthorizationHeaderExtractor,
		func(token *jwt.Token) (i interface{}, e error) {
			return []byte("My Secret"), nil
		})

	if err != nil || !token.Valid {
		res.Data = err.Error()
		res.State = models.StatusBadReq
		return
	}
	req := TextUpdateReq{}
	err := c.Ctx.ReadJSON(&req)
	if err != nil || req.Detail == "" {
		res.State = models.StatusBadReq
		return
	}
	err1 := c.Model.UpdateContent(req.ID, req.Detail, req.Tags, id.(string), req.IsPublic)
	if err1 != nil {
		res.State = err1.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```


#### like

>Like Controller主要实现点赞功能，以及个人喜爱列表，使用了Session来进行用户验证

- 获取用户的点赞列表
	- 验证id是否存在
	- 调用model中的函数获取个人的点赞信息
	- 返回点赞信息和状态

```go
// GetBy Get /like/{contentID} 获取用户点赞列表
func (c *LikeController) GetBy(id string) (res LikeRes) {
	if !bson.IsObjectIdHex(id) {
		res.State = models.StatusBadReq
		return
	}
	var err error
	res.Data, err = c.Model.GetUserListByContentID(id)
	if err != nil {
		res.State = err.Error()
	}
	res.State = models.StatusSuccess
	return
}
```

- 对内容点赞
	- 对某个内容点赞的OPTIONS域请求 Options /like/{contentID}
		- 返回正确的200信息码
	- 对内容点赞  POST /like/{contentID}
		- 根据Session来判断登录状态以及ID的有效性
		- 调用model的函数对内容进行点赞
		- 根据操作状态返回回执

```go
//Options Options /like/{contentID} 对某个内容点赞
func (c *LikeController) OptionsBy(id string) {
	c.Ctx.StatusCode(http.StatusOK)
	fmt.Println("hahahah")
	return
}

//​PostBy POST /like/{contentID} 对某个内容点赞
func (c *LikeController) PostBy(id string) (res models.CommonRes) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
		return
	}
	if !bson.IsObjectIdHex(id) {
		res.State = models.StatusBadReq
		return
	}
	err := c.Model.LikeByID(id, c.Session.Get("id").(string))
	if err != nil {
		res.State = err.Error()
	}
	res.State = models.StatusSuccess
	return
}
```

- 取消点赞
	- 取消用户对某个内容的点赞 PATCH /like/{contentID} 
		- 获取Session判断用户的登录状态
		- 判断id的有效性
		- 调用model的函数来取消点赞
		- 返回函数执行的状态信息

```go
//​ PatchBy PATCH /like/{contentID} 取消用户对某个内容的点赞
func (c *LikeController) PatchBy(id string) (res models.CommonRes) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
		return
	}
	if !bson.IsObjectIdHex(id) {
		res.State = models.StatusBadReq
		return
	}
	err := c.Model.CancelLikeByID(id, c.Session.Get("id").(string))
	if err != nil {
		res.State = err.Error()
	}
	res.State = models.StatusSuccess
	return
}
```

#### Notification
> Notification用户获取用户的所有通知，也就是别人对你的点赞情况以及删除通知，使用Session来进行用户判断，并且在main函数中使用jwt中间件来对对应的Notification的Path进行Token验证，所以Controller中就不需要再次验证

- 获取用户所有通知
	- 获取用户所有通知 GET /api/notification/all
		- 根据Session来判断用户的个人登录状态和个人ID
		- 调用model中的函数来获取个人Notification
		- 将Notification和状态信息返回

```go
//GetAll GET /api/notification/all  获取用户所有通知
func (c *NotificationController) GetAll() (res models.UserNotificationres) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
		return
	}
	id := c.Session.GetString("id")
	notificationres, err := c.Model.GetNotificationByUserID(id)
	res = notificationres
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return
}
```

- 删除指定通知
	- 删除通知 DELETE /api/notificaiton/{NotificationID:string}
		- 获取用户的session来判断登陆状态
		- 判断notification的id合法性
		- 调用model的函数来删除对应notification
		- 返回函数执行状态

```go
//DeleteBy DELETE /api/notificaiton/{NotificationID:string}  删除指定通知
func (c *NotificationController) DeleteBy(notificationID string) (res models.CommonRes) {
	if c.Session.Get("id") == nil {
		res.State = models.StatusNotLogin
		return
	}
	if notificationID == "" {
		res.State = models.StatusBadReq
	}
	err := c.Model.DeleteNotificationByID(notificationID)
	if err != nil {
		res.State = err.Error()
	} else {
		res.State = models.StatusSuccess
	}
	return

}
```


### 主函数

>main函数中进行了整个框架的初始化设置以及监听启用，设计了数据库配置，添加中间件，路由设置和API Root的实现

- 首先进行Session的初始化，Session是浏览器和服务器保持会话状态的关联信息，我们在本项目中使用session进行用户验证

初始化session：

```go
	sesson, err := service.DBservice()
	if err != nil {
		fmt.Println("error")
	}
```
- 之后初始化我们的数据库，用于每一个model对应的数据存储

数据库的初始化：

```go
	//创建数据库
	var user models.UserDB
	user.DB = sesson.DB("project").C("user")
	var like models.LikeDB
	like.DBU = sesson.DB("project").C("user")
	like.DBL = sesson.DB("project").C("like")
	like.DBC = sesson.DB("project").C("content")
	like.DBN = sesson.DB("project").C("notification")
	var content models.ContentDB
	content.DB = sesson.DB("project").C("content")
	content.DBuser = sesson.DB("project").C("user")
	var notification models.NotifiationDB
	notification.DBN = sesson.DB("project").C("notification")
	notification.DBU = sesson.DB("project").C("user")
```
- 初始化iris服务,启用iris

```go
	app := iris.New()
	app.Use(myMiddleware)
```

- 实现API Root，完成显示简单API服务列表功能

```go
	app.Handle("GET", "/api", func(ctx iris.Context) {
		ctx.JSON(models.RootRes{
			"http://47.103.210.109:8080/api/user/info/{userID}",
			"http://47.103.210.109:8080/api/user/login",
			"http://47.103.210.109:8080/api/user/register",
			"http://47.103.210.109:8080/api/user/logout",
			"http://47.103.210.109:8080/api/user/name",

			"http://47.103.210.109:8080/api/content/{contentID}",
			"http://47.103.210.109:8080/api/content/detail/{contentID}",
			"http://47.103.210.109:8080/api/content/public",
			"http://47.103.210.109:8080/api/content/texts/{userID}",
			"http://47.103.210.109:8080/api/content/update",

			"http://47.103.210.109:8080/api/like/{contentID}",

			"http://47.103.210.109:8080/api/notificaiton/{notificationID}",
			"http://47.103.210.109:8080/api/notification/all",
		})
	})

	app.Handle("DELETE", "/api", func(ctx iris.Context) {
		ctx.JSON(models.RootRes{
			"AAAAAAA",
			"http://47.103.210.109:8080/api/user/login",
			"http://47.103.210.109:8080/api/user/register",
			"http://47.103.210.109:8080/api/user/logout",
			"http://47.103.210.109:8080/api/user/name",

			"http://47.103.210.109:8080/api/content/{contentID}",
			"http://47.103.210.109:8080/api/content/detail/{contentID}",
			"http://47.103.210.109:8080/api/content/public",
			"http://47.103.210.109:8080/api/content/texts/{userID}",
			"http://47.103.210.109:8080/api/content/update",

			"http://47.103.210.109:8080/api/like/{contentID}",

			"http://47.103.210.109:8080/api/notificaiton/{notificationID}",
			"http://47.103.210.109:8080/api/notification/all",
		})
	})
```


- 进行Session的创建和使用

```go
	sessionID := "mySession"
	//session的创建
	sess := sessions.New(sessions.Config{
		Cookie: sessionID,
	})
	app.Use(sess.Handler())
```

- 加入cors中间件来处理跨域请求，允许跨域访问：

```go
crs := cors.New(cors.Options{
		AllowedOrigins:   []string{"*"}, //允许通过的主机名称
		AllowCredentials: true,
	})
```

- 加入JWT中间件来处理Token验证：

```go
// JWT验证中间件
func ValidateJwtMiddleware(ctx iris.Context) {
	token, err := request.ParseFromRequest(ctx.Request(), request.AuthorizationHeaderExtractor,
		func(token *jwt.Token) (i interface{}, e error) {
			return []byte("My Secret"), nil
		})

	if err != nil || !token.Valid {
		var errres models.CommonRes
		errres.State = models.StatusBadReq
		errres.Data = err.Error()
		ctx.JSON(errres)
	} else {
		ctx.Next()
	}
}
```

- 使用myMiddleware中间件来设置允许跨域和允许的请求方法：

```go
func myMiddleware(ctx iris.Context) {
	ctx.Application().Logger().Infof("Runs before %s", ctx.Path())
	fmt.Println("test for middle")
	//ctx.Recorder().ResetHeaders()
	//ctx.Header("Access-Control-Allow-Origin", "*")
	//ctx.Header("Access-Control-Allow-Headers", "content-type")
	ctx.Header("Access-Control-Allow-Credentials","true");
	ctx.Header("Access-Control-Allow-Origin", "*")
	fmt.Println("Method", ctx.Request().Method)
	if ctx.Request().Method == "OPTIONS" {
		fmt.Println("test for core")
		ctx.StatusCode(200)	
		ctx.Header("Access-Control-Allow-Methods", "GET,POST,PUT,DELETE,PATCH,OPTIONS")
		ctx.Header("Access-Control-Allow-Headers", "Content-Type, Accept, Authorization")
		return
	}
	if ctx.Request().Method != "GET" && ctx.Request().Method != "POST"  {
		
	}
	ctx.Next()
}
```

- 实现user controller的路由PATH和Session设置、启用cors中间件：

```go
	users := mvc.New(app.Party("/api/user", crs).AllowMethods())
	users.Register(sess.Start)
	users.Handle(&controllers.UsersController{Model: user})
```

- 实现like controller的路由PATH和Session设置、启用cors中间件：

```go
	likes := mvc.New(app.Party("/api/like"))
	likes.Register(sess.Start)
	likes.Handle(&controllers.LikeController{Model: like})
```

- 实现contents controller的路由PATH和Session设置、启用cors中间件：

```go
	contents := mvc.New(app.Party("/api/content"))
	contents.Register(sess.Start)
	contents.Handle(&controllers.ContentController{Model: content})
```

- 实现notification controller的路由PATH和Session设置、启用cors中间件：

```go
	notifications := mvc.New(app.Party("/api/notification", crs).AllowMethods())
	notifications.Register(sess.Start)
	notifications.Handle(&controllers.NotificationController{Model: notification})
```

- 最后设置监听端口，开启服务：

```go
app.Listen("0.0.0.0:8080")
```






## 展示视频



## 参考文献

1. Github v3 API文档 https://docs.github.com/cn/free-pro-team@latest/rest/reference/reactions
2. Iris 框架中文文档 https://learnku.com/docs/iris-go/10



