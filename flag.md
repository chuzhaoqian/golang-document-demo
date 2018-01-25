# package flag
```go
import "flag"
```
> flag 实现了命令行参数的解析。

```go
var CommandLine = NewFlagSet(os.Args[0], ExitOnError)
```
> CommandLine 是默认的命令行 flag 集，用于解析 os.Args。

```go
var ErrHelp = errorsNew("flag: help requested")
```
> 当 flag -help 被调用但没有注册这个 flag 时,就会返回 ErrHelp。

```go
var Usage = func(){
	fmt.Fprintf(os.Stderr, "Usage of %s:\n", os.Args[0])
	PrintDefaults()
}
```
> Usage 打印到标准错误输出一个使用信息，记录了所有注册的 flag。

## type Value
```go
type Value interface {
	String() string
	Set(string) error
}
```
> Value 接口是用于将动态的值保存在一个 flag 里。
>
> 如果 Value 接口具有 IsBoolFlag() bool 方法，且返回真。命令行解析其会将 -name 等价为 -name=true 而不是使用下一个命令行参数。

## type Getter
```go
type Getter interface {
	Value
	Get() interface{}
}
```
> Getter 接口可以取回 Value 接口的内容。包装了 Value 接口而不是作为 Value 接口的一部分，因为本接口是在 Go 1之后出现，处于兼容。本包所有的满足 Value 接口的类型都同时满足 Getter 接口。

## type ErrorHanding
```go
type ErrorHandling int
```
> ErrorHandling 定义如何处理 flag 解析错误。

```go
const (
	ContinueOnerror ErrorHandling = iota
	ExitOnError
	panicOnError
)
```

## type Flag 
```go
type Flag struct {
	Name string	// flag 在命令行中的名字
	Usage string	// 帮助信息
	Value Value	// 要设置的值
	DefValue string	// 默认值(文本格式)，用于使用信息
}
```
> Flag 类型代表一条 flag 的状态。

## type FlagSet
```go
type FlagSet struct {
	// Usage 函数在解析 flag 出现错误时会被调用
	// 该字段为一个函数(非采用方法)，以便修改为自定义的错误处理函数。
	Usage func()
	//
}
```
> FlagSet 代表一个已注册的 flag 集合。FlagSet 零值没有名字，采用 ContinueOnError 错误处理策略。

## func NewFlagSet
```go
func NewFlagSet(name string, errorHandling ErrorHandling)*FlagSet
```
> NewFlagSet 创建一个新的名为 name, 采用 errorHanding 为错误处理策略的 FlagSet。

## func (*flagSet)Init
```go
func (f *FlagSet)Init(name string, errorHandling ErrorHandling)
```
> Init 设置 flag 集合 f 的名字和错误处理属性。FlagSet 零值没有名字，默认采用 ContinueOnError 错误处理策略。

## func (*FlagSet)NFlag
```go
func (f *FlagSet)NFlag()int
```
> Flag 返回解析时进行了设置的 flag 的数量。

## func (*FlagSet)Lookup
```go
func (f *FlagSet)Lookup(name string)*Flag
```
> 如果 flag 不存在返回 nil。

## func (*FlagSet)Args
```go
func (f *FlagSet)Args()[]string
```
> 返回解析之后剩下的非 flag 参数。(不包括命令名)

## func (*FlagSet)Arg
```go
func (f *FlagSet)Arg(i int)string
```
> 返回解析之后的第 i 个参数，从0开始索引。

## func (*FlagSet)PrintDefaults
```go
func (f *FlagSet)PrintDefaults()
```
> PrintDefaults 打印集合中所有注册好的 flag 的默认值。除非另外配置，默认输出到标准错误输出中。

## func (*FlagSet)SetOutput
```go
func (f *FlagSet)SetOutput(output io.Writer)
```
> 设置使用信息和错误信息的输出流，如果 output 为 nil，将使用 os.Stderr。 

