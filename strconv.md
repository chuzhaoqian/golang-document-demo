# package strconv
```go
import "strconv"
```
> strconv 实现了基本数据类型和其字符串表示的相互转化

```go
const IntSize = intSize
```
> IntSize 是 int 或 uint 类型的字位数

```go
var ErrRange = errors.New("value out of range")
```
> ErrRange 表示超出目标类型表示范围

```go
var ErrSyntax = errors.New("invalid syntax")
```
> ErrSyntax 表示不符合目标类型语法

## type NumError
```go
type NumError struct {
	Func string // 失败的函数（ParseBool、ParseInt、ParseUint、ParseFloat）
	Num string // 输入的字符串
	Err error // 失败的原因（ErrRange、ErrSyntax）
}
```
> NumError 表示一次失败的转换

## func (*NumError)Error 
```go
func (e *NumError)Error()string
```

## func IsPrint
```go
func IsPrint(r rune)bool
```
> 返回一个字符是否是可打印的，和unicode.IsPrint一样，r必须是：字母（广义）、数字、标点、符号、ASCII空格。

## func CanBackquote
```go
func CanBackquote(s string)bool
```
> 返回字符串 s 是否可以不被修改的表示为一个单行的、没有空格和tab之外控制字符的反引号字符串

## func Quote
```go
func Quote(s string)string
```
> 返回字符串s在go语法下的双引号字面值表示，控制字符、不可打印字符会进行转义。（如\t，\n，\xFF，\u0100）

## func QuoteToASCII
```go
func QuoteToASCII(s string)string
```
> 返回字符串s在go语法下的双引号字面值表示，控制字符和不可打印字符、非ASCII字符会进行转义

## func QuoteRune
```go
func QuoteRune(s string)string
```
> 返回字符r在go语法下的单引号字面值表示，控制字符、不可打印字符会进行转义。（如\t，\n，\xFF，\u0100）

## func QuoteRuneToASCII
```go
func QuoteRuneToASCII(r rune)string
```
> 返回字符r在go语法下的单引号字面值表示，控制字符、不可打印字符、非ASCII字符会进行转义

## func Unquote
```go
func Unquote(s string)(t string, err error)
```
> 函数假设s是一个单引号、双引号、反引号包围的go语法字符串，解析它并返回它表示的值。（如果是单引号括起来的，函数会认为s是go字符字面值，返回一个单字符的字符串）

## func UnquoteChar
```go
func UnquoteChar(s string, quote byte)(value rune, multibyte bool, tail string, err error)
```
> 函数假设s是一个表示字符的go语法字符串，解析它并返回四个值：
>
* value，表示一个rune值或者一个byte值
* multibyte，表示value是否是一个多字节的utf-8字符
* tail，表示字符串剩余的部分
* err，表示可能存在的语法错误
>
> quote参数为单引号时，函数认为单引号是语法字符，不接受未转义的单引号；双引号时，函数认为双引号是语法字符，不接受未转义的双引号；如果是零值，函数把单引号和双引号当成普通字符。

## func ParseBool
```go
func ParseBool(s string)(b bool, err error)
```
> 返回字符串便是的 bool 值。它接受1、0、t、f、T、F、true、false、True、False、TRUE、FALSE；否则返回错误。

## func ParseInt
```go
func ParseInt(s string, base int, bitSize int)(i int64, err error)
```
> 返回字符串表示的整数值，接受正负号。
> 
> base指定进制（2到36），如果base为0，则会从字符串前置判断，"0x"是16进制，"0"是8进制，否则是10进制；
> 
> bitSize指定结果必须能无溢出赋值的整数类型，0、8、16、32、64 分别代表 int、int8、int16、int32、int64；返回的err是*NumErr类型的，如果语法有误，err.Error = ErrSyntax；如果结果超出类型范围err.Error = ErrRange。

## func ParseUint
```go
func ParseUint(s string, base int, bitSize int)(n uint64, err error)
```
> ParseUint 类似 ParseInt 但不接受正负号，用于无符号整型。

## func ParseFloat
```go
func ParseFloat(s string, bitSize int)(f float64, err error)
```
> 解析一个表示浮点数的字符串并返回其值。
> 
> 如果s合乎语法规则，函数会返回最为接近s表示值的一个浮点数（使用IEEE754规范舍入）。bitSize指定了期望的接收类型，32是float32（返回值可以不改变精确值的赋值给float32），64是float64；返回值err是*NumErr类型的，语法有误的，err.Error=ErrSyntax；结果超出表示范围的，返回值f为±Inf，err.Error= ErrRange

## func FormatBool
```go
func FormatBool(b bool)string
```
> 根据b的值返回"true"或"false"。

## func FormatInt
```go
func FormatInt(i int64, base int)string
```
> 返回i的base进制的字符串表示。base 必须在2到36之间，结果中会使用小写字母'a'到'z'表示大于10的数字

## func FormatUint
```go
func FormatUint(i uint64, base int)string
```
> 是FormatInt的无符号整数版本

## func FormatFloat
```go
func FormatFloat(f float64, fmt byte, prec, bitSize int)string
```
> 函数将浮点数表示为字符串并返回。
> 
> bitSize表示f的来源类型（32：float32、64：float64），会据此进行舍入。
> 
> fmt表示格式：'f'（-ddd.dddd）、'b'（-ddddp±ddd，指数为二进制）、'e'（-d.dddde±dd，十进制指数）、'E'（-d.ddddE±dd，十进制指数）、'g'（指数很大时用'e'格式，否则'f'格式）、'G'（指数很大时用'E'格式，否则'f'格式）。
> 
> prec控制精度（排除指数部分）：对'f'、'e'、'E'，它表示小数点后的数字个数；对'g'、'G'，它控制总的数字个数。如果prec 为-1，则代表使用最少数量的、但又必需的数字来表示f。

## func Atoi
```go
func Atoi(s string)(i int, err error)
```
> Atoi是ParseInt(s, 10, 0)的简写。

## func Itoa
```go
func Itoa(i int)string
```
> Itoa是FormatInt(i, 10) 的简写。

## func AppendBool
```go
func AppendBool(dst []byte, b bool)[]byte
```
> 等价于append(dst, FormatBool(b)...)

## func AppendInt
```go
func AppendInt(dst []byte, i int64, base int)[]byte
```
> 等价于append(dst, FormatInt(I, base)...)

## func AppendUint
```go
func AppendUint(dst []byte, i uint64, base int) []byte
```
> 等价于append(dst, FormatUint(I, base)...)

## func AppendFloat
```go
func AppendFloat(dst []byte, f float64, fmt byte, prec int, bitSize int) []byte
```
> 等价于append(dst, FormatFloat(f, fmt, prec, bitSize)...)

## func AppendQuote
```go
func AppendQuote(dst []byte, s string) []byte
```
> 等价于append(dst, Quote(s)...)

## func AppendQuoteToASCII
```go
func AppendQuoteToASCII(dst []byte, s string) []byte
```
> 等价于append(dst, QuoteToASCII(s)...)

## func AppendQuoteRune
```go
func AppendQuoteRune(dst []byte, r rune) []byte
```
> 等价于append(dst, QuoteRune(r)...)

## func AppendQuoteRuneToASCII
```go
func AppendQuoteRuneToASCII(dst []byte, r rune) []byte
```
> 等价于append(dst, QuoteRuneToASCII(r)...)