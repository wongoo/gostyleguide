# 命名规则


go语言建议代码文件内使用驼峰格式的命名规则,但不建议为了自解释而将命名定义很长，建议另外使用注释来进行说明。

## 1. 包名

按照惯例，包应当以小写的单个单词来命名，且不应使用下划线或驼峰记法。
长命名并不会使其更具可读性。一份有用的说明文档通常比额外的长名更有价值。


## 2. 构造器方法命名
出于构造复杂或者为了隐藏具体的struct细节信息，一般会在struct同一个包下创建一个构造器方法，
如果struct命名为Project, 构造器方法的命名为`NewProject(parameters)`,但如果该包下只有一个构造器则简化命名为`New(parameters)`。

如果struct是不公开的(首写字母小写), 且包名和struct名称没有字面上的关联含义的话，
构造器名称可以用struct名称将首写字母改为大写，比如 `netutil.LimitListener(parametersl net.Listener, n int) net.Listener`。



## 3. 获取器和设置器

Go并不对获取器（getter）和设置器（setter）提供自动支持。
对外导出struct字段可以直接进行访问，一般情况无需设置 getter/setter。

如果要在get/set时添加特殊逻辑，字段需设置为不导出的，并使用和字段名一样的导出的方法名作为getter名称。
如不导出字段owner，其getter为Owner()，setter命名为SetOwner。


## 4. interface命名方法

interface命名需名词化，用名词性的单词来作为接口名，如果单词是动词，则将其名词化即增加以“er”或“or”结尾。

只包含一个方法的接口应当以该方法的名称加上-er后缀来命名，
如核心库中就有很多这样的例子 Reader、Writer、Scanner、Stringer、Formatter、CloseNotifier 等。

go中struct只要拥有某一个interface定义的相同的签名方法，则被视为实现了这个接口，
所以有些方法签名需要慎用，如Read、Write、Close、Flush、 String 等都具有典型的签名和意义，除非你明确知道要实现对应的接口。

## 5. 文件命名规则

文件命名采用下划线命名规则，单词字母均使用小写，尽量用一个单词来命名，保持精炼。

如果实在需要多个单词，则使用下划线进行间隔，例如 `string_amd64.go`.

单元测试的文件命名均以 `_test` 结尾，例 `search\_test.go`.