## func (*FlagSet)Bool
```go
func (f *FlagSet)Bool(name string, balue bool, usage string)*bool
```
> Bool 用指定的名称、默认值、使用信息注册一个 bool 类型 flag。返回一个保存了该 flag 的值得指针。

## func (*FlagSet)BoolVar
```go
func (f *FlagSet)BoolVar(p *bool, name string, balue bool, usage string)
```
> BoolVar 用指定的名称、默认值、使用信息注册一个 bool 类型 flag，并将 flag 的值保存到 p 指向的变量。

## func (*FlagSet)Int
```go
func (f *FlagSet)Int(name string, value int, usage string)*int
```
> Int 用指定的名称、默认值、使用信息注册一个 int 类型 flag。返回一个保存了该 flag 的值得指针。

## func (*FlagSet)IntVar
```go
func (f *FlagSet)IntVar(p *int, name string, value int, usage string)
```
> IntVar 用指定的名称、默认值、使用信息注册一个 int 类型 flag，并将 flag 的值保存到 p 指向的变量。

## func (*FlagSet)Int64
## func (*FlagSet) Int64Var
## func (*FlagSet) Uint
## func (*FlagSet) UintVar
## func (*FlagSet) Uint64
## func (*FlagSet) Uint64Var
## func (*FlagSet) Float64
## func (*FlagSet) Float64Var
## func (*FlagSet) String
## func (*FlagSet) StringVar
## func (*FlagSet) Duration
## func (*FlagSet) DurationVar

## func (*FlagSet)Var 
```go
func (f *FlagSet)Var(value Value, name string, usage string)
```
> Var 方法使用指定的名称、使用信息注册一个 flag。该 flag 的类型和值由第一个参数表示，该参数应实现了 Value 接口。例如，创建一个 flag，可以用 Value 接口的 Set 方法将逗号分隔的字符串转化为字符串切片。

## func (*FlagSet)Set
```go
func (f *FlagSet)Set(name, value string)error
```
> 设置已注册的 flag 的值。

## func (*FlagSet)Parse
```go
func (f *FlagSet)Parse(arguments []string)error
```
> 从 arguments 中解析注册的 flag。必须在所有 flag 都注册好而未访问其值时执行。未注册却使用 flag -help 时，会返回 ErrHelp。

## func (f *FlagSet)Parsed
```go
func (f *FlagSet)Parsed()bool
```
> 返回是否 fParse 已经被调用过。

## func (*FlagSet)Visit
```go
func (f *FlagSet)Visit(fn func(*Flag))
```
> 按照字典顺序便利标签，并对每个标签调用 fn 。这个函数只便利解析时进行了设置的标签。

## func (*FlagSet)VisitAll
```go
func (f *FlagSet)VisitAll(fn func(*Flag))
```
> 按照字典顺序遍历标签，并且对每个标签调用fn。 这个函数会遍历所有标签，不管解析时有无进行设置。

## func NFlag
```go
func NFlag()int
```
> Nflag 返回已被设置的 flag 的数量。

## func Lookup
```go
func Lookup(name string)*Flag
```
> 返回已经注册 flag 的 Flag 结构体指针；如果 flag 不存在的话，返回 nil。

## func NArg
```go
func NArg()int
```
> NArg 返回解析 flag 之后剩余参数的个数。

## func Args
```go
func Args()[]string
```
> 返回解析后剩下的非 flag 参数。(不包括命令名)

## func Arg
```go
func Arg(i int)string
```
> 返回解析后剩下的第 i 个参数，从0开始索引。

## func PrintDefaults
```go
func PrintDefaults()
```
> PrintDefaults 会向标准错误输出写入所有注册好的 flag 的默认值。

## func Bool
```go
func Bool(name string, value bool, usage string)*bool
```
> Bool 用指定的名称、默认值、使用信息注册一个 bool 类型 flag。返回一个保存了该 flag 的值得指针。

## func BoolVar
```go
func BoolVar(p *bool, name string, value bool, usage string)
```
> BoolVar 用指定的名称、默认值、使用信息注册一个 bool 类型 flag，并将 flag 的值保存到 p 指向的变量。

