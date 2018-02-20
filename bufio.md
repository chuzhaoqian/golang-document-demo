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
>
> 相当于 NewReaderSize(rd io.Reader, 4096)

## func NewReaderSize
```go
func NewReaderSize(rd io.Reader, size int)*Reader
```
> NewReaderSize 创建一个具有最小有 size 尺寸的缓冲、从 rd io.Reader 读取的 *Reader。如果参数 rd io.Reader 已经是一个具有足够大缓冲的 *Reader 类型值，会返回 rd io.Reader。

### Example
```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	s := strings.NewReader("")
	fmt.Println("strings NewReader:", s)
	br := bufio.NewReader(s)
	fmt.Println("bufio NewReader:", br)
}
```

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

### Example
```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	s := strings.NewReader("123456")
	fmt.Println("strings NewReader:", s)
	br := bufio.NewReader(s)
	fmt.Println("bufio NewReader:", br)

	b, _ := br.Peek(5)
	fmt.Println("Peek:", b)

	bRead := make([]byte, 20)
	fmt.Println("初始一个空的 byte 切片：", bRead)
	n, _ := br.Read(bRead)
	fmt.Printf("读取后：%s\n", bRead)
	fmt.Println("读取", n, "个")
}
```

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

### Example
```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	s := strings.NewReader("123456")
	br := bufio.NewReader(s)
	fmt.Println("bufio NewReader:", br)

	c, _ := br.ReadByte()
	fmt.Println(c)

	c2, _ := br.ReadByte()
	fmt.Println(c2)

	br.UnreadByte()

	c3, _ := br.ReadByte()
	fmt.Println(c3)

	//	多次调用没看出有什么错误
	//	br.UnreadByte()
	//	br.UnreadByte()
}
```

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

### Example
```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"strings"
)

func main() {
	s := strings.NewReader("123456")
	br := bufio.NewReader(s)
	b := bytes.NewBuffer(make([]byte, 0))

	br.WriteTo(b)
	fmt.Printf("%s\n", b)
}
```


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

### Example
```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
)

func main() {
	b := bytes.NewBuffer(make([]byte, 0))

	bw := bufio.NewWriter(b)
	fmt.Println(bw.Available()) // 剩余缓存 4096
	fmt.Println(bw.Buffered())  // 使用了多少个缓存

	bw.Write([]byte("1"))
	fmt.Println(bw.Available()) // 剩余缓存 4095
	fmt.Println(bw.Buffered())  // 1

	fmt.Println("Flush 前", b) // ""
	bw.Flush()
	fmt.Println("Flush 后", b) // 这时候 Available Buffered重置

	fmt.Println(bw.Available()) // 剩余缓存 4096
	fmt.Println(bw.Buffered())  // 使用了多少个缓存
	bw.Write([]byte("2"))       // 可以继续使用
	bw.WriteString("3")         // 可以继续使用
	bw.WriteByte('4')           // byte 单引号
	bw.Flush()
	fmt.Println("Flush 后", b) // Flush 后 123

	bws := bufio.NewWriterSize(b, 6)
	fmt.Println(bws.Available()) // 6
	fmt.Println(bws.Buffered())  // 0
}
```

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

### Example
```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"strings"
)

func main() {
	b := bytes.NewBuffer(make([]byte, 0))
	s := strings.NewReader("go 语言")

	bw := bufio.NewWriter(b)
	bw.ReadFrom(s) // 无需调用 Flush

	fmt.Println(b)
}
```

## type ReadWriter
```go
type ReadWriter struct{
	*Reader
	*Writer
}
```
> ReaderWriter 类型保管了 指向 Reader 和 Writer 类型的指针， 因此实现了 io.ReadWriter 接口。

## func NewReadWriter
```go
func NewReadWriter(r *Reader, w *Writer)*ReadWriter
```
> NewReadWriter 创建一个新的、将读写操作分派个 r 和 w 的 ReadeWriter。

### Example
```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"strings"
)

func main() {
	b := bytes.NewBuffer(make([]byte, 0))
	bw := bufio.NewWriter(b)

	s := strings.NewReader("123")
	br := bufio.NewReader(s)

	rw := bufio.NewReadWriter(br, bw)

	p, _ := rw.ReadString('\n')
	fmt.Println(string(p))

	rw.WriteString("asdf")
	rw.Flush()
	fmt.Println(b)
}
```

