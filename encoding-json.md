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
## type Number
```go
type Number string
```
> Number 类型代表一个 json 数字字面量。

## func (Number)Int64
```go
func (n Number)Int64()(int64, error)
```
> 将该数字作为 int64 类型返回。

## func (Number)Float64
```go
func (n Number)Float64()(float64, error)
```
> 将该数字作为 float64 类型返回

## func (NUmber)String
```go
func (n Number)String()string
```
> 返回该数字额字面值文本表示。

## type RawMessage
```go
type RawMessage []byte
```
> RawMessage 类型是一个保持原本编码的 json 对象。本类型事项了 Marshaler 和 Unmarshaler 接口，用于延迟 json 的解码或者预计算 json 的编码。

## func (*RawMessage)MarshalJSON
```go
func (m *RawMessage)MarshalJSON()([]byte, error)
```
> MarshalJSON 返回 *m 的json 编码。

## func (*RawMessage)UnmarshalJSON
```go
func (m *RawMessage)UnmarshalJSON(data []byte)error
```
> UnmarshalJSON 将 *m 设为 data 的一个拷贝。

## type Marshaler
```go
type Marshaler interface {
	MarshalJson()([]byte, error)
}
```
> 实现了 Marshaler 接口的类型可以将自身序列化为合法的 json 描述。

## type Unmarshaler
```go
type Unmarshaler interface {
	UnmarshalJSON([]byte)error
}
```
> 实现了 Unmarshaler 接口的对象可以将吱声的 json 描述反序列化。该方法可以认为输入是合法的 json 字符串。如果要在方法返回后保存自身的 json 数据，必须进行拷贝。

## func Compact
```go
func Compact(dst *bytes.Buffer, src []byte)error
```
> Compact 函数将 json 编码的 src 中无用的空白字符串剔除后写入 dst。

## func HTMLEscape
```go
func HTMLEscape(dst *bytes.Buffer, src []byte)
```
> HTMLEscape 函数将json编码的src中的<、>、&、U+2028 和U+2029字符替换为\u003c、\u003e、\u0026、\u2028、\u2029 转义字符串，以便json编码可以安全的嵌入HTML的<script>标签里。因为历史原因，网络浏览器不支持在<script>标签中使用标准HTML转义， 因此必须使用另一种json编码方案。

## func Indent
```go
func Indent(dst *bytes.Buffer, src []byte, prefix,indent string)error
```
> Indent函数将json编码的调整缩进之后写入dst(格式化、排版)。每一个json元素/数组都另起一行开始，以prefix为起始，一或多个indent缩进（数目看嵌套层数）。写入dst的数据起始没有prefix字符，也没有indent字符，最后也不换行，因此可以更好的嵌入其他格式化后的json数据里。

## func Marshal 
```go
func Marshal(v interface{})([]byte, error)
```
> Marshal 函数返回 v interface{} 的 json 编码。
>
> Marshal 函数会地柜的处理值。如果一个值实现了 Marshaler 接口且非 nil 指针，会调用其他 MarshalJSON 方法来生成 json 编码。nil 指针异常并不是严格必需的，但会模拟与 UnmarshalJSON 的行为类似的必需的异常。否则，Marshal 函数使用下面的基于类型的默认编码格式：布尔类型编码为 json 布尔类型。浮点数、整数和 Number 类型的值编码为 json 数字类型。字符串编码为 json 字符串。角括号"<"和">"会转义为"\u003c"和"\u003e"以避免某些浏览器吧json输出错误理解为HTML。基于同样的原因，"&"转义为"\u0026"。数组和切片类型的值编码为json数组，但[]byte编码为base64编码字符串，nil切片编码为null。结构体的值编码为json对象。每一个导出字段变成该对象的一个成员，除非：
- 字段的标签是"-"
- 字段是空值，而其标签指定了omitempty选项

> 空值是false、0、""、nil指针、nil接口、长度为0的数组、切片、映射。对象默认键字符串是结构体的字段名，但可以在结构体字段的标签里指定。结构体标签值里的"json"键为键名，后跟可选的逗号和选项，举例如下：

```go
// 字段被本包忽略
Field int `json:"-"`
// 字段在json里的键为"myName"
Field int `json:"myName"`
// 字段在json里的键为"myName"且如果字段为空值将在对象中省略掉
Field int `json:"myName,omitempty"`
// 字段在json里的键为"Field"（默认值），但如果字段为空值会跳过；注意前导的逗号
Field int `json:",omitempty"`
```

> "string"选项标记一个字段在编码json时应编码为字符串。它只适用于字符串、浮点数、整数类型的字段。这个额外水平的编码选项有时候会用于和javascript程序交互：

