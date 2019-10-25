# 错误处理规则

## 1. 处理所有错误返回
为忽略错误而丢弃错误值的代码是一种糟糕的实践。
请务必检查错误返回，它们会提供错误的理由。

```go
// 烂代码！若路径不存在，它就会崩溃。
fi, _ := os.Stat(path)
if fi.IsDir() {
	fmt.Printf("%s is a directory\n", path)
}
```