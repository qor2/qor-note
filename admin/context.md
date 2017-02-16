
// Context admin context, which is used for admin controller
type Context struct {
    *qor.Context
    *Searcher
    Flashes  []Flash
    Resource *Resource
    Admin    *Admin
    Content  template.HTML
    Action   string
    Settings map[string]interface{}
    Result   interface{}

    funcMaps template.FuncMap
}


Render函数，接收模板名称，模板必须以tmpl结尾，注意，data用的是context这个变量，所以context的变量：

Context，Searcher，Flashes，Resource，Admin，Result，Content，Settings，Action

都可以在模板中访问


renderWith通过Asset函数，查找到具体的模板，读取内容，然后通过renderText将模板渲染出来。



Asset函数:

resoure的路径通过resource.ToParam来获取，如果存在themes，则

存在resource路径的时候

```go
filepath.Join("themes", theme, resourcePath)
```

不存在resource路径的时候

```go
filepath.Join("themes", theme)
```

获取文件内容：
    1、prefixes组合后获取，优先
    2、直接判定

通过Admin.AssetFS.Asset函数来查找对应的文件，如果存在，就返回content内容，否则返回 template not found 错误


Execute函数，通过Asset函数，查找到layout.tmpl文件，转换为html/template.Template变量，查找header和footer，如果
存在header或footer，则通过Asset函数查找header.tmpl或footer.tmpl文件.

将Execute中的result作为Context的Result变量,Content变量为context.Render(name, result)





