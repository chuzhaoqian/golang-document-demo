# package filepath
```go
import "path/filepath"
```
> 实现了兼容各操作系统额文件路径的实用操作函数

```go
const (
    Separator     = os.PathSeparator
    ListSeparator = os.PathListSeparator
)
```

```go
var ErrBadPattern = errors.New("syntax error in pattern")
```
> ErrBadPattern表示一个glob模式匹配字符串的格式错误。

```go
var SkipDir = errors.New("skip this directory")
```
> 用作WalkFunc类型的返回值，表示该次调用的path参数指定的目录应被跳过。本错误不应被任何其他函数返回。

## func IsAbs
```go
func IsAbs(path string)bool
```
> IsAbs 返回路径是否一个绝对路径

## func Abs
```go
func Abs(path string)(string, error)
```
> Abs函数返回path代表的绝对路径，如果path不是绝对路径，会加入当前工作目录以使之成为绝对路径。因为硬链接的存在，不能保证返回的绝对路径是唯一指向该地址的绝对路径。

## func Rel
```go
func Rel(basepath, targpath string)(string, error)
```
> Rel函数返回一个相对路径，将basepath和该路径用路径分隔符连起来的新路径在词法上等价于targpath。也就是说，Join(basepath, Rel(basepath, targpath))等价于targpath本身。如果成功执行，返回值总是相对于basepath的，即使basepath和targpath没有共享的路径元素。如果两个参数一个是相对路径而另一个是绝对路径，或者targpath无法表示为相对于basepath的路径，将返回错误。

## func SplitList
```go
func SplitList(path string)[]string
```
> 将PATH或GOPATH等环境变量里的多个路径分割开（这些路径被OS特定的表分隔符连接起来）。与strings.Split函数的不同之处是：对""，SplitList返回[]string{}，而strings.Split返回[]string{""}。

## func Split 
```go
func split(path string)(dir, file string)
```
> Split 函数将路径从最后一个路径分隔符后面位置分隔为两个部分（dir和file）并返回。如果路径中没有路径分隔符，函数返回值dir会设为空字符串，file 会设为path。两个返回值满足 path == dir+file。

## func Join
```go
func Join(elem ...string)string
```
> Join 函数可以将任意数量的路径元素放入一个单一路径里，会根据需要添加路径分隔符。结果是经过简化的，所有的空字符串元素会被忽略。

## func Fr omSlash
```go
func FromSlash(path string)string
```
> FromSlash 函数将path中的斜杠（'/'）替换为路径分隔符并返回替换结果，多个斜杠会替换为多个路径分隔符

## func ToSlash
```go
func ToSlash(path string)string
```
> ToSlash 函数将path中的路径分隔符替换为斜杠（'/'）并返回替换结果，多个路径分隔符会替换为多个斜杠。

## func VolumeName
```go
func VolumeName(path string)(v string)
```
> VolumeName函数返回最前面的卷名。如Windows系统里提供参数"C:\foo\bar"会返回"C:"；Unix/linux系统的"\\host\share\foo"会返回"\\host\share"；其他平台会返回""。

## func Dir 
```go
func Dir(path string)string
```
> Dir返回路径除去最后一个路径元素的部分，即该路径最后一个元素所在的目录。在使用Split去掉最后一个元素后，会简化路径并去掉末尾的斜杠。如果路径是空字符串，会返回"."；如果路径由1到多个路径分隔符后跟0到多个非路径分隔符字符组成，会返回单个路径分隔符；其他任何情况下都不会返回以路径分隔符结尾的路径。

## func Base 
```go
func Base(path string)string
```
> Base 函数返回路径的最后一个元素。在提取元素前会求掉末尾的路径分隔符。如果路径是""，会返回"."；如果路径是只有一个斜杆构成，会返回单个路径分隔符。

## func Ext
```go
func Ext(path string)string
```
> Ext 函数返回 path 文件扩展名。返回值是路径最后一个路径元素的最后一个'.'起始的后缀（包括'.'）。如果该元素没有'.'会返回空字符串。

## func Clean
```go
func Clean(path string)string
```
> Clean函数通过单纯的词法操作返回和path代表同一地址的最短路径。返回的路径只有其代表一个根地址时才以路径分隔符结尾，如Unix的"/"或Windows的`C:\`。如果处理的结果是空字符串，Clean会返回"."

## func EvalSymlinks
```go
func EvalSymlinks(path string)(string, error)
```
> EvalSymlinks 函数返回 path 指向的符号链接(链接)所保航的路径。如果path和返回值都是相对路径，会相对于当前目录；除非两个路径其中一个是绝对路径。

## func Match
```go
func Match(pattern, name string)(matched bool, err error)
```
> 如果 name 匹配 shell 文件名模式匹配字符串，Match函数返回真。Match要求匹配整个name字符串，而不是它的一部分。只有pattern语法错误时，会返回ErrBadPattern。

## func Glob
```go
func Glob(pattern string)(natches []string, err error)
```
> Glob 函数返回所有匹配模式匹配字符串 pattern 的文件或者 nil（如果没有匹配的文件）。pattern 的语法和 Match 函数相同。 pattern 可以描述多层的名字，如 /usr/*/bin/ed（假设路径分隔符是'/'）。

## type WalkFunc
## func Walk 
## func HasPrefix










