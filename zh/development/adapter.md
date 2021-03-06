# 适配器
---

适配器的作用是实现web框架context与GoAdmin自身context的转换。
制作一个adapter需要实现以下方法：

```go
package adapter

import (
	"github.com/GoAdminGroup/go-admin/plugins"
	"github.com/GoAdminGroup/go-admin/template/types"
)

// WebFrameWork is a interface which is used as an adapter of
// framework and goAdmin. It must implement two methods. Use registers
// the routes and the corresponding handlers. Content writes the
// response to the corresponding context of framework.
type WebFrameWork interface {
	Use(interface{}, []plugins.Plugin) error
	Content(interface{}, types.GetPanelFn)
	SetConnection(db.Connection)
	GetConnection() db.Connection
	SetContext(ctx interface{}) WebFrameWork
	GetCookie() (string, error)
	Path() string
	Method() string
	PjaxHeader() string
	Redirect()
	SetContentType()
	Write(body []byte)
	CookieKey() string
	HTMLContentType() string
	Name() string
	User(ci interface{}) (models.UserModel, bool)
	SetApp(app interface{}) error
	AddHandler(method, path string, plug plugins.Plugin)
}
```

## Use

**Use**接收两个参数，第一个参数类型为**interface{}**，是web框架的context，第二参数为插件数组。返回值是一个**error**。
**Use**的作用是利用传入的插件数组，将web框架的路由与将插件数组中的控制器方法关联起来，实现web框架与GoAdmin插件方法的对应。比如插件example中：

```go
"/admin/example" => ShowExample(ctx *context.Context)
```

通过```Use```的处理后将注入到web框架中。

## Content

**Content**方法接收两个参数，第一个参数类型为**interface{}**，是web框架的context，第二参数为**types.GetPanel**类型。

类型如下：

```go
type GetPanel func(ctx interface{}) (Panel, error)
```

**Content**的作用就是自定义页面的时候，传入web框架的context，然后往web框架的context写入自定页面内容的返回。

## init

**init**方法往engine中注入该适配器，从而能够被别人去使用。

```go
func init() {
	engine.Register(new(Beego))
}
```

更多请参考：[https://github.com/GoAdminGroup/go-admin/tree/master/adapter](https://github.com/GoAdminGroup/go-admin/tree/master/adapter)