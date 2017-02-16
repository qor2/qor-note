responder库是核心支持库。


默认支持html，json和xml三种格式的输出


使用With函数，对的函数进行封装


使用Respond函数，对http.Request进行响应操作：

1、从url中获取request的路径的扩展，然后渲染，如果没有，执行2
2、从Accept中获取第一个能够满足支持的类型，如果没有，执行3
3、取出第一个，然后渲染


Respond函数接受http.Request的参数，但回调的函数则没有传入，所以需要和qor.Context强绑定，最为qor.Context下一个闭包




