# package bytes
```go
import "bytes"
```
> bytes 实现了操作 []byte 的常用函数。与 strings 类似。

```go
// MinRead 是被 Buffer.ReadFrom 传递给 Read 调用的最小尺寸。只要该 Buffer 在保存内容之外有最少 MinRead 字节的余量，其 ReadFrom 方法就不会增加底层的缓冲。
const MinRead = 512

// 如果内存中不能申请足够保存数据的缓冲，ErrTooLarge 就会被传递给 panic 函数。
var ErrToolarge = errors.New("bytes.buffer: too large")
```

## func Compare
```go
func Compare(a, b []byte)int
```
> Compare 返回一个整数表示两个 []byte 切片按字典比较的结果。0 相等 -1小于 1大于。nil 参数视为空切片。

## func Equal
```go
func Equal(a, b []byte)bool
```
> 判断两个切片的内容是否完全相同。

## func EqualFold
```go
func EqualFold(s,t []byte)bool
```
> 判断两个 utf-8 编码切片(将 unicode 大写、小写、标题三种格式字符视为相同)是否相等。

## func Runes
```go
func Runes(s []byte)[]rune
```
> Runes 函数返回和 s 等价的 []rune 切片。(将 utf-8 编码的 unicode 码值分别写入单个 rune)

## func HasPrefix
```go
func HasPrefix(s, prefix []byte)bool
```
> 判断 s 是否有齐纳最切片 prefix。

## func HasSuffix
```go
func HasPrefix(s, prefix []byte)bool
```
> 判断 s 是否有后缀切片 suffix


## func Contains
```go
func Contains(b, subslice []byte)bool
```
> 判断切片 b 是否包含子切片 subslice。

## func Count
```go
func Count(s, sep []byte)int
```
> Count 计算 s 中有多少个不重叠的 sep 子切片。

## func Index
```go
func Index(s, sep []byte)int
```
> 子切片 sep 在 s 中第一次出现的位置，不存在则返回 -1。

## func IndexByte
```go
func IndexByte(s []byte, c byte)int
```
> 字符 c 在 s 中第一次出现的位置，不存在则返回-1。

## func IndexRune
```go
func IndexRune(s []byte, r rune)int
```
> unicode 字符 r 的 utf-8 编码在 s 中第一次出现的位置，不存在返回-1。

## func IndexAny(s []byte, chars string)int
```go
func IndexAny(s []byte, chars string)int
```
> 字符串 chars 中的任一 utf-8 编码在 s 中第一次出现的位置，如果不存在或者 chars 为空字符串则返回-1。

## func IndexFunc
```go
func IndexFunc(s []byte, f func(r rune)bool)int
```
> s 中第一个满足函数 f 的位置(该处的 utf-8 码值 r 满足 f(r)==true),不存在则返回-1。

## func LastIndex
```go
func LastIndex(s, sep []byte)int
```
> 切片 sep 在切片 s 中最后一次出现的位置，不存在则返回-1。

## func LastIndexAny
```go
func LastIndexAny(s []byte,chars string)int
```
> 字符串 chars 中的任一 utf-8 字符在 s 中最后一次出现的位置，如果不存在或者 chars 为空字符串则返回-1。

## func LastIndexFunc
```go
func LastIndexFunc(s []byte, f func(r rune)bool)int
```
> s 中最后一个满足函数 f 的 unicode 码值得为之，不存在则返回-1。

## func Title
```go
func Title(s []byte)[]byte
```
> 返回 s 中每个单词的首字母都改为标题格式的拷贝。 Title 用于划分单词的规则不能很好的处理 Unicode 标点符号。

## fun ToLower
```go
func ToLower(s []byte)[]byte
```
> 返回所有字幕都变成小写版本的拷贝。

## func ToLowerSpecial
```go
func ToLowerSpecial(_case unicode.specialCase, s []byte)[]byte
```
> 使用 _case 规则的字符映射，将所有字幕都转为对应的小写版本的拷贝。

## func ToUpper
```go
func ToUpper(s []byte)[]byte
```
> 返回所有字幕对应的大写版 

## func ToUpperSpecial
```go
func ToUpperSpecial(_casse unicode.SpecialCase, s []byte)[]byte
``` 
> 使用 _case 规定的字符映射，返回字母对应的标题版本的拷贝。

## func Repeat
```go
func Repeat(b []byte, count int)[]byte
```
> 返回 count 个 b 穿梁形成的新切片。

## func Replace
```go
func Replace(s, old, new []byte, nint)[]byte
```
> 返回将 s 中签 n 个不重叠 old 切片替换成 new 切片的拷贝，如果 n<0 会替换所有 old 子切片。

## func Map
```go
func Map(mapping func(r rune)rune, s []byte)[]byte
```
> 将 s 的每一个 unicode 码值 r 都替换为 mapping(r),返回这些新码值组成的切片拷贝。如果 mapping 返回一个负值，将会丢弃该码值而不会被替换(返回中对应未知将没有码值)。