```go
Int64String int64 `json:",string"`
```
> 如果键名是只含有unicode字符、数字、美元符号、百分号、连字符、下划线和斜杠的非空字符串，将使用它代替字段名。匿名的结构体字段一般序列化为他们内部的导出字段就好像位于外层结构体中一样。如果一个匿名结构体字段的标签给其提供了键名，则会使用键名代替字段名，而不视为匿名。Go结构体字段的可视性规则用于供json决定那个字段应该序列化或反序列化时是经过修正了的。如果同一层次有多个（匿名）字段且该层次是最小嵌套的（嵌套层次则使用默认go规则），会应用如下额外规则：

- 1）json标签为"-"的匿名字段强行忽略，不作考虑；

- 2）json标签提供了键名的匿名字段，视为非匿名字段；

- 3）其余字段中如果只有一个匿名字段，则使用该字段；

- 4）其余字段中如果有多个匿名字段，但压平后不会出现冲突，所有匿名字段压平；

- 5）其余字段中如果有多个匿名字段，但压平后出现冲突，全部忽略，不产生错误。

> 对匿名结构体字段的管理是从go1.1开始的，在之前的版本，匿名字段会直接忽略掉。映射类型的值编码为json对象。映射的键必须是字符串，对象的键直接使用映射的键。指针类型的值编码为其指向的值（的json编码）。nil指针编码为null。接口类型的值编码为接口内保持的具体类型的值（的json编码）。nil接口编码为null。通道、复数、函数类型的值不能编码进json。尝试编码它们会导致Marshal函数返回UnsupportedTypeError。Json不能表示循环的数据结构，将一个循环的结构提供给Marshal函数会导致无休止的循环。

## func MarshalIndent
```go
func MarshalIndent(v interface{}, prefix, indent string)([]byte, error)
```
> MarshalIndent 类似 Marshal 但会使用缩进将输出格式化。

## func Unmarshal
```go
func Unmarshal(data []byte, v interface{})error
```
> Unmarshal 函数解析 json 编码的数据并将结果存入 v 指向的值。 Unmarshal 和 Marshal 做相反操作，必要时申请映射、切片或指针，有如下附加规则：
>
> 要将 json 数据解码写入一个指针，Unmarshal 函数首先处理 json 数据是 json 字面值 null 的情况。此时，函数将指针设为 nil; 否则，函数将 json 数据解码写入指针指向的值；如果指针本身就是 nil ,函数会先申请一个值并使指针指向它。
>
> 要将 json 数据解码写入一个结构体，函数会匹配输入对象的键和 Marshal 使用的键(结构体字段名或它的标签指定的键名)，优先选择精确的匹配，但也接受大小写不敏感的匹配。
>
> 要将 json 数据解码写入一个接口类型的值，函数会将数据解码如下类型写入接口：Bool                   对应JSON布尔类型,float64 对应JSON数字类型,string 对应JSON字符串类型,interface{} 对应JSON数组,map[string]interface{} 对应JSON对象,nil 对应JSON的null
>
> 如果一个 JSON 值不匹配给出的目标类型，或如果一个 json 数字写入目标类型时溢出， Unmarshal 函数会跳过该字段并尽量会完成其余的解码操作。如果没有出现更加严重的错误，本函数会返回一个描述第一个此类错误的详细信息的 UnmarshalTypeError。
>
> JSON 的 null 值解码为 go 的接口、指针、切片时会将他们设为 nil，因为 null 在 json 里一般表示”不存在“。解码 json 的null 值到其他 go 类型时,不会造成任何改变，也不会产生错误。
>
> 当解码字符串时，不合法的 utf-8 或 utf-16 字符不会视为错误，而是将非法字符替换为 unicaode 字符 U+FFFD。

## type Decoder
```go
type Decoder struct {
	
}
```
> Decoder 从输入流解码 json 对象。

## func NewDecoder
```go
func NewDecoder(r io.Reader)*Decoder
```
> NewDecoder 创建一个从 r 读取并解码 json 对象的 *Decaoder,解码器有自己的缓冲，并可能超前读取部分 json 数据。

## func (*Decoder)Buffered
```go
func (dec *Decoder)Buffered()io.Reader 
```
> Buffered 方法返回保存在 dec 缓存里数据的读取器，该返回值在下次调用 Decode 之前有效。

## func (*Decoder)UseNumber
```go
func (dec *Decoder)UseNumber()
```
> UseNumber 方法将 dec 设置为当接收端是 interface{} 接口时将 json 数字编码为 Number 类型而不是 float64 类型

## func(*Decoder)Decode
```go
func (dec *Decoder)Decode(v interface{})error
```
> Decode 从输入流读取下一个 json 编码值 并保存在 v 指向的值里。

## type Encoder
```go
type Encoder struct {
	
}
```
> Encoder 将 json 对象写入输出流。

## func NewEncoder
```go
func NewEncoder(w io.Writer)*Encoder
```
> NewEncoder 创建一个将数据写入 w 的 *Encoder。

## func (*Encoder)Encode
```go
func (enc *Encoder)Encode(v interface{})error
```
> Encode 将 v 的 json 编码写入输出流，并写入一个换行符。
