# package log
```go
import "log"
```
> log 实现了简单的日志服务。定义了 Logger 类型，提供了一些格式化输出的方法。提供了一个预定义的”标准“ Logger,可以通过辅助函数 Print[f|ln]、Fatal[f|ln]和Panic[f|ln]访问。Logger 会打印每条日志信息的日期、时间，默认输出到标准错误。Fatal 系列会在写入日志信息后调用 os.Exit(1)。Panic 系列函数会在写入日志信息后 panic。

```go
const (
	// 控制输出日志信息的细节。不能控制输出的顺序和格式。
	// 在所有项目后会有一个冒号：
	Ldate = 1 << iota	// 日期
	Ltime	// 时间
	Lmicroseconds	// 微妙
	Llongfile	// 文件路径名 + 行号
	Lshortfile	// 文件无路径名 + 行号
	LstdFags = Ldate | Ltime // 标准 logger 的初始值
)
```
> 定义 Logger 类型如何生成用于每条日志的前缀文本。

## type Logger
```go
type Logger struct {
	//
}
```
> Logger 类型表示一个活动状态的记录日志的对象，它会生成一行行的输出写入一个 io.Writer 接口。每一条日志操作会调用一次 io.Writer 接口的 Write 方法。 Logger 类型的对象可以被多个线程安全的同时使用，它会保证对 io.Writer 接口的顺序访问。

## func New
```go
func New(out io.Writer, prefix string, flag int)*Logger
```
> New 创建一个 Logger。参数 out 设置日志信息写入的目的地。参数 prefix 会加添到生成的每一条日志前面。参数 flag 定义日志的属性(时间、文件等)

## func (*Logger)Flags
```go
func (l *Logger)flags()int
```
> Flags 返回 logger 的输出选项。

## func (*Logger)SetFlags
```go
func (l *Logger)Flags()int
```
> SetFlags 设置 logger 的输出选项。

## func (*Logger)Prefix 
```go
func (l *Logger)Prefix()string
```
> Prefix 返回 logger 的输出前缀

## func (*Logger)SetPrefix
```go
func (l *Logger)SetPrefix(prefix string)
```
> SetPrefix 设置 logger 的输出前缀。

## func (*Logger)Output
```go
func (l *Logger)Output(calldepth int, s string)error
```
> Output 写入输出一次日志时间。板书 s 包含 Logger 根据选项生成的前缀之后要打印的文本。如果 s 末尾没有切换行会添加换行符。calldepth 用于恢复 PC，用于一般性而提供，但目前在所有预定义的路径上它的值都为 2 。

## func (*Logger)Print
```go
func (l *Logger)Print(v ...interface{})
```
> Print 调用 l.Output 将生成的格式化字符串输出到 logger,参数用和 fmt.Print 相同的方法处理。

## func (*Logger)Println
```go
func (l *Logger)Println(v ...inerface{})
```
> Println 调用 l.Output 将生成的格式化字符串输出到 logger,参数用和 fmt.Println 同样的方法处理。

## func (*Logger)Fatalf
```go
func (l *Logger)Fatalf(format string, v ...interface{})
```
> Fatal 等价于 {l.Printf(v...);os.Exit(1)}

## func (*Logger)Fatal
```go
func (l *Logger)Fatal(v ...interface{})
```
> Fatal 等价于 {l.Print(v...); os.Exit(1)}

## func (*Logger)Fatalln
```go
func (l *Logger) Fatalln(v ...interface{})
```
> Fatalln等价于{l.Println(v...); os.Exit(1)}

## func (*Logger)Panicf
```go
func (l *Logger) Panicf(format string, v ...interface{})
```
> Panicf等价于{l.Printf(v...); panic(...)}

## func (*Logger)Panic
```go
func (l *Logger) Panic(v ...interface{})
```

## func (*Logger)Panicln
```go
func (l *Logger) Panicln(v ...interface{})
```

## func Flags
```go
func Flags()int
```
> Flags 返回标准 logger 的输出选项。

## func SetFlags
```go
func SetFlags(flag int)
```
> SetFlags 设置标准 logger 的输出选项。

## func Prefix
```go
func Prefix()string
```
> Prefix 返回标准 logger 的输出前缀。

## func SetPrefix
```go
func SetPrefix(prefix string)
```
> SetPrefix 设置标准 logger 的输出前缀。

## func SetOutput
```go
func SetOutput(w io.jWriter)
```
> SetOutput设置标准logger的输出目的地，默认是标准错误输出。

## func Printf
```go
func Printf(format string, v ...interface{})
```

## func Print
```go
func Print(v ...interface{})
```

## func Println
```go
func Println(v ...interface{})
```

## func Fatalf
```go
func Fatalf(format string, v ...interface{})
```

## func Fatal
```go
func Fatal(v ...interface{})
```

## func Fatalln
```go
func Fatalln(v ...interface{})
```

## func Panicf
```go
func Panicf(format string, v ...interface{})
```

## func Panic
```go
func Panic(v ...interface{})
```

## func Panicln
```go
func Panicln(v ...interface{})
```