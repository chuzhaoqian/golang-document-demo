# package fmt
```go
import "fmt"
```
> fmt 格式化 I/O

## type Stringer
```go
type Stringer interface{
	String() string 
}
```
> 实现了 Stringer 接口的类型 (既有 String 方法)，定义了该类型值得原始显示。当采用任何接受字符的 verb (%v %s %q %x %X)动作格式化一个操作数时，或者被不使用格式字符串如 Print 函数打印操作数时，会调用 String 方法来生成输出的文本。

## type GoStringer
```go
type GoStringer interface {
	GoString() string
}
```
> 实现了 GoStringer 接口的类型(既有 GoString 方法)，定义了该类型值得go语法表示。 当采用 verb %#v 格式化一个操作时，会调用 GoString 方法来生成输出的文本。

## type State
```go
type State interface {
	// Write 方法用来写入格式化文本
	Write(b []byte)(ret int, err error)
	// Width 返回宽度值，及其是否被设置
	Width()(wid int, ok bool)
	// Precision 返回精度值，及其是否被设置
	Precision()(prec int, ok bool)
	// Flag 报告是否设置了 flag c (一个字符，如+、-、#等)
	Flag(c int)bool
}
```
> State 代表一个传递给自定义 Formatter 接口的 Format 方法的打印环境。它实现了 io.Writer 接口用来写入格式化文本，还提供了该操作数的格式字符串指定的选项和宽度、精度信息(通过调用方法)。

## type Formatter
```go
type Formatter interface {
	// c 为 verb, f 提供 verb 的细节信息和 Write 方法用于写入生成的格式化文本。
	Format(f State, c rune)
}
```
> 实现了 Formatter 接口的类型可以定制自己的格式化输出。Format 方法的实现内部可以调用 Sprint 或 Fprint 等函数来生成吱声的输出。

## type ScanState
```go
type ScanState interface {
	// 从输入读取下一个 rune ,在读取超过指定宽度时会返回 EOF
	// 如果在 Scanln、Fscanln 或 Sscanln 中被调用，本方法会在返回第一个 ‘、n’ 后再次调用时返回 EOF
	ReadRune()(r rune, size int, err error)
	// UnreadRune 方法让下一次调用 ReadRune 时返回上一次返回的 rune 且不移动读取位置
	UnreadRune()error
	// SkipSpace 方法跳过输入中的空白，换行
	// 在 Scanln、Fscanln 或 Sscanln 中被调用时，换行被视为 EOF
	SkipSpace()
	// 方法从输入中一次读取 rune 并用 f 测试，直到 f 返回假；将读取的 rune 组织为一个 []byte 切片返回。
	// 如果 skipSpace 参数为真，本方法会先跳过输入中的空白。
	// 如果为 f 为 nil，会使用 !unicode.IsSpace(c); 就户名返回值 token 将为一串非空字符。
	// 换行被视为空白，在 Scanln、Fscanln 或 Sscanln 中被调用时，换行被视为 EOF
	// 返回的切片指向一个共享内存，可能被下一次调用 Token 方法时重写
	// 或被使用该 Scanstate 的另一个 Scan 函数重写；或在本次调用的 Scan 方法返回时重写
	Token(skipSpace bool, f func(rune)bool)(token []byte, err error)
	// Width 返回宽度值，及时是否被设置。单位是 unicode 码值
	Width()(wid int, ok bool)
	// 因为本接口实现了 ReadRune 方法，Read 方法永远不应被 Scanner 接口使用
	// 一个合法的 ScanStat 接口实现可能会选择让本方法总是返回错误。
	Read(buf []byte)(n int, err error)
}
```
> ScanState 代表一个将传递给 Scanner 接口的 Scan 方法的扫描环境。 Scan 函数中，可以进行一次一个 rune 的扫描，或者使用 Token 方法获得下一个 token (比如空白分隔的 token)

## type Scanner
```go
type Scanner interface {
	Scan(state ScanState, verb rune)error
}
```
> 当 Scan、Scanf、Scanln 或 类似函数接受实现了 Scanner 接口的类型(其 Scan 方法的 receiver 必须是指针，该方法从输入读取该类型值得字符串表示并将结果写入 recerver)作为参数时，会调用其 Scan 方法进行定制扫描。

## func Printf
```go
func Printf(format string, a ...interface{})(n int, error)
```
> Peintf 根据 format 参数生成格式化的字符串并写入标准输出。返回写入的字节数和遇到的任何错误。

