# API 设计文档

## 状态说明

|       状态        |                   说明                   |     应用API      |
| :---------------: | :--------------------------------------: | :--------------: |
|      success      |               表明成功返回               |     所有API      |
|    email_exist    |               邮箱已被使用               |       注册       |
|  username_exist   |              用户名已被使用              | 注册、更改用户名 |
| username_notexist |               用户名不存在               |       登陆       |
|  password_error   |                 密码错误                 |       登陆       |
|     not_login     |                  未登录                  | 获取用户信息、点赞、取消点赞、增加文本内容  |
|     like_exist    |              已经对该对象点赞过了        | 点赞     |
|   like_not_exist  |              不能取消没有的点赞          | 取消点赞     |
|  no_this_content  |              点赞、取消点赞的对象不存在  | 点赞、取消点赞、获取点赞列表     |
|     no_this_id    |              请求的id不存在              | 获取用户信息     |
|     name_nil      |              空用户名              | 用户注册 |
|     email_nil     |              空邮箱              | 用户注册 |
|     email_format_error    |            错误的邮箱格式              | 用户注册 |
| user_content_id_not_matching  |        更新的内容不是本用户发布的             |     更新内容    |

|      bad_req      | 错误的请求信息，代表请求json文件格式有误 | 所有POST类型API  |

- 注意：所有GET类型默认返回success状态，错误将在http状态码中体现

## User
### 用户注册

```
POST /api/user/register
```

#### Request

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| username    | string | 用户名        |
| email       | string | 邮箱          |
| password    | string | 密码          |

* 参数使用json形式提交

##### Example

```json
{
	"username": "lijia",
	"email": "lijia@sysu.edu.cn",
	"password": "123456"
}
```

#### Response

> Status: 201 Created
>
> Location: /api/user/register

| 参数名 |  类型  | 描述 |                    参数                    |
| :----: | :----: | :--: | :----------------------------------------: |
| State  | string | 状态 | success,email_exist,username_exist,bad_req |
|  Data  | string | 数据 |                    暂无                    |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
  "Data": ""
}
```

### 用户登录

```
POST /api/user/login
```

#### Request

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| username    | string | 用户名        |
| password    | string | 密码          |

* 参数使用json形式提交

##### Example

```json
{
	"username": "lijia",
	"password": "123456"
}
```

#### Response

> Status: 200 OK
>
> Location: /api/user/login

| 参数名 |  类型  | 描述 |                       参数                        |
| :----: | :----: | :--: | :-----------------------------------------------: |
| State  | string | 状态 | success, username_notexist,password_error,bad_req |
|  Data  | string | 令牌 |                       暂无                        |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
  "Data":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MDg0NTE0OTAsIm5hbWUiOiJzdW5oYW9uYW4iLCJwYXNzd29yZCI6IjEyMzQ1NiJ9.XfEv5awYf7sw6b6wrgiiz691MKGx-sCYKY1FwgaKemQ"
}
```

### 退出登录

```
POST /api/user/logout
```

#### Request

空


##### Example

空

#### Response

> Status: 200 OK
>
> Location: /api/user/logout

| 参数名 |  类型  | 描述 |       参数       |
| :----: | :----: | :--: | :--------------: |
| State  | string | 状态 | success, bad_req |
|  Data  | string | 数据 |       暂无       |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
  "Data":""
}
```


### 更新用户名

```
POST /api/user/name
```
* Token required
#### Request

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| name        | string | 用户名        |

* 参数使用json形式提交

##### Example

```json
{
	"name": "lijianew"
}
```

#### Response

> Status: 200 OK
>
> Location: /api/user/name

| 参数名 |  类型  | 描述 |              状态              |
| :----: | :----: | :--: | :----------------------------: |
| State  | string | 状态 | success,username_exist,bad_req |
|  Data  | string | 数据 |              暂无              |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
  "Data":""
}
```


### 获取用户信息

```
GET /api/user/info/{userID:string}
```

* userID string 用户id(user_id="self"时，获取自身信息)
#### Request

