# package bufio
```go
import "bufio"
```
> bufio 包实现了有缓冲的 I/O。 它包装一个 io.Reader 或 io.Writer 接口对象，创建另一个也实现了该接口，且同时还提供了缓冲和一些文本 I/O 的帮助函数的对象。

```go
const(
	MaxScanTokenSize = 64 * 1024
)

var (
	ErrInvalidUnreadByte = errors.New("bufio: invalid use of UnreadByte")
	ErrInvalidUnreadRune = errors.New("bufio: invalid use of UnreadRune")
	ErrBufferFull = errors.New("bufio: buffer full")
	ErrNegativaCount = errors.New("bufio: negative count")
)

var (
	ErrTooLong = errors.New("bufio.Scanner: token too long")
	ErrNegativeAdvance = errors.New("bufio.ScannerFunc: SplitFunc returns negative advance count")
	ErrAdvaceTooFar = errors.New("bufio.Scanner: SplitFunc returns advace count beyond input")
)
```

## type Reader
```go
type Reader struct{
	//
}
```
> Reader实现了给一个 io.Reader 接口对象附加缓冲。

## func NewReader
```go
func NewReader(rd io.Reader)*Reader
```
> NewReader 创建一个具有默认大小缓冲、从 rd io.Reader 读取的 *Reader。

## func NewReaderSize
```go
func NewReaderSize(rd io.Reader, size int)*Reader
```
> NewReaderSize 创建一个具有最小有 size 尺寸的缓冲、从 rd io.Reader 读取的 *Reader。如果参数 rd io.Reader 已经是一个具有足够大缓冲的 *Reader 类型值，会返回 rd io.RReader。

## func (*reader)Reset
```go
func (br *Reader)Reset(r io.Reader)
```
> Reaset 丢弃缓冲中的数据，清除任何错误，将 br *Reader 重设为其下层从 r io.Reader 读取数据。

## func (*Reader)Buffered
```go
func (br *Reader)Buffered()int
```
> Buffered 返回缓冲中现有的可读取的字节数。

## func (*Reader)Peek
```go
func (br *Reader)Peek(n int)([]byte, err error)
```
> Peek 返回输入流下 n 个字节，而不会移动读取位置。返回的 []byte 只在下一次调用读取操作前合法。如果 Peek 返回的切片长度比 n 小，他也会返回一个错误活命原因。如果 n 比缓冲尺寸还大，返回的错误将是 ErrBufferFull。

## func (*Reader)Read
```go
func (br *Reader)Read(p []byte)(n int, err error)
```
> Read 读取数据写入 p []byte。本方法返回写入 p 的字节数。本方法一次调用最多会调用下层 Reader 接口一次 Read 方法，因此返回值 n 可能小于 len(p)。读取到达结尾时，返回 n 将为0而 err 将为 io.EOF。

## func (*Reader)ReadByte
```go
func (br *Reader)ReadByte()(c byte, wrr wrror)
```
> ReadByte 读取并返回一个字节。如果没有可用的数据，会返回错误。

## func (*Reader)UnreadByte
```go
func (br *Reader)UnreadByte()error
```
> UnreadByte 吐出最近一次读取操作读取的最后一个字节。(只能吐出最后一个，多次调用会出问题)

## func (*Reader)ReadRune
```go
func (br *Reader)ReadRune()(r rune, size int, err error)
```
> ReadRune 读取一个 utf-8 编码的 unicode 码值，返回该码值、其编码长度和可能的错误。如果 utf-8 编码非法，读取位置只移动1字节，返回 U+FFFD,返回值 size 为1而 err 为 nil。如果没有可用的数据，会返回错误。

## func (*Reader)UnreadRune
```go
func (br *Reader)UnreadRune()error
```
> UnreadRune 吐出最后一次 ReadRune 调用读取的 unicode 码值。如果最近一次读取不是调用的 ReadRune,会返回错误。(从这点看，UnreadRune 比 UnreadByte 严格很多)

## func (*Reader)ReadLine
```go
func (br *Reader)ReadLine()(line []byre, isPresfix bool, err error)
```
> ReadLine 是一个低水平的行数据读取原语。大多数调用者使用 ReadBytes('\n') 或 ReadString('\n') 代替，或者使用Scanner。
>
> ReadLine 尝试返回一行数据，不包括行尾标志的字节。如果行太长超过了缓冲，返回值 isPrefix 会被设置为 true, 并返回行的前面一部分。该行剩下的部分将在之后的调用中返回。返回值 isPresfix 会在返回该行最后一个片段时才设为 false。 返回切片是缓冲的子切片，只在下一次读取操作之前有效。ReadLine 要么返回一个非 nil 的 line, 要么返回一个非 nil 的 err, 两个返回值至少一个非 nil。
>
> 返回的文本不保航行尾的标志字节 (“\r\n” 或 "\n")。如果输入流结束时没有行尾标志字节，方法不会出错，也不会指出这一情况。在调用 ReadLine 之后调用 UnreadByte 会总是吐出最后一个读取的字节 (很可能是改行的结尾标志字节)，即使该字节不是 ReadLine 返回值的一部分。

