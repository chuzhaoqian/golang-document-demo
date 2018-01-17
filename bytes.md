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

