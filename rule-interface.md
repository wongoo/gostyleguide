# 接口规范

## 导出所有接口方法

需要注意：定义interface的时候如果要供package外部使用，需要对外导出所有定义的方法，
不然可能会遇到不在期望只能的错误。请看如下的范例：

```GO
// p1/p1.go
type TA interface {
   Hello()
   hi()
}

// p2/main.go
type T struct {
}

func (t *T) Hello() {
   fmt.Println("hello")
}

func (t *T) hi() {
   fmt.Println("hi")
}

func callHello(t interface{}) {
	if h, ok := t.(p1.TA); ok {
		h.Hello()
	} else {
		fmt.Println("not p1.TA")
	}
}

func main() {
   t := &T{}
   callHello(t) // t is not p1.TA
}

```