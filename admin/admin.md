admin模块包含了丰富的后台管理功能。


Admin模块，当然最重要的是Admin的结构

```go
// Admin is a struct that used to generate admin/api interface
type Admin struct {
    SiteName         string                 // 配置的站点名称
    Config           *qor.Config            // 配置信息
    I18n             I18n                   // 全球化信息
    AssetFS          AssetFSInterface       // 资源文件接口
    menus            []*Menu                // 菜单项
    resources        []*Resource            // 资源
    searchResources  []*Resource            // 搜索资源
    Auth             Auth                   // 认证
    router           *Router                // 路由
    funcMaps         template.FuncMap       // 模板函数
    metaConfigorMaps map[string]func(*Meta)     // 元配置映射表
}

```

构建一个新的Admin，通过admin.New来创建。



先来看以一下Config信息

```go
// Config admin config struct
type Config struct {
    Name       string       // 配置名称
    Menu       []string     // 配置时菜单的名称列表
    Invisible  bool         // 是否显示
    Priority   int          // 排序时的优先级
    PageCount  int          // 多页显示时的单页数量
    Singleton  bool         // 是否显示单个（如Setting或User，一个要单个，一个要多个），默认为单个
    Permission *roles.Permission        // 权限
    Themes     []ThemeInterface     // 显示的样式
}

```



