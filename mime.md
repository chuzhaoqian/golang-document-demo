# package mime
```go
import "mime"
```
> mime 实现了 MIME 的部分规定。

## func AddExtensionType
```go
func AddExtensionType(ext, type string)error
```
> 函数将扩展名和 mimetype 建立偶联；扩展名应以点号开始，例如".html"

## func FormatMediaType
```go
func FromatMediaType(t string, param map[string]string)string
```
> 函数根据 RFC 2045 和 RFC 2616 的规定将媒体类型 t 和参数 param 连接为一个 mime 媒体类型，类型和参数都采用小写字母。任何一个参数不合法都会返回空字符串。

## func ParseMediaType
```go
func ParseMediaType(v string)(mediatype string, params map[string]string, err error)
```
> 函数根据RFC 1521解析一个媒体类型值以及可能的参数。媒体类型值一般应为Content-Type和Conten-Disposition头域的值（参见RFC 2183）。成功的调用会返回小写字母、去空格的媒体类型和一个非空的map。返回的map映射小写字母的属性和对应的属性值。

## func TypeByExtension
```go
func TypeByExtension(ext string)string
```
> 函数返回与扩展名偶联的MIME类型。扩展名应以点号开始，如".html"。如果扩展名未偶联类型，函数会返回""。
> 
> 内建的偶联表很小，但在unix系统会从本地系统的一或多个mime.types文件（参加下表）进行增补。
>
> Windows系统的mime类型从注册表获取。文本类型的字符集参数默认设置为"utf-8"。

### Example
```go
package main

import (
	"fmt"
	"mime"
)

func main() {
	v := mime.TypeByExtension(".html")
	fmt.Println(v) // text/html; charset=utf-8

	mediatype, params, _ := mime.ParseMediaType(v)
	fmt.Println(mediatype, params, params["charset"])	// text/html map[charset:utf-8] utf-8
}
```