空

##### Example

空

#### Response

> Status: 200 OK
>
> Location: /api/user/info/self
> 
| 参数名 |  类型  | 描述 |              状态              |
| :----: | :----: | :--: | :----------------------------: |
| State  | string | 状态 | success,not_login,bad_req,no_this_id |
|     ID      |   string   |    用户ID     |              暂无              |
|    Email    |   string   |     邮箱      |              暂无              |
|    Info     | dictionary |   用户信息    |              暂无              |
|  Info.Name  |   string   |    用户名     |              暂无              |
| Info.Aavtar |   string   |    头像URL    |              暂无              |
|  Info.Bio   |   string   |   个人简介    |              暂无              |
| Info.Gender |   number   | 性别(0为男生) |              暂无              |

* 参数使用json形式解析

##### Example
```json
{
  "State":"success",
  "ID": "5fbcb442f5beb22628d4b685",
	"Email": "lijia@sysu.edu.cn",
	"Info" :{
        "Name": "lijia",
        "Avatar": "",
        "Bio": "",
        "Gender": 1
    }
}
```

## Content

### 获取指定内容

```
GET /api/content/detail/{contentID:string}
```

* contentID string 内容id
#### Request

空

##### Example

空

#### Response

> Status: 200 OK
>
> Location: /api/content/detail/5c3774187a2bdd000111e10c

| 参数名      | 类型   | 描述          |
| :---------: | :----: | :-----------: |
| State       | string | 状态          |
| Data        | dictionary | 内容信息  |
| User        | dictionary | 用户信息  |
| Data.ID | string | 文章ID |
| Data.Name | string | 标题 |
| Data.Detail | string | 内容 |
| Data.OwnID | string | 发布者ID |
| Data.PublishDate | string | 发布时间（Unix时间戳） |
| Data.LikeNum | int | 点赞数 |
| Data.Public | bool | 公开内容 |
| Data.Tag | array | 标签数组 |
| User.Name | string | 用户名 |
| User.Avatar | string | 头像url |
| User.Gender | number | 性别 |

* 参数使用json形式解析

##### Example
```json
{
 "State": "success",
 "Data": {
  "ID": "5c3774187a2bdd000111e10c",
  "Name": "测试",
  "Detail": "一个普通的测试",
  "OwnID": "5b3510fe7a2bdd4aac29eb73",
  "PublishDate": "1547138072000",
  "LikeNum": 1,
  "Public": true,
  "Tag": [],
 },
 "User": {
  "Name": "Test用户",
  "Avatar": "https://violet-1252808268.cos.ap-guangzhou.myqcloud.com/5b1fe672f2e85b26522ac546.jpg",
  "Gender": 0
 }
}
```

### 删除指定内容

```
DELETE /api/content/{contentID:string}
```

* contentID string 内容id
#### Request

空

##### Example

空

#### Response

