# package json
```go
import "encoding/josn"
```
> json包实现了json对象的编解码。会使用encoding，encoding 包定义了供其它包使用的可以将数据在字节水平和文本表示之间转换的接口。

## type InvalidUTF8Error
```go
type InvalidUTF8Error struct {
	S string
}
```
> 1.2 版本不会再出现，为兼容保留。1.2 之前当试图编码一个包含非法 utf-8 序列的字符串时会返回本错误。1.2 后编码器会强行将非法字节替换为 unicode 字符 U+FFFD 来使字符串合法。

## type InvalidUnmarshalError
```go
type InvalidUnmarshalError strct {
	Type refledt.Type
}
```
> InvalidUnmarshalError 用于描述一个传递给解码器的非法参数。

## func (*InvalidUnmarshalError)Error
```go
func (e *InvalidUnmarshalError)Error()string
```

## type SyntaxError
```go
type SyntaxError struct {
	Offset int64 // 在读取 Offset 个字节后出现错误
	//
}
```
> SyntaxError 表示一个 json 语法错误。

## func (*SyntaxError)Error
```go
func (e *SyntaxError)Error()string
```

## type UnmarshalFieldError
```go
type UnmarshalFieldError struct {
	Key string
	Type reflect.Type
	Field reflect.StructField
}
```
> UnmarshalFieldError 表示一个 json 对象的键指向一个非导出字段。(因此不能写入；已补在使用，兼容保留)

## func (*UnmarshalFieldError)Error
```go
func (e *UnmarshalFieldError)Error()string
```

## type UnmarshalTypeError
```go
type UnmarshalTypeError struct {
	Value string	// 描述 json 值："bool”， “array”， “number -5“
	Type reflect.Type // 不能转化为的 go 类型 
}
```
> UnmarshalTypeError 表示一个 json 值不能转化为特定的 go 类型的值。

## func (*UnmarshalTypeError)Error
```go
func (e *UnmarshalTypeError)Error()string
```
## type UnsupportedTypeError
```go
type UnsupportedTypeError struct {
	Type reflect.Type
}
```
> UnsupportedTypeError 表示试图编码一个不智齿类型的值。

## func (*UnsupportedTypeError)Error
```go
func (e *UnsupportedTypeError)Error()string
```

## type UnsupportedValueError
```go
type UnsupportedValueError struct {
	Value reflect.Value
    Str   string
}
```

## func (*UnsupportedValueError)Error
```go
func (e *UnsupportedValueError)Error()string
```

## type MashalerError
```go
type MarshalerError struct {
	Type reflect.Type
	Err error
}
```

## func (*MarshalerError)Error
```go
func (e *MarshalerError)Error()string
```
