路由结构

```go
// Router contains registered routers
type Router struct {
    Prefix      string                      
    routers     map[string][]routeHandler    
    middlewares []*Middleware
}
```

路由三部分组成：
    1、前缀
    2、匹配到的路由的处理过程
    3、middlewares


路由中，添加方法的是Get,Post,Put,Delete等方法

这其中的middlewares通过router.Use函数来添加新的middleware，其结构体是

```go
type Middleware struct {
    Name    string                          // 名称
    Handler func(*Context, *Middleware)     // 处理函数
    next    *Middleware                     // 下一个middleware
}
```

Name名称可以被覆盖，Middleware.Next函数用来调用下一个middleware


admin中，定义了Resource添加到路由中的方法：

```go

func (admin *Admin) RegisterResourceRouters(res *Resource, modes ...string) {
    var (
        prefix          string
        router          = admin.router
        param           = res.ToParam()             // 转换为名称
        primaryKey      = res.ParamIDName()         // 主键ID名称
        adminController = &Controller{Admin: admin}  // 构建admin的控制器
    )

    if prefix = func(r *Resource) string {
        p := param

        // 递归将所有的前缀连接起来
        for r.base != nil {
            bp := r.base.ToParam()
            if bp == param {
                return ""
            }
            p = path.Join(bp, r.base.ParamIDName(), p)
            r = r.base
        }
        return "/" + strings.Trim(p, "/")
    }(res); prefix == "" {
        return          // 表示没有找到合适的前缀
    }

    // modes为crud
    for _, mode := range modes {
        if mode == "create" {
            if !res.Config.Singleton {
                // New
                router.Get(path.Join(prefix, "new"), adminController.New, RouteConfig{
                    PermissionMode: roles.Create,
                    Resource:       res,
                })
            }

            // Create
            router.Post(prefix, adminController.Create, RouteConfig{
                PermissionMode: roles.Create,
                Resource:       res,
            })
        }

        if mode == "update" {
            if res.Config.Singleton {
                // Update
                router.Put(prefix, adminController.Update, RouteConfig{
                    PermissionMode: roles.Update,
                    Resource:       res,
                })
            } else {
                // Action
                for _, action := range res.Actions {
                    actionController := &Controller{Admin: admin, action: action}
                    router.Get(path.Join(prefix, "!action", action.ToParam()), actionController.Action, RouteConfig{
                        Permissioner:   action,
                        PermissionMode: roles.Update,
                        Resource:       res,
                    })
                    router.Put(path.Join(prefix, "!action", action.ToParam()), actionController.Action, RouteConfig{
                        Permissioner:   action,
                        PermissionMode: roles.Update,
                        Resource:       res,
                    })
                }

                // Edit
                router.Get(path.Join(prefix, primaryKey, "edit"), adminController.Edit, RouteConfig{
                    PermissionMode: roles.Update,
                    Resource:       res,
                })

                // Update
                router.Post(path.Join(prefix, primaryKey), adminController.Update, RouteConfig{
                    PermissionMode: roles.Update,
                    Resource:       res,
                })
                router.Put(path.Join(prefix, primaryKey), adminController.Update, RouteConfig{
                    PermissionMode: roles.Update,
                    Resource:       res,
                })

                // Action
                for _, action := range res.Actions {
                    actionController := &Controller{Admin: admin, action: action}
                    router.Get(path.Join(prefix, primaryKey, action.ToParam()), actionController.Action, RouteConfig{
                        Permissioner:   action,
                        PermissionMode: roles.Update,
                        Resource:       res,
                    })
                    router.Put(path.Join(prefix, primaryKey, action.ToParam()), actionController.Action, RouteConfig{
                        Permissioner:   action,
                        PermissionMode: roles.Update,
                        Resource:       res,
                    })
                }
            }
        }

        if mode == "read" {
            if res.Config.Singleton {
                // Index
                router.Get(prefix, adminController.Show, RouteConfig{
                    PermissionMode: roles.Read,
                    Resource:       res,
                })
            } else {
                // Index
                router.Get(prefix, adminController.Index, RouteConfig{
                    PermissionMode: roles.Read,
                    Resource:       res,
                })

                // Show
                router.Get(path.Join(prefix, primaryKey), adminController.Show, RouteConfig{
                    PermissionMode: roles.Read,
                    Resource:       res,
                })
            }
        }

        if mode == "delete" {
            if !res.Config.Singleton {
                // Delete
                router.Delete(path.Join(prefix, primaryKey), adminController.Delete, RouteConfig{
                    PermissionMode: roles.Delete,
                    Resource:       res,
                })
            }
        }
    }

    // Sub Resources
    scope := gorm.Scope{Value: res.Value}
    if scope.PrimaryField() != nil {
        for _, meta := range res.ConvertSectionToMetas(res.NewAttrs()) {
            if meta.FieldStruct != nil && meta.FieldStruct.Relationship != nil && meta.Resource.base != nil {
                if len(meta.Resource.newSections) > 0 {
                    admin.RegisterResourceRouters(meta.Resource, "create")
                }
            }
        }

        for _, meta := range res.ConvertSectionToMetas(res.ShowAttrs()) {
            if meta.FieldStruct != nil && meta.FieldStruct.Relationship != nil && meta.Resource.base != nil {
                if len(meta.Resource.showSections) > 0 {
                    admin.RegisterResourceRouters(meta.Resource, "read")
                }
            }
        }

        for _, meta := range res.ConvertSectionToMetas(res.EditAttrs()) {
            if meta.FieldStruct != nil && meta.FieldStruct.Relationship != nil && meta.Resource.base != nil {
                if len(meta.Resource.editSections) > 0 {
                    admin.RegisterResourceRouters(meta.Resource, "update", "delete")
                }
            }
        }
    }
}



```