> Status: 204 No Content
>
> Location: /api/content/5c3774187a2bdd000111e10c

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| State       | string | 状态          |
| Data        | string | 数据          |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```


### 获取公共内容

```
GET /api/content/public
```

#### Parameters

| 字段     | 类型   | 描述   |
| -------- | ------ | ------ |
| page     | number | 页码   |
| per_page | number | 页大小 |

##### Example


#### Response

> Status: 200 OK
>
> Location: /api/content/public

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| State       | string | 状态          |
| Data        | array  | 内容信息数组  |
| DataItem    | dictionary | 内容信息数组项 |
| DataItem.Data | dictionary | 内容信息  |
| DataItem.User | dictionary | 用户信息  |


* 参数使用json形式解析

##### Example
```json
{
 "State": "success",
 "Data": [
  {
   "Data": {
    "ID": "5c3774187a2bdd000111e10c",
    "Name": "测试1",
    "Detail": "一个普通的测试",
    "OwnID": "5b3510fe7a2bdd4aac29eb73",
    "PublishDate": 1547138072000,
    "LikeNum": 1,
    "Public": true,
    "Tag": [],
   },
   "User": {
    "Name": "Test用户",
    "Avatar": "https://violet-1252808268.cos.ap-guangzhou.myqcloud.com/5b1fe672f2e85b26522ac546.jpg",
    "Gender": 0
   }
  },
  {
    "Data": {
    "ID": "5c3774187a2bdd000111e10d",
    "Name": "测试2",
    "Detail": "一个普通的测试",
    "OwnID": "5b3510fe7a2bdd4aac29eb73",
    "PublishDate": 1547138072000,
    "LikeNum": 1,
    "Public": true,
    "Tag": [],
   },
   "User": {
    "Name": "Test用户",
    "Avatar": "https://violet-1252808268.cos.ap-guangzhou.myqcloud.com/5b1fe672f2e85b26522ac546.jpg",
    "Gender": 0
   }
  }
 ]
}
```

### 获取指定用户的所有内容

```
GET /api/content/texts/{userID:string}
```

* userID string 用户id(user_id="self"时，获取自身信息)

#### Parameters

| 字段     | 类型   | 描述   |
| -------- | ------ | ------ |
| page     | number | 页码   |
| per_page | number | 页大小 |

##### Example


#### Response

> Status: 200 OK
>
> Location: /api/content/texts/5c3774187a2bdd000111e10c

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| State       | string | 状态          |
| Data        | array  | 内容信息数组  |

* 参数使用json形式解析

##### Example
```json
{
 "State": "success",
 "Data": [
  {
   "ID": "5b35115a7a2bdd4aac29eb74",
   "Name": "我是一个测试账户",
   "Detail": "test内容",
   "OwnID": "5b3510fe7a2bdd4aac29eb73",
   "PublishDate": 1530204506000,
   "LikeNum": 2,
   "CommentNum": 1,
   "Public": true,
   "Tag": [
    "说明"
   ]
  }
 ]
}
```

### 更新文本内容

```
POST /api/content/update
```

#### Request

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| contentID   | string | 内容ID        |
| detail      | string | 正文          |
| tags        | array  | 标签          |
| isPublic    | bool   | 是否公开      |

* 参数使用json形式提交

##### Example

```json
{
    "contentID":"5fda52e2619fcb15076f9b0c",
	  "detail": "这是一个正文",
    "tags":[
        "标签"
    ],
    "isPublic": true
}
```

#### Response

> Status: 201 Created
>
> Location: /api/content/update

| 参数名 |  类型  | 描述 |           参数           |
| :----: | :----: | :--: | :----------------------: |
| State  | string | 状态 | success,not_login,bad_req,user_content_id_not_matching |
|  Data  | string | 数据 |           暂无           |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```

### 增加文本内容

```
POST /api/content/text
```

#### Request

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| detail      | string | 正文          |
| tags        | array  | 标签          |
| isPublic    | bool   | 是否公开      |

* 参数使用json形式提交

##### Example

```json
{
	"detail": "这是一个正文",
    "tags":[
        "标签"
    ],
    "isPublic": true
}
```

#### Response

> Status: 201 Created
>
> Location: /api/content/text

| 参数名 |  类型  | 描述 |           参数           |
| :----: | :----: | :--: | :----------------------: |
| State  | string | 状态 | success,not_login,bad_req |
|  Data  | string | 数据 |           暂无           |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```


## Like
### 获取用户点赞列表
```
GET /api/like/{contentID}
```
* contentID string 内容id

#### Response

> Status: 200 OK
>
> Location: /api/like/5c3765bd7a2bdd000111e107

|  参数名  |  类型  |   描述   |       参数       |
| :------: | :----: | :------: | :--------------: |
|  State   | string |   状态   |success,no_this_content|
|   Data   | array  | 点赞列表 |       暂无       |
| DataItem | string |  用户名  |       暂无       |


* 参数使用json形式解析

##### Example
```json
{
 "State": "success",
 "Data": [
     "xiaoming",
     "xiaohong"
 ]
}
```
### 对某个内容点赞
```
POST /api/like/{contentID}
```
* contentID string 内容id
#### Request

空

##### Example

空
#### Response

> Status: 202 Accepted
>
> Location: /api/like/5c3765bd7a2bdd000111e107

| 参数名 |  类型  | 描述 |       参数       |
| :----: | :----: | :--: | :--------------: |
| State  | string | 状态 | success, not_login,like_exist,bad_req |
|  Data  | string | 数据 |       暂无       |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```

