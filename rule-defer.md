# defer规则

defer的作用是在方法退出之前处理一段逻辑, 主要用于资源回收，如解锁、流关闭等。

## 1. defer 的好处
 - 使得代码逻辑清晰
 - 不容易遗忘资源回收
 - 良好的编程习惯

## 2. defer 的不好之处

- 有微弱的性能损耗(纳秒级20ns左右)

## 3. 使用defer的场景

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
    f,err:=os.Open(path)
    if err!=nil{
        return err
    }
    defer f.Close()

    // file operation
}
```

## 4. 何时不使用defer
如果追求纳秒级别的性能优化，比如算法代码本身使纳秒级，使用defer对性能有明显的影响，
则可以不实用defer，在方法尾部添加解锁或资源回收的代码。

```go
func alg() {
    lock.Lock()
    
    // 算法代码（纳秒级）

    lock.Unlock()
}
```