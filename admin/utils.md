```go

// ExitWithMsg debug error messages and print stack
func ExitWithMsg(msg interface{}, value ...interface{}) {
    fmt.Printf("\n"+filenameWithLineNum()+"\n"+fmt.Sprint(msg)+"\n", value...)
    debug.PrintStack()
}

```
爆出错误，打印调用堆栈