## func Fprintf
```go
func Fprintf(w io.Writer, format string, a ...interface{})(n int, err error)
```
> Fprintf 根据 format 参数生成格式化的字符串并写入 w。返回写入的字节数和遇到的任何错误。

## func Sprintf
```go
func Sprintf(format string, a ...interface{})string
```
> Sprintf 更具 format 参数生成格式化的字符串并返回该字符串。

## func Print
```go
func Print(a ...interface{})(n int, err error)
```
> Print 采用默认格式将其参数格式化并写入标准输出。如果像个相邻的参数都不是字符串，会在他们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

## func Fprint
```go
func Fprint(w io.Writer, a ...interface{})(n int, err error)
```
> Fprint 采用默认格式将其参数格式化并写入 w 。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

## func Sprint
```go
func Sprint(a ...interface{})string
```
> Sprint 采用默认格式将其参数格式化，串联所有输出并返回一个字符串。如果两个相邻的参数都不是字符串，会在它们之间添加空格。

## func Println
```go
func Println(a ...interface{})(n int, err error)
```
> Println 采用默认格式将其参数格式化并写入标准输出。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的自己数和遇到的任何错误。

## func Fprintln
```go
func Fprintln(w io.Writer, a ...interface{})(n int, err error)
```
> Fprintln 采用默认格式将其参数格式化写入 w io.Writer。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。

## func Sprintln
```go
func Sprintln(a ...interface{})string
```
> Sprintln 采用默认格式将其参数格式化，串联所有输出并返回一个字符串。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。

## func Errorf
```go
func Errorf(format string, a ...interface{})error
```
> Errorf 根据 format 参数生成格式化字符串并返回一个包含该字符串的错误。 同 errors 包。

## func Scanf
```go
func Scanf(format string, a ...interface{})(n int, err error)
```
> Scanf 从标准输入扫描文本，根据 format 参数指定的格式将成功读取的空白分割的值保存进行成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

## func Fscanf
```go
func Fscanf(r io.Reader, format string, a ...interface{})(n int, err error)
```
> Fscanf 从 r 扫描文本，根据 format 参数指定的格式将成功读取的空白分割的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

## func Sscanf
```go
func Sscanf(str string, format string, a ...interface{})(n int, err error)
```
> Sscanf 从字符串 str 扫描文本，根据 format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

## func Scan
```go
func Scan(a ...interface{})(n int, err error)
```
> Scan 从标准输入扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回错误。

## func Fscan
```go
func Fscan(r io.Reader, a ...interface{})(n int, err error)
```
> Fscan 从 r 扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。幻皇视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误。

## func Sscan 
```go
func Sscan(str string, a ...interface{})(n int, err error)
```
> Sscan 从字符串 str 扫描文本，将成功读取的空白分割的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

## func Scanln
```go
func Scanln(a ...interface{}) (n int, err error)
```
> Scanln类似Scan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

## func Fscanln
```go
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
```
> Fscanln 类似 Fscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

## func Sscanln
```go
func Sscanln(str string, a ...interface{}) (n int, err error)
```
> Sscanln类似Sscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

### Example
```go
package main

import (
	"fmt"
)

func main() {
	s := "http://www.baidu.com"
	i := 1
	f := 1.0

	fmt.Printf("%T \n", s) // string
	fmt.Printf("%T \n", i) // int
	fmt.Printf("%T \n", f) // float64

	fmt.Printf("%v \n", s)
	fmt.Printf("%+v \n", s)
	fmt.Printf("%#v \n", s) // "http://www.baidu.com" 多引号
	fmt.Printf("%#v \n", i) // 1

	fmt.Printf("%d \n", i)
	_, err := fmt.Printf("%9.1f \n", i) //	%!f(int=        1)
	fmt.Println(err)                    // <nil>
	fmt.Printf("%9.1f \n", f)
	
	fmt.Fprintf(os.Stdout, "%s\n", i) // %!s(int=1)
	fmt.Fprintf(os.Stdout, "%s\n", s)
}
```

```go
package main

import (
	"fmt"
	"strings"
	//	"os"
)

func main() {
	s := "I am Golang ,age 4 岁"
	sr := strings.NewReader(s)
	var name string
	var age int

	fmt.Fscanf(sr, "I am %s ,age %d", &name, &age)
	fmt.Printf("%s %d \n", name, age)
}
```

```go
package main

import (
	"fmt"
)

func main() {
	var s string
	fmt.Println("请输入：")
	fmt.Scan(&s)
	fmt.Println("输入的是", s)
}
```