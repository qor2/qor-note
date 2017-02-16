assetFS

定义当前路径为root或者环境变量WEB_ROOT为当前路径

设定
globalViewPaths 和 globalAssetFSes

默认的globalAssetFSes为 AssetFileSystem ，接口为

```go

type AssetFSInterface interface {
    RegisterPath(path string) error
    Asset(name string) ([]byte, error)
    Glob(pattern string) (matches []string, err error)
    Compile() error
}


```


Compile未实现

Glob:

    对内部所有的路径进行遍历，按照模式查找对应的文件名

Asset：
    
    对内部所有的路径进行遍历，找到文件后，读取文件内容（字节类型）

RegisterPath:

    将路径添加到内部的所有路径中


assetFS可以支持阿里云，aws等，admin有一个默认的


