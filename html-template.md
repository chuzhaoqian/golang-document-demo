# package template
```go
import "html/template"
```
> template 实现了数据驱动的模板，用于生成可防止代码注入的安全 HTML 输出。 是对 text/template 的包装，API 几乎没有差别，可以安全的随意替换俩包。但是无论何时当输出是 HTML 的时候都应使用本包。

## type ErrorCode
```go
type ErrorCode int
```
> ErrorCode 是代表错误种类的错误码。

```go
const (
	// OK 表示没有出错
	OK ErrorCode = iota
	// 当上下文环境有歧义的时导致 ErrAmbigContext
	ErrAmbigContext
	// 期望空白、属性名、标签结束标志而没有时，标签名或无引号标签值包含非法字符时
	// 一般是因为 HTML 元素输入了错误的标签名、属性名或未用引号的属性值
	ErrBadHTML
	// {{if}} 等分支在相同上下文开始和结束时，会导致 ErrBranchEnd
	// html/template 会静态检验 {{if}}{{range}}{{with}} 的每一个分支
	ErrBranchEnd
	// 如果以非文本上下文结束，则导致 ErrEndContext
	ErrEndContext
	// 调用不存在模板时导致 ErrNoSuchTemplate
	// {{define "main"}}<div {{template "attrs"}}&rt;{{end}}
    // {{define "attrs"}}href="{{.URL}}"{{end}}
	ErrNoSuchTemplate
	// 不能计算输出位置的上下文环境时，导致 ErrOutputContext
	ErrOutputContext
	// 尚未支持 JS 正则表达式插入字符集
	ErrPartialCharset
	// 部分转移序列尚未支持
	// 示例：
    //   <script>alert("\{{.X}}")</script&rt;
    // 讨论：
    //   html/template包不支持紧跟在反斜杠后面的action
    //   这一般是错误的，有更好的解决方法，例如：
    //     <script>alert("{{.X}}")</script&rt;
    //   可以工作，如果{{.X}}是部分转义序列，如"xA0"，
    //   可以将整个序列标记为安全文本：JSStr(`\xA0`)
	ErrPartialEscape
	// range 循环的重入口错误。
	ErrRangeLoopReentry
	// 斜杠可以是一个除法或者正则表达式
	// 检查分支中是否缺少分号，或者使用括号来明确意图
	ErrSlashAmbig
)
```

## type Error 
```go
type Error struct {
	// ErrorCode 描述错误的种类
	ErrorCode ErrorCode
	// Name是发生错误的模板的名字
	Name string
	// Line 是错误位置在模板原文中的行号 或者 0
	Line int
	// Desciption 是提供调试者阅读的错误描述
	Description string
}
```
> Error 描述在模板转义时出现的错误。

## func (*Error)Error
```go
func (e *error)Error()string
```

## func HTMLEscape
```go
func HTMLEscape(w io.Writer, b []byte)
```
> 函数向 w 中写入 b 的 HTML 转义等价表示。

## func HTMLEscapeString
```go
func HTMLEscapeString(s string)string
```
> 返回 s 的 HTML 转义等价字符串

## HTMLEscaper
```go
func HTMLEscaper(args ...interface{})string
```
> 函数返回其所有参数文本表示的 HTML 转义等价字符串。

## func JSEscape
```go
func JSEscape(w io.Writer, b []byte)
```
> 函数向 w 中写入 b 的 JacaScript 转义表示。

## func JSEscapeString
```go
func JSEscaperString(s string)string
```
> 返回 s 的 JavaScript 转义等价字符串

## func JSEscaper 
```go
func JSEcaper(args ...interface{})string
```
> 函数返回其所有参数文本表示的 JavaSctipe 转义等价字符串。

## func URLQueryEscaper
```go
func URLQueryEsccaper(args ...interface{})string
```
> 返回其所有参数文本表示的可以嵌入 URL 查询的转义等价字符串

## type FuncMap
```go
type FuncMap map[string]interface{}
```
> FuncMap 类型定义了函数名字字符串到函数的映射，每个函数都不许有1到2个返回值，如果有2个则后一个必须是 error 接口类型；如果有2个返回值的方法返回的 error 非 nil，模板执行会中断并返回给调用者该错误。该类型拷贝自 text/template 包的同名类型，因此不需要导入该包以使用该类型。

## type HTML
```go
type HTML string
```
> HTML 用于封装一个已知安全的 HTML 文档片段。它不应被第三方使用，也不能用于含有未闭合的标签或注释的 HTML 文本。该类型适用于封装一个效果良好的 HTML 生成器生成的 HTML 文本或本包模板的输出的文本。

## type HTMLAttr
```go
type HTMLAttr string
```
> HTMLAttr 用来封装一个来源可信的 HTML 属性

## type JS
```go
type JS string
```
> JS 用于封装一个已知安全的 EcmaScript5 表达式。

## type JSStr
```go
type JSStr string
```
> JSStr 用于封装一个打算想入 JavaScripe 表达式中的字符序列，该字符串必须匹配一系列 StringCharacters
>
> StringCharacter :: 除了`\`和行终止符的SourceCharacter | EscapeSequence
>
>注意不允许换行，JSStr("foo\\nbar")是可以的，但JSStr("foo\\\nbar")不可以。

## type URL
```go
type URL string
```
> URL 用来封装一个已知安全的 URL 或 URL 子字符串

## type CSS
```go
type CSS string
```
> CSS 用于包装匹配如下任一条的已知安全的内容
- 1. CSS3样式表，如`p { color: purple }`
- 2. CSS3规则，如`a[href=~"https:"].foo#bar`
- 3. CSS3声明，如`color: red; margin: 2px`
- 4. CSS3规则，如`rgba(0, 0, 255, 127)`

## type Template 
```go
type Tmplate struct {
	// 底层的模板解析树，会更新为 HTML 安全的
	Tree *parse.Tree // ???
	//
}
```
> Template 类型是 text/template 包的 Template 类型的特化版，用于生成安全的 HTML 文本片段。