## func Trim
```go
func Trim(s []byte, cutset string)[]byte
```
> 返回将 s 前后端有所 cutset 包含的 unicode 码值都去掉的子切片。(公用底层数组)

## func TrimSpace
```go
func TrimSpace(s []byte)[]byte
```
> 返回将 s 前后端所有空白(unicaode.IsSpace指定)都去掉的自切片。(公用底层数组)

## func Trimfunc
```go
func TrimFunc(s []byte, f func(r rune)bool)[]byte
```
> 返回将 s 前后端所有满足 f 的 unicode 码值都去掉的自切片。(公用底层数组)

## func TrimLeft
```go
func TrimLeft(s []byte, cutset string)[]byte
```
> 返回将 s 前端所有 cutset 包含的 unicode 码值都去掉的自切片。(公用底层数组)

## func TrimLeftFunc
```go
func TrimLeftFunc(s []byte, f func(r rune)bool)[]byte
```
> 返回将 s 前端所有满足 f 的 unicode 码值都去掉的自切片。(公用底层数组)

## func TrimPrefix
```go
func TrimPrefix(s, prefix []byte)[]byte
```
> 返回去掉 s 可能的前缀 prefix 的自切片。(公用底层数组)

## func TrimRight
```go
func TrinRight(s []byte, cutset string)[]byte
```
> 返回将 s 后端所有 cutset 包含的 unicode 码值都去掉的子切片。（共用底层数组）

## func TrimRightFunc
```go
func TrimRightFunc(s []byte, f func(r rune)bool)[]byte
```
> 返回将s后端所有满足f的unicode码值都去掉的子切片。（共用底层数组）

## func TrimSuffix
```go
func TrimSuffix(s, suffix []byte)[]byte
```
> 返回去除s可能的后缀suffix的子切片。（共用底层数组）

## func Fields
```go
func Fields(s []byte)[][]byte
```
> 返回将字符串按照空白（unicode.IsSpace确定，可以是一到多个连续的空白字符）分割的多个子切片。如果字符串全部是空白或者是空字符串的话，会返回空切片。

## func Split
```go
func Split(s, sep []byte)[][]byte
```
> 用去掉s中出现的sep的方式进行分割，会分割到结尾，并返回生成的所有[]byte切片组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个[]byte切片。

## func SplitN
```go
func SplitN(s, sep []byte, n int)[][]byte
```
> 用去掉s中出现的sep的方式进行分割，会分割到最多n个子切片，并返回生成的所有[]byte切片组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个[]byte切片。参数n决定返回的切片的数目：
>
> n > 0 : 返回的切片最多n个子字符串；最后一个子字符串包含未进行切割的部分。n == 0: 返回nil。n < 0 : 返回所有的子字符串组成的切片

## func SplitAfer
```go
func SplitAfter(s, sep []byte) [][]byte
```
> 用从s中出现的sep后面切断的方式进行分割，会分割到结尾，并返回生成的所有[]byte切片组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个[]byte切片。

## func SplitAferN
```go
func SplitAfterN(s, sep []byte, n int) [][]byte
```

## func Join
```go
func Join(s [][]byte, sep []byte) []byte
```
> 将一系列[]byte切片连接为一个[]byte切片，之间用sep来分隔，返回生成的新切片。

## type Reader
```go
type Reader struct{
	//
}
```
> Reader类型通过从一个[]byte读取数据，实现了io.Reader、io.Seeker、io.ReaderAt、io.WriterTo、io.ByteScanner、io.RuneScanner接口。

## func NewReader
```go
func NewReader(b []byte)*Reader
```
> NewReader创建一个从 b []byte 读取数据的Reader。

## func (*Reader)Len 
```go
func (r *Reader)Len()int
```
> Len 返回 r 包含的切片中还没有被读取的部分。

## func (*Reader)Read
```go
func (r *Reader)Read(b []byte)(n int, err error)
```

## func (*Reader)ReadByte
```go
func (r *Reader)ReadByte()(b byte, err error)
```

## func (*Reader)UnreadByte
```go
func (r *Reader)UnreadByte()error
```

## func (*Reader)ReadRune
```go
func (r *Reader)ReadRune(ch rune, size int, err error)
```

## func (*Reader)UnreadRune
```go
func (r *Reader)UnreadRune()error
```

## func (*Reader)Seek
```go
func (r *Reader)Seek(offset int64, whence int)(int64, error)
```
> Seek 实现了 io.Seeker 接口。

## func (*Reader)ReadAt
```go
func (r *Reader)ReadAt(b []byte, off int64)(n int, err error)
```

## func (*Reader)WriteTo
```go
func (r *Reader)WriteTo(w io.Writer)(n int64, err error)
```
> WriteTo 实现了 io.WriterTo 接口。


## type Buffer
```go
type Buffer struct{
	//
}
```
> Buffer 是一个实现了读写方法的可变大小的字节缓冲。本类型的零值是一个空的可用于读写的缓冲。