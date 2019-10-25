# defer规则

defer的作用是在方法退出之前处理一段逻辑, 主要用于资源回收，如解锁、资源关闭等。

## 1. 使用 defer 的好处
资源回收紧跟着资源请求, 使得代码逻辑清晰，不容易遗忘要处理资源回收, 减少BUG。

## 2. 使用 defer 的不好之处

有微弱的性能损耗(纳秒级20ns左右)

## 3. 范例

加锁解锁:
```go
func foo(){
    lock.Lock()
    defer lock.Unlock()

    // user logic
}
```

关闭资源:
```go
func readFile() error{
    f,err := os.Open(path)
    if err != nil{
        return err
    }
    defer f.Close()

    // file operation
}
```


panic-recover:
```go
func doWithPanic() error{
    defer func(){
        if err := recover(); err != nil{
            // err check
        }       
    }()

    // user code may panic
    panic("some error")
}
```

## 4. 何时不使用defer
如果追求纳秒级别的性能优化，比如算法代码本身是纳秒级，使用defer对性能有明显的影响，
则可以不使用defer，在方法尾部添加解锁或资源回收的代码。

```go
func alg() {
    lock.Lock()
    
    // 算法代码（纳秒级）

    lock.Unlock()
}
```