## func (*Reader) ReadSlice
```go
func (br *Reader) ReadSlice(delim byte) (line []byte, err error)
```
> ReadSlice读取直到第一次遇到delim字节，返回缓冲里的包含已读取的数据和delim字节的切片。该返回值只在下一次读取操作之前合法。如果ReadSlice放在在读取到delim之前遇到了错误，它会返回在错误之前读取的数据在缓冲中的切片以及该错误（一般是io.EOF）。如果在读取到delim之前缓冲就被写满了，ReadSlice失败并返回ErrBufferFull。因为ReadSlice的返回值会被下一次I/O操作重写，调用者应尽量使用ReadBytes或ReadString替代本法功法。当且仅当ReadBytes方法返回的切片不以delim结尾时，会返回一个非nil的错误。

## func (*Reader) ReadBytes
```go
func (br *Reader) ReadBytes(delim byte) (line []byte, err error)
```
> ReadBytes读取直到第一次遇到delim字节，返回一个包含已读取的数据和delim字节的切片。如果ReadBytes方法在读取到delim之前遇到了错误，它会返回在错误之前读取的数据以及该错误（一般是io.EOF）。当且仅当ReadBytes方法返回的切片不以delim结尾时，会返回一个非nil的错误。

## func (*Reader) ReadString
```go
func (br *Reader) ReadString(delim byte) (line string, err error)
```
> ReadString读取直到第一次遇到delim字节，返回一个包含已读取的数据和delim字节的字符串。如果ReadString方法在读取到delim之前遇到了错误，它会返回在错误之前读取的数据以及该错误（一般是io.EOF）。当且仅当ReadString方法返回的切片不以delim结尾时，会返回一个非nil的错误。

## func (*Reader)WriteTo
```go
func (b *Reader)WriteTo(w io.Writer)(n int64, err error)
```
> WriteTo 方法实现了 io.WriterTo 接口。

## type Writer
```go
type Writer struct{
	//
}
```
> Writer 实现了 io.Writer 接口对象提供的缓冲。如果在向一个 Writer 类型值写入是遇到错误，该对象将不再接受任何数据，且所有写操作都会返回该错误。在说所有数据都写入后，调用者有义务调用 Flush 方法保证所有的数据都交给了下层的 io.Writer。

## NewWriter
```go
func NewWriter(w io.Writer)*Writer
```
> NewWriter 创建一个具有默认大小缓冲、写入 w io.Writer 的 *Writer。

## func NewWriterSize
```go
func NewWriterSize(w io.Writer, size int)*Writer
```
> NewWriterSize 创建一个具有最少有 size 尺寸的缓冲、写入 w io.Writer 的 *Writer。如果参数 w io.Writer 已经是一个具有足够大缓冲的 *Writer 类型值，会返回 w io.Writer。

## func (*Writer)Reset
```go
func (b *Writer)Reset(w io.Writer)
```
> Rest 丢弃缓冲中的数据，清除任何错误，将 b *Writer 重设为将其输出写入 w io.Writer。

## func (*Writer)Buffered
```go
func (b *Writer)Buffered()int
```
> Buffered 返回缓冲中已使用的字节数。

## func (b *Writer)Available
```go
func (b *Writer)Available()int
```
> Available 返回缓冲中海油多少字节未使用。

## func (*Writer)Write
```go
func (b *Writer)Write(p []byte)(n int, err error)
```
> Write 将 p []byte 的内容写入缓冲。返回写入的字节数。如果返回值 n < len(p)，会返回一个错误。

## func (*Writer)WriteString
```go
func (b *Writer)WriteString(s String)(int, error)
```
> WriteString 写入一个字符创。返回写入的字节数。如果返回 n < len(s),还会返回一个错误。

## func (*Writer)WriteByte
```go
func (b *Writer)WriteByte(c byte) error
```
> WriteByte 写入单个字节。

## func (*Writer)WriteRune(r rune)(size int,err error)
```go
func (b *Writer)WriteRune(r rune)(size int, err error)
```
> WriteRune 写入一个 unicode 码值(utf-8 编码)，返回写入的字节数和可能的错误。

## func (*Writer)Flush
```go
func (b *Writer)Flush()error
```
> Flush 方法将缓冲中的数据写入下层的 io.Writer 接口。

## func (*Writer)ReadFrom
```go
func (b *Writer)ReadFrom(r io.Reader)(n int64, err error)
```
> ReadFrom 实现了 io.ReaderFrom 接口。

## type ReadWriter
```go
type ReadWriter struct{
	*Reader
	*Writer
}
```
> ReaderWriter 类型保管了 指向 Reader 和 Writer 类型的指针， 因此实现了 io.ReadWriter 接口。