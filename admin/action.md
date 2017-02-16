action是一些列动作的抽象

```go

// Action action definiation
type Action struct {
    Name        string              // 名字
    Label       string              // 标签
    Method      string              // 支持的方法
    URL         func(record interface{}, context *Context) string      // 生成对应的url的方法
    URLOpenType string                                                 // ?
    Visible     func(record interface{}, context *Context) bool        // 是否可见的一个操作方法
    Handle      func(argument *ActionArgument) error                   // action对应的处理方法
    Modes       []string                    // ?
    Resource    *Resource                   // 对应的资源
    Permission  *roles.Permission           // 操作权限
}

```

handle的时候，会传入一个ActionArgument的结构

```go

// ActionArgument action argument that used in handle
type ActionArgument struct {
    PrimaryValues       []string            // 主键值
    Context             *Context            // 上下文了
    Argument            interface{}         // 具体的参数值
    SkipDefaultResponse bool                // 跳过默认响应?
}

```

其中关键的是ToParam的函数

```go

// ToParam used to register routes for actions
func (action Action) ToParam() string {
    return utils.ToParamString(action.Name)
}


// ToParamString replaces spaces and separates words (by uppercase letters) with
// underscores in a string, also downcase it
// e.g. ToParamString -> to_param_string, To ParamString -> to_param_string
func ToParamString(str string) string {
    return gorm.ToDBName(strings.Replace(str, " ", "_", -1))
}

```

gorm的ToDBName是TitleWord转换字母为小写的title_word字母，所以Action中的名称是关键的url参数名称，通常的路由形式为

`url_prefix/!action/action_name`







