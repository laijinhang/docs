# 基本使用
---

使用命令行将sql表生成一个数据表单类型，如：

```sql
CREATE TABLE `users` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `gender` tinyint(4) DEFAULT NULL,
  `city` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `ip` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `phone` varchar(10) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

生成了：

```go
package datamodel

import (
	...
)

func GetUserTable() (userTable table.Table) {

	// config the table model.
	userTable = table.NewDefaultTable(...)

  ...

	formList := userTable.GetForm()

	// set id editable is false.
	formList.AddField("ID", "id", db.Int, form.Default).FieldNotAllowEdit()
	formList.AddField("Ip", "ip", db.Varchar, form.Text)
	formList.AddField("Name", "name", db.Varchar, form.Text)

  ...

	return
}
```

## 类型

- 默认，只显示 ```Default```
- 普通文本 ```Text```
- 单选 ```SelectSingle```
- 密码 ```Password```
- 富文本 ```RichText```
- 文件 ```File```
- 双选择框 ```SelectBox```
- 多选 ```Select```
- icon下拉选择框 ```IconPicker```
- 时间选择框 ```Datetime```
- radio选择框 ```Radio```
- email输入框 ```Email```
- url输入框 ```Url```
- ip输入框 ```Ip```
- 颜色选择框 ```Color```
- 货币输入框 ```Currency```
- 数字输入框 ```Number```

例子：

```go

import (
    ...
    "github.com/GoAdminGroup/go-admin/template/types/form"  
    ...
)

func GetxxxTable() table.Table {
    formList.AddField("ID", "id", db.Int, form.Default)
}

```

## 操作

### 添加字段

```go

// 添加一个字段，字段标题为 ID，字段名为 id，字段类型为 int，表单类型为 Default
formList.AddField("ID", "id", db.Int, form.Default)

// 添加第二个字段，字段标题为 Ip，字段名为 ip，字段类型为 varchar，表单类型为 Text
formList.AddField("Ip", "ip", db.Varchar, form.Text)

// 添加第三个字段，一个sql表不存在的字段
formList.AddField("Custom", "custom", db.Varchar, form.Text)

```

### 设置默认值

```go
formList.AddField("header", "header", db.Varchar, form.Text).FieldDefault("header")
```

### 设置为必须输入

```go
formList.AddField("header", "header", db.Varchar, form.Text).FieldMust()
```

### 设置帮助信息

```go
formList.AddField("header", "header", db.Varchar, form.Text).FieldHelpMsg("长度应该大于5")
```

### 不允许编辑

```go

formList.AddField("id", "id", db.Int, form.Default).FieldNotAllowEdit()

```

### 不允许新增

```go

formList.AddField("id", "id", db.Int, form.Default).FieldNotAllowAdd()

```

### 提交前对字段进行过滤处理

```go
formList.AddField("链接", "url", db.Varchar, form.Text).
		FieldPostFilterFn(func(value types.PostFieldModel) string {
			return "http://xxxx.com/" + value.Get("url")
		})
```

### 显示过滤处理

```go

// 限制长度
formList.AddField("链接", "url", db.Varchar, form.Text).FieldLimit(limit int)

// 去除空格
formList.AddField("链接", "url", db.Varchar, form.Text).FieldTrimSpace()

// 截取
formList.AddField("链接", "url", db.Varchar, form.Text).FieldSubstr(start int, end int)

// 标题
formList.AddField("链接", "url", db.Varchar, form.Text).FieldToTitle()

// 大写
formList.AddField("链接", "url", db.Varchar, form.Text).FieldToUpper()

// 小写
formList.AddField("链接", "url", db.Varchar, form.Text).FieldToLower()

// xss过滤
formList.AddField("链接", "url", db.Varchar, form.Text).FieldXssFilter()

```

### 插入/新增逻辑重写

如果你的表单插入操作与新增操作比较复杂，框架不能满足，那么你可以完全重写并取代框架的插入操作。

```go

// 取代新增函数
formList.SetInsertFn(func(values form2.Values) error {
      // values 为传入的表单参数
  })
  
// 取代更新函数
formList.SetUpdateFn(func(values form2.Values) error {
      // values 为传入的表单参数
	})  
```