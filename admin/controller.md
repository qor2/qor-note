主要处理的controller

type Controller struct {
    *Admin
    action *Action
}

从Admin继承下来，一般将admin作为参数生成一个新的Controller。所有的请求，都满足`requestHandler`的接口

```go
type requestHandler func(c *Context)
```

默认定义了

Action:
    针对action的操作

Asset, 

Create, 

Dashboard, 

Delete, 

Edit, 

Index, 

New, 

SearchCenter, 

Show, 

Update

这些方法，主要是调用context的Execute方法来进行具体的渲染的。json和xml使用JSON和XML函数来处理。