### 取消用户对某个内容的点赞
```
PATCH /api/like/{contentID}
```
* contentID string 内容id
#### Request

空

##### Example

空

#### Response

> Status: 204 No Content
>
> Location: /api/like/5c3765bd7a2bdd000111e107

| 参数名 |  类型  | 描述 |       参数       |
| :----: | :----: | :--: | :--------------: |
| State  | string | 状态 | success,not_login,like_not_exist, bad_req |
|  Data  | string | 数据 |       暂无       |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```

## Notification

### 获取用户所有通知

```
GET /api/notification/all
```

#### Parameters

| 字段     | 类型   | 描述   |
| -------- | ------ | ------ |
| page     | number | 页码   |
| per_page | number | 页大小 |

##### Example

#### Response

> Status: 200 OK
>
> Location: /api/notification/all

| 参数名       | 类型   | 描述          |
| :---------:  | :----: | :-----------: |
| State        | string | 状态          |
| Notification | array  | 通知信息数组  |
| NotificationItem | dictionary | 通知信息数组项 |
| NotificationItem.Data | dictionary | 通知信息  |
| NotificationItem.User | dictionary | 用户信息  |
| NotificationItem.Data.ID | string | 通知ID |
| NotificationItem.Data.CreateTime | string | 通知创建时间（Unix时间戳） |
| NotificationItem.Data.Content | string | 通知内容 |
| NotificationItem.Data.SourceID | string | 来源用户ID |
| NotificationItem.Data.TargetID | string | 通知用户ID |
| NotificationItem.Data.ContentID | string | 点赞文章ID |
| NotificationItem.Data.Type | string | 通知类型 |


* 参数使用json形式解析

##### Example
```json
{
 "State": "success",
 "Notification": [
  {
   "Data": {
    "ID": "5fc774187a2bdd000111e10c",
    "CreateTime": "1606563448000",
    "Content": "",
    "SourceID": "5b3510fe7a2bdd4aac29eb73",
    "TargetID": "5b3510fe7a2bdd4aac29eb73",
    "ContentID": "5fda52e2619fcb15076f9b0c",
    "Type": "like"
   },
   "User": {
    "Name": "Test用户",
    "Avatar": "https://violet-1252808268.cos.ap-guangzhou.myqcloud.com/5b1fe672f2e85b26522ac546.jpg",
    "Gender": 0
   }
  },
  {
   "Data": {
    "ID": "5c3774187a2bdd000111e10e",
    "CreateTime": "1606563448012",
    "Content": "",
    "SourceID": "5b3510fe7a2bdd4aac29eb73",
    "TargetID": "5b3510fe7a2bdd4aac29eb73",
    "ContentID": "5fda52e2619fcb15076f9b0c",
    "Type": "like"
   },
   "User": {
    "Name": "Test用户",
    "Avatar": "https://violet-1252808268.cos.ap-guangzhou.myqcloud.com/5b1fe672f2e85b26522ac546.jpg",
    "Gender": 0
   }
  },
 ]
}
```

### 删除指定通知
```
DELETE /api/notificaiton/{NotificationID:string}
```

* NotificationID string 通知id

#### Request

空

##### Example

空

#### Response

> Status: 204 No Content
>
> Location: /api/notificaiton/5fc774187a2bdd000111e10c

| 参数名      | 类型   | 描述          |
| ----------- | ------ | ------------- |
| State       | string | 状态          |
| Data        | string | 数据          |

* 参数使用json形式解析

##### Example
```json
{
	"State": "success",
	"Data": ""
}
```

