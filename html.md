# package html
```go
import "html"
```
> html 提供了用于转义和解转义 HTML 文本的函数

## func EscapeString
```go
func EscapeString(s string)string
```
> EscapeString 函数将特定的一些字符转为 html 实体。

## func UnescapeString
```go
func UnescapeString(s string)string
```
> UnescapeString函数将逸码的字符实体如"&lt;"修改为原字符"<"。它会解码一个很大范围内的字符实体，远比函数EscapeString转码范围大得多。例如"&aacute;"解为"á"，"&#225;"和"&xE1;"也会解码为该字符。

### Example
```go
package main

import (
	"fmt"
	"html"
)

func main() {
	s := html.EscapeString(">")
	fmt.Println(s)

	fmt.Println(html.UnescapeString(s))
}
```