## func Int
```go
func Int(name string, value int, usage string)*int
```
> Int 用指定的名称、默认值、使用信息注册一个 int 类型 flag。返回一个保存了该 flag 的值得指针。

## func IntVar
```go
func IntVar(p *int, name string, value int, usage string)
```
> IntVar 用指定的名称、默认值、使用信息注册一个int 类型 flag，并将 flag 的值保存到指向的变量。

## func Int64
```go
func Int64(name string, value int64, usage string)*int64
```
> Int64 用指定的名称、默认值、使用信息注册一个 int64 类型 flag。返回一个保存了该 flag 的值得指针。

## func Int64Var
```go
func Int64Var(p *int64, name string, value int64, usage string)
```
> Int64Var 用指定的名称、默认值、使用信息注册一个 int64 类型 flag。并将 flag 的值保存到 p 指向的变量。

## func Uint
```go
func Uint(name string, value uint, usage string)*unit
```
> Uint 用指定的名称、默认值、使用信息注册一个 unit 类型 flag。返回一个保存了该 flag 的值得指针。

## func Unit64
```go
func Uint64(name string, value unit64, usage string)*uint64
```
> Uint64 用指定的名称、默认值、使用信息注册一个 uint64 类型 flag。返回一个保存了该 flag 的值得指针。

## func Uint64Var
```go
func Uint64Var(p *unit64, name string, value uint64, usage string)*uint64
```
> Uint64Var 用指定的名称、默认值、使用信息注册一个 uint64 类型 flag，并将 flag 的值保存到 p 指向的变量。

## func Float64
```go
func Float64(name string, value float64, usage string)*float64
```
> Float64 用指定的名称、默认值、使用信息注册一个 float64 类型 flag。返回一个 保存了该 flag 的值得指针。

## func Float64Var
```go
func Float64Var(p *float64, name string, value float64, usage string)
```
> Float64Var 用指定的名称、默认值、使用信息注册一个 float64 类型 flag，并将 flag 的值保存到 p 指向的变量。

## func String
```go
func String(name string, value string, usage string)*string
```
> String 用指定的名称、默认值、使用信息注册一个 string 类型 flag。返回一个保存了该 flag 的值得指针。

## func StringVar
```go
func StringVar(p *string, name string, value string, usage string)
```
> StringVar 用指定的名称、默认值、使用信息注册一个 string 类型 flag,并将 flag 的值保存到 p 指向的变量。

## func Duration
```go
func Duration(name string, value time.Duration, usate string)*time.Duration
```
> Duration用指定的名称、默认值、使用信息注册一个time.Duration类型flag。返回一个保存了该flag的值的指针。

## func DurationVar
```go
func DurationVar(p *time.Duration, name string, value time.Duration, usage string)
```
> DurationVar用指定的名称、默认值、使用信息注册一个time.Duration类型flag，并将flag的值保存到p指向的变量。

## func Var
```go
func Var(value Value, name string, usage string)
```
> Var 方法使用指定的名称、使用信息注册一个 flag。该 flag 的类型和值由第一个参数表示，该参数实现了 Value 接口。例如，用户可以创建一个 flag，可以用 Value 接口的 Set 方法将逗号分隔的字符串转化为字符串切片。

## func Set
```go
func Set(name, value string)error
```
> 设置已注册的 flag 的值。

## func Parse
```go
func Parse()
```
> 从 os.Args[1:] 中解析注册的 flag。必须在所有 flag 都注册好而未访问其值时执行。未注册却使用 flag -help 时，会返回 ErrHelp。

## func Parsed
```go
func Parsed()bool
```
> 返回是否 Parse 已被调用过。

## func Visit
```go
func Visit(fn func(*Flag))
```
> 按照字典顺序遍历标签，并且对每个标签调用fn。 这个函数只遍历解析时进行了设置的标签。

## func VisitAll
```go
func VisitAll(fn func(*Flag))
```
> 按照字典顺序遍历标签，并且对每个标签调用fn。 这个函数会遍历所有标签，不管解析时有无进行设置。

