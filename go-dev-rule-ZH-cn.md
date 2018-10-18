整理go语言一些特别的规则，遵守这样的规则可以让你的代码更简洁、更清晰、更有范儿，可以让你避免被吐槽！如果你是从其他语言转过了的新手，以下内容或许对你有些帮助！


# 1. 代码格式化规则
其他语言可以根据自己的喜好设置代码格式化样式，go语言内置了代码格式化工具，只要执行命令`go fmt [packages]`命令则可对对应packages下的go文件进行格式化。不用也不应该再为团队指定一套格式化标准。

go中减少了很多括号和分号，让代码变得简洁。一开始写go代码时你可能还带有写其他语言代码的习惯，老喜欢敲括号（比如java），不过一个好的编辑器（比如goland）可以不断的提示你删除括号。

# 2. 导出(exported)规则：

go语言的最小封装单元是package, 针对package内的func、struct、package variable，如果首字母是大写则对外导出的(exported)，如果是小写则只能package内部访问(unexported,不导出的)。

对于对外导出(exported)的struct如果其field为大写开头，其他包也可以通过struct直接访问这些field。

## 2.1 interface 方法导出规则
需要注意：定义interface的时候如果要供package外部使用，需要对外导出所有定义的方法，不然可能会遇到不在期望只能的错误。请看如下的范例：

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

# 2. 命名规则

go语言建议代码文件内使用驼峰格式的命名规则,但不建议为了自解释而将命名定义很长，建议另外使用注释来进行说明。

## 2.1 包名

按照惯例，包应当以小写的单个单词来命名，且不应使用下划线或驼峰记法。
长命名并不会使其更具可读性。一份有用的说明文档通常比额外的长名更有价值。


## 2.2 构造器方法命名
出于构造复杂或者为了隐藏具体的struct细节信息，一般会在struct同一个包下创建一个构造器方法，如果struct命名为Project, 构造器方法的命名为`NewProject(parameters)`,但如果该包下只有一个构造器则简化命名为`New(parameters)`。

如果struct是不公开的(首写字母小写), 且包名和struct名称没有字面上的关联含义的话，构造器名称可以用struct名称将首写字母改为大写，比如 `netutil.LimitListener(parametersl net.Listener, n int) net.Listener`。



## 2.3 获取器和设置器

Go并不对获取器（getter）和设置器（setter）提供自动支持。
对外导出struct字段可以直接进行访问，一般情况无需设置getter/setter。

如果要在get/set时添加特殊逻辑，字段需设置为不导出的，并使用和字段名一样的导出的方法名作为getter名称。如不导出字段owner，其getter为Owner()，setter命名为SetOwner。


## 2.4 interface命名方法

interface命名需名词化，用名词性的单词来作为接口名，如果单词是动词，则将其名词化即增加以“er”或“or”结尾。

只包含一个方法的接口应当以该方法的名称加上-er后缀来命名，如核心库中就有很多这样的例子 Reader、Writer、Scanner、Stringer、Formatter、CloseNotifier 等。

go中struct只要拥有某一个interface定义的相同的签名方法，则被视为实现了这个接口，所以有些方法签名需要慎用，如Read、Write、Close、Flush、 String 等都具有典型的签名和意义，除非你明确知道要实现对应的接口。

## 2.5 文件命名规则

文件命名采用下划线命名规则，单词字母均使用小写，尽量用一个单词来命名，保持精炼。

如果实在需要多个单词，则使用下划线进行间隔，例如string_amd64.go.

单元测试的文件命名均以"\_test"结尾，例 search\_test.go

# 3. 注释规则

Go项目十分重视代码的文档，良好的文档可以增强软件的可读性和可维护性。
也能让你写的程序更容易被其他程序员所理解。

Go语言支持C风格的块注释 /* */ 和C++风格的行注释 //。
行注释更为常用，而块注释则主要用作包的注释。

## 3.1 注释内容规则

在程序中，每个可导出（首字母大写）的名称都应该有文档注释。

文档注释最好是完整的句子，这样它才能适应各种自动化的展示。 第一句应当以被声明的东西开头，并且是单句的摘要。

## 3.2 包注释规则

每个包都应包含一段包注释，即放置在包子句前的一个块注释。

如果一个包下只有一个文件，则将包注释加入到此文件中。
如果一个包下有多个文件，增加一个doc.go文件来专门写包的注释内容。

包注释内容比较少的情况可以简洁的用行注释即可。


# 4. 编码规则

## 4.1 初始化规则

go还可以直接使用类似 `&File{fd, name, nil, 0}`的形式初始化一个struct，参数顺序按照定义的顺序。但最好的做法是使用`字段:值`对的形式进行初始化，如`&File{fd: fd, name: name}`，这样的好处是依赖代码是前后兼容的，不会要求调用者要了解struct定义的字段顺序，struct增加了新字段，对调用者也不会产生影响。

## 4.2 处理所有错误返回
为忽略错误而丢弃错误值的代码是一种糟糕的实践。
请务必检查错误返回，它们会提供错误的理由。

```
// 烂代码！若路径不存在，它就会崩溃。
fi, _ := os.Stat(path)
if fi.IsDir() {
	fmt.Printf("%s is a directory\n", path)
}
```

# 参考
- [Golang Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Effective Go](https://golang.org/doc/effective_go.html)
- [Effective Go 中文版](https://go-zh.org/doc/effective_go.html)
- [Effective Go 中英双语版](https://legacy.gitbook.com/book/bingohuang/effective-go-zh-en/details)

