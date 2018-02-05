# package url
```go
import "net/url"
```
> 解析 URL 并实现了查询逸码

## func QueryEscape
```go
func QueryEscape(s string)string
```
> QueryEscape 函数对 s 进行转码使之可以安全的用在 URL 查询里

## func QueryUnescape
```go
func QueryUnescape(s string)(string, error)
```
> QueryUnescape函数用于将QueryEscape转码的字符串还原。它会把%AB改为字节0xAB，将'+'改为' '。如果有某个%后面未跟两个十六进制数字，本函数会返回错误。

## type Error 
```go
type Error struct {
	Op string
	URL string
	Err error
}
```
> Error 会报告一个错误，以及导致该错误发生的 URL 和操作。

## func (*Error)Error
```go
func (e *Error)Error()string
```

## type EscapeError
```go
type EscapeError string
```

## func (EscapeError)Error
```go
func (e EscapeError)Error()string
```

## type URL
```go
type URL struct {
	Scheme string
	Opapue string
	User *Userinfo
	Host string
	Path string
	RawQuery string
	Fragment strting
}
```
> URL类型代表一个解析后的URL。注意路径字段是以解码后的格式保存的

## func Parse
```go
func Parse(rawurl string)(url *URL, err error)
```
> Parse 函数解析 rawurl 为一个 URL 结构体，rawurl 可以是绝对地址，也可以是相对地址。

## func ParseRequestURL
```go
func ParseRequestURL(rawurl string)(url *URL, err error)
```
> PareeRequestURL 函数解析 rawurl 为一个 URL 结构体，本函数会假设rawurl是在一个HTTP请求里，因此会假设该参数是一个绝对URL或者绝对路径，并会假设该URL没有#fragment后缀。（网页浏览器会在去掉该后缀后才将网址发送到网页服务器）

## func (*URL)IsAbs
```go
func (u *URL)IsAbs()bool
```
> 函数在URL是绝对URL时才返回真。

## func (*URL)Query 
```go
func (u *URL)Query()Values
```
> Query 方法解析 RawQuery 字段并返回其表示的 Values 类型键值对

## func (*URL)RequestURL
```go
func (u *URL)RequestURL()string
```
> RequestURL 方法返回编码好的 path?query 或 opaque?query 字符串，用在HTTP请求里。

## func (*URL)String 
```go
func (u *URL)String()string
```
> String 将 URL 重构为一个合法 URL 字符串

## func (*URL)Parse
```go
func (u *URL)Parse(ref string)(*URL, error)
```
> Parse方法以u为上下文来解析一个URL，ref可以是绝对或相对URL。本方法解析失败会返回nil, err；否则返回结果和ResolveReference一致。

## func (*URL)ResolveReference
```go
func (u *URL)ResolveReference(ref *URL)*URL
```
> 本方法根据一个绝对URI将一个URI补全为一个绝对URI，参见RFC 3986 节 5.2。参数ref可以是绝对URI或者相对URI。ResolveReference总是返回一个新的URL实例，即使该实例和u或者ref完全一样。如果ref是绝对URI，本方法会忽略参照URI并返回ref的一个拷贝。

## type Userinfo
```go
type Userinfo struct {
	//
}
```
> Userinfo类型是一个URL的用户名和密码细节的一个不可修改的封装。一个真实存在的Userinfo值必须保证有用户名以及一个可选的密码。

## func User
```go
func User(username string)*Userinfo
```
> User函数返回一个用户名设置为username的不设置密码的*Userinfo。

## func UserPassword
```go
func UserPassword(username, password string)*Userinfo
```
> UserPassword函数返回一个用户名设置为username、密码设置为password的*Userinfo。这个函数应该只用于老式的站点，因为风险很大，不建议使用

## func (*Userinfo)Username 
```go
func (u *Userinfo)Username()string
```
> Username 返回用户名

## func (*Userinfo)Password 
```go
func (u *Userinfo) Password() (string, bool)
```
> 如果设置了密码返回密码和真，否则会返回假。

## func (*Userinfo)String
> String方法返回编码后的用户信息，格式为"username[:password]"。

## type Values
```go
type Values map[string][]string
```
> Values 将建映射到值的列表。它一般用于查询的参数和表单的属性。不同于http.Header这个字典类型，Values的键是大小写敏感的。

## func ParseQuery
```go
func ParseQuery(query string)(m Values, err error)
```
> ParseQuery 函数及诶系一个 URL 编码的查询字符串，并返回可以表示该查询的 Values 类型的字典。本函数总是返回一个包含了所有合法查询参数的非 nil 字典， err 用来描述解码时遇到的第一个错误。

## func (Values)Get
```go
func (v Values)Get(key string)string
```
> Get会获取key对应的值集的第一个值。如果没有对应key的值集会返回空字符串。获取值集请直接用map。

## func (Values)Set
```go
func (v Values)Set(key, value string)
```
> Set 方法将 key 对应的值集设为只有 value，它会替换掉已有的值集。

## func (Value)Add
```go
func (v Values)Add(key, value string)
```
> Add 将 value 添加到 key 关联的值集里原有的值的后面

## func (Value)Del
```go
func (v Values)Del(key string)
```
> Del删除key关联的值集

## func (Values)Encode
```go
func (v Values)Encode()string
```
> Encode 方法将 v 编码设为 url 编码格式("bar=baz&foo=quux")，编码时会以键进行排序。

### Example
```go
package main

import (
	"fmt"
	"net/url"
)

func main() {
	rawurl := "htpp://www.lawuyou.com?name=czq&lang=go&other[]=JAVA&other[]=PHP"

	escape := url.QueryEscape(rawurl)
	fmt.Println(escape)

	s, _ := url.QueryUnescape(escape)
	fmt.Println(s)

	u, _ := url.Parse(rawurl)
	fmt.Println(u)
	fmt.Println(u.RawQuery)
	fmt.Println(u.String())
	fmt.Println(u.IsAbs())

	values := u.Query()
	fmt.Println(values)

	// values2 map[lang:[go] other[]:[JAVA PHP] htpp://www.lawuyou.com?name:[czq]]
	//	values2, _ := url.ParseQuery(rawurl)
	values2, _ := url.ParseQuery(u.RawQuery)
	fmt.Println(values2)

	// 空
	//	fmt.Println(values2.Get("other"))
	fmt.Println(values2.Get("lang"))
	fmt.Println(values2.Encode())
}
```