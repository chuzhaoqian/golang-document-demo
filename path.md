# package path
```go
import "path"
```
> path 实现了对斜杠分隔的路径的使用操作函数

```go
var ErrBadPattern = errors.New("syntax error in pattern")
```
> ErrBadPattern 表示一个 glob 模式匹配字符串的格式错误

## func IsAbs
```go
func IsAbs(path string)bool
```
> IsAbs返回路径是否是一个绝对路径。

## func Split
```go
func Split(path string)(dir, file string)
```
> Split 函数将路径从最后一个斜杠后面位置分隔为两个部分(dir 和 file)并返回。如果路径中没有斜杠，函数返回值 dir 会设为空字符串，file 会设为 path。两个返回值满足path == dir+file。

## func Join
```go
func Join(elem ...string)string
```
> Join函数可以将任意数量的路径元素放入一个单一路径里，会根据需要添加斜杠。结果是经过简化的，所有的空字符串元素会被忽略。

## func Dir 
```go
func Dir(path string)string
```
> Dir返回路径除去最后一个路径元素的部分，即该路径最后一个元素所在的目录。在使用Split去掉最后一个元素后，会简化路径并去掉末尾的斜杠。如果路径是空字符串，会返回"."；如果路径由1到多个斜杠后跟0到多个非斜杠字符组成，会返回"/"；其他任何情况下都不会返回以斜杠结尾的路径。

## func Base
```go
func Base(path string)string
```
> Base函数返回路径的最后一个元素。在提取元素前会求掉末尾的斜杠。如果路径是""，会返回"."；如果路径是只有一个斜杆构成，会返回"/"。

## func Ext
```go
func Ext(path string)string
```
> Ext 函数返回 path 文件扩展名。返回值是路径最后一个斜杠分隔出的路径元素的最后一个'.'起始的后缀（包括'.'）。如果该元素没有'.'会返回空字符串。

## func Clean
```go
func Clean(path string)string
```
> Clean函数通过单纯的词法操作返回和path代表同一地址的最短路径。


## func Match
```go
func Match(pattern, name string)(matched bool, err error)
```
> 如果 name 匹配 shell 文件名模式匹配字符串，Match函数返回真。Match要求匹配整个name字符串，而不是它的一部分。只有pattern语法错误时，会返回ErrBadPattern。