## type SplitFunc
```go
type SplitFunc func(data []byte, atEOF bool)(advance int, token []byte, err error)
```
> SplitFunc 类型代表用于对输出做分词分析的分割函数。
>
> 参数 data 是尚未处理的数据的一个开始部分的切片， 参数 atEOF 表示是否 Reader 接口不能提供更多的数据。返回值是分析未知前进的字节数，将要返回给调用者的 token 切片，以及可能遇到的错误。如果数据不足以(保证)生成一个完整的 token，例如需要一整行数据但 data 里没有换行符，SplitFunc 可以返回(0, nil, nil)来告诉 Scanner 读取更多的数据写入切片然后用从不同一起始、长度更长的切片再试一次(调用 SplitFunc 类型函数)。
>
> 如果返回值 err 非 nil,扫描将终止并将该错误并返回给 Scanner 的调用者。
>
> 除非 atEOF 为真，永远不会使用空切片 data 调用 SplitFunc 类型行数。如果 atEOF 为真，data 却可能是非空的、且包含未处理的文本。


## func ScanBytes
```go
func ScanBytes(data []byte, atEOF bool)(advance int, token []byte, err error)
```
> ScanBytes 是用于 canner 类型的分割函数(符合 SplitFunc),会将每个字节作为一个 token 返回。

## func ScanRunes
```go
func ScanRunes(data []byte, atEOF bool)(advance int, token []byte, err error)
```
> ScanRunes 是用于 Scanner 类型的分割函数(符合 SplitFunc),会将每个 utf-8 编码的 unicode 作为一个 token 返回。返回的 rune 序列和 range 一个字符串的输出 rune 序列相同。错误的 utf-8 编码会翻译为 U+FFFD = "\xef\xbf\xbd",但只会消耗一个字节。调用者无法区分正确编码的 rune 和错误的编码 rune。

## func ScanWords
```go
func ScanWords(data []byte, atEOF bool)(advance int, token []byte, err error)
```
> ScanWords 适用于 Scanner 类型的分割函数(符合 SplitFunc),本函数会将空白(unicode.IsSpace)分割的片段(去掉前后空白后)作为一个 tokern 返回。本函数永远不会返回空字符串。

## func ScanLines
```go
func ScanLines(data []byte, atEOF bool)(advance int, token []byte, err error)
```
> ScanLine 是用于 Scanner 类型的分割函数(符合 SplitFunc),会将每一行文本去掉末尾的换行标记作为一个 token 返回。返回的行可以是空字符串。换行标记为一个可选的回车后跟一个必选的换行符。最后一行即使没有换行符也会作为一个 token 返回。

## type Scanner
```go
type Scanner struct {
	//
}
```
> Scanner 类型提供了方便的读取数据的接口，如从换行分割的文本里读取每一行。
>
> 成功调用的 Scan 方法会逐步提供文件的 token,跳过 token 之间的字节。token 由 SplitFunc 类型的分割函数指定；默认的分割函数会将输入分割为多个行，并且去掉行尾的汉航标识。可以定制自己的分割函数。
>
> 扫描会在抵达输入流结尾、遇到的第一个I/O错误、token过大不能保存进缓冲时，不可恢复的停止。当扫描停止后，当前读取位置可能会远在最后一个获得的token后面。需要更多对错误管理的控制或token很大，或必须从reader连续扫描的程序，应使用bufio.Reader代替

## func NewScanner
```go
func NewScanner(r io.Reader)*Scanner
```
> NewScanner 创建并返回一个从 r io.Reader 读取数据的Scanner,默认的分割函数是 ScanLines。

## func (*Scanner)Split
```go
func (s *Scanner)Split(split SplitFunc)
```
> Split 设置该 Scanner 的分割函数。本方法必须在 Scan 之前调用。

## func (*Scanner)Scan
```go
func (s *Scanner)Scan()bool
```
> Scan 方法获取当前位置的 token (该 token 可以通过 Bytes 或 Text 方法获得)，并让 Scanner 的扫描未知移动到下一个 token。当扫描因为抵达输入流结尾或遇到错误而停止时，本方法会返回 false。在 Scan 方法返回 false 后，Err 方法将会返回扫描时遇到的任何错误；除非是 io.EOF，此时 Err 会返回 nil。

## func (*Scanner)Bytes
```go
func (s *Scanner)Bytes()[]byte
```
> Bytes 方法返回最后一次 Scan 调用生成的 token。底层数组指向的数据可能会被下一次 Scan 的调用重写。

## func (*Scanner)Text
```go
func (s *Scanner)Text()string
```
> Text 返回最后一次 Scan 调用生成的 token,会申请创建一个字符串保存 token 并返回该字符串。

## func (*Scanner)Err
```go
func (s *Scanner)Err()error
```
> Err 返回 Scanner 遇到的第一个非 EOF 的错误。
