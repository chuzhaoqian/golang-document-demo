# package io
```go
import "io"
```
> io包提供了对I/O原语的基本接口。本包的基本任务是包装这些原语已有的实现（如os包里的原语），使之成为共享的公共接口，这些公共接口抽象出了泛用的函数并附加了一些相关的原语的操作。
>
> 因为这些接口和原语是对底层实现完全不同的低水平操作的包装，除非得到其它方面的通知，客户端不应假设它们是并发执行安全的。
>> 在 io 包中最重要的是两个接口：Reader 和 Writer 接口。本章所提到的各种 IO 包，都跟这两个接口有关，也就是说，只要实现了这两个接口，它就有了 IO 的功能

```go
var EOF = errors.New("EOF")
```
> EOF 当无法得到更多输入时， Read 方法返回 EOF。当函数一切正常的到达输入的结束时，就会返回 EOF 。如果在一个结构化数据流中 EOF 在不期望的位置出现了，则应返回错误 ErrUnexpectedEof 或其它给出更多细节的错误。

```go
var ErrClosedPipe = errors.New("io: read/write on closed pipe")
```
> 当从一个已关闭的 Pipe 读取或者写入时，会返回 ErrClosedPipe。

```go
var ErrNoProgress = errors.New("nultiple Read calls return no data or error")
```
> 某些使用 io.Reader 接口的客户端如果多次调用 Read 都不返回数据也不返回错误时，就会赶回本错误，一般来说是 io.Reader 的实现有问题的标识。

```go
var ErrShortBuffer = errors.New("short buffer")
```
> ErrShortBuffer 表示读取操作需要大缓冲，但提供的缓冲不够大。

```go
var ErrShortWrite = errors.New("short write")
```
> ErrShortWrite 表示写入操作写入的数据比提供的烧，却没有显示的返回错误。

```go
var ErrUnexpectedEOF = errors.New(unexpected EOF)
```
> ErrUnexpectedEOF 表示在读取一个固定尺寸的块或数据结构时，在读取未完全时遇到了 EOF。

## type Reader
```go
type Reader interface {
	Red(p []byte)(n int, err error)
}
```
> Reader 接口用于包装基本的读取方法。
>
> Read 方法读取 len(p) 字节数据写入 p。它返回写入的字节数和遇到的任何错误。即使 Read 方法返回值 n > len(p),本方法在被调用时仍可能使用 p 的全部长度作为暂存空间。如果有部分可用数据，但不够 len(p) 字节，Read 按照惯例会返回可以读取到的数据，而不是等待更多数据。
>
> 当 Read 在读取 n > 0个字节后遭遇错误或到达文件结尾时，会返回读取的字节数。可能会在该次调用返回一个非 nil 的错误，或者在下一次调用时返回 0 和该错误。一个常见的例子， Reader 接口会在输入流的结尾返回非 o 的字节数，返回值 err == EOF 或 err == nil。 这么做可以正确的处理发生在读取部分数据后的 I/O错误，也能正确处理 EOF 事件。
>
> 如果 Read 的某个实现返回 0 字节数，表示被阻碍；调用者应该将这种情况视为未进行操作。
>> 当 Read 方法返回错误时，不代表没有读取到任何数据。调用者应该处理返回的任何数据，之后才处理可能的错误

## type Writer
```go
type Writer interface {
	Write(p []byte)(n int, err error)
}
```
> Writer 接口用于包装基本的写入方法。
>
> Write 方法 len(p) 字节数据从 p 写入底层的数据流。它会返回写入的字节数 (0 <= n <= len(p))和遇到的任何导致写错结束的错误。 Write 必须返回非 nil 的错误，如果返回的 n < len(p)。 Write 不能修改切片 p 中的数据，即使临时修改也不行。

## type Closer
```go
type Closer interface {
	Close() error
}
```
> Closer 接口用于包装基本的关闭方法。在第一次调用之后再次被调用时，Close 方法的行为是未定义的。

## type Seeker
```go
type Seeker interface {
	Seek(offset int64, whence int)(int64, error)
}
```
> Seeker 接口用于包装基本的移位方法。
>
> Seek 方法设定下一次读写的位置：偏移量为 offset,校准点由 whence 确定：0表示相对于文件起始；1表示相对于当前位置；2表示相对于文件结尾。Seek 方法返回新的位置以及可能遇到的错误。移动到一个绝对偏移量为负数的位置会导致错误。移动到任何偏移量为正数的位置都是合法的，但其下一次 I/O 操作的具体行为则要看底层的实现。

## type ReadCloser
```go
type ReadCloser interface {
	Reader
	Closer
}
```
> ReadCloser 接口聚合了基本的读取和关闭操作。

## type ReadSeeker
```go
type ReadSeeker interface{
	Reader
	Seeker
}
```
> ReadSeeker 接口聚合了基本的读取和移位操作。

## type WriteCloser
```go
type WriteCloser interface {
	Writer
	Closer
}
```
> WriteCloser 接口聚合了基本的写入和关闭操作。

## type WriteSeeker
```go
type WriteSeeker interface {
	Writer
	Seeker
}
```
> WriteSeeker 接口聚合了基本的写入和移位操作。

## type ReadWriter
```go
type ReadWriter interface {
	Reader
	Writer
}
```
> ReadWriter 接口聚合了基本的读写操作。

## type ReadWriteCloser
```go
type ReadWriteCloser interface {
	Reader
	Writer
	Closer
}
```
> ReadWriteCloser 接口聚合了基本的读写和关闭操作。

## type ReadWriteSeeker
```go
type ReadWriteSeeker interface {
	Reader
	Writer
	Seeker
}
```
> ReadWriteSeeker 接口聚合了基本的读写和移位操作。

## type ReaderAt
```go
type ReaderAt interface {
	ReadAt(p []byte, off int64)(n int, err error)
}
```
> ReaderAt 接口包装了基本的 ReadAt 方法。
>
> ReadAt从底层输入流的偏移量off位置读取len(p)字节数据写入p， 它返回读取的字节数(0 <= n <= len(p))和遇到的任何错误。当ReadAt方法返回值n < len(p)时，它会返回一个非nil的错误来说明为啥没有读取更多的字节。在这方面，ReadAt是比Read要严格的。即使ReadAt方法返回值 n < len(p)，它在被调用时仍可能使用p的全部长度作为暂存空间。如果有部分可用数据，但不够len(p)字节，ReadAt会阻塞直到获取len(p)个字节数据或者遇到错误。在这方面，ReadAt和Read是不同的。如果ReadAt返回时到达输入流的结尾，而返回值n == len(p)，其返回值err既可以是EOF也可以是nil。如果ReadAt是从某个有偏移量的底层输入流（的Reader包装）读取，ReadAt方法既不应影响底层的偏移量，也不应被底层的偏移量影响。
>
> ReadAt方法的调用者可以对同一输入流执行并行的ReadAt调用。

## type WriterAt
```go
type WriterAt interface {
	WriteAt(p []byte, off int64)(n int, err error)
}
```
> WriterAt接口包装了基本的WriteAt方法。
>
>WriteAt将p全部len(p)字节数据写入底层数据流的偏移量off位置。它返回写入的字节数(0 <= n <= len(p))和遇到的任何导致写入提前中止的错误。当其返回值n < len(p)时，WriteAt必须放哪会一个非nil的错误。如果WriteAt写入的对象是某个有偏移量的底层输出流（的Writer包装），WriteAt方法既不应影响底层的偏移量，也不应被底层的偏移量影响。
>
> ReadAt方法的调用者可以对同一输入流执行并行的WriteAt调用。（前提是写入范围不重叠）

## type ByteReader
```go
type ByteReader interface {
	ReadByte()(c byte, err error)
}
```
> ByteReader 是基本的ReadByte 方法的包装。ReadByte 读取输入中的单个字节并返回。如果没有字节可读取，会返回错误。

## type ByteScanner
```go
type ByteScanner interface {
	ByteReader
	UnreadByte()error
}
```
> ByteScanner 接口在基本的 ReadByte 方法之外还添加了 UnreadByte 方法。UnreadByte 方法让下一次调用 ReadByte 时返回之前调用 ReadByte 时返回的同一个字节。连续调用两次 UnreadByte 方法而中间没有调用 ReadByte 时，可能会导致错误。

## type RuneReader
```go
type RuneReader interface {
	ReadRune()(r rune, size int, err error)
}
```
> RuneReader是基本的ReadRune方法的包装。ReadRune读取单个utf-8编码的字符，返回该字符和它的字节长度。如果没有有效的字符，会返回错误。

## type RuneScanner
```go
type RuneScanner interface {
	RuneReader
	UnreadRune()error
}
```
> RuneScanner接口在基本的ReadRune方法之外还添加了UnreadRune方法。UnreadRune方法让下一次调用ReadRune时返回之前调用ReadRune时返回的同一个utf-8字符。连续调用两次UnreadRune方法而中间没有调用ReadRune时，可能会导致错误。

## type ByteWriter
```go
type ByteWriter interface {
	WriteByte(c byte)error
}
```
> ByteWriter 是基本的 WriteByte 方法的包装。

## type ReaderFrom
```go
type ReaderFrom interface {
	ReadFrom(r Reader)(n int64, err error)
}
```
> ReaderFrom接口包装了基本的ReadFrom方法。ReadFrom方法从r读取数据直到EOF或者遇到错误。返回值n是读取的字节数，执行时遇到的错误（EOF除外）也会被返回。

## type WriterTo
```go
type WriterTo interface {
	WriteTo(w Writer)(n int64, err error)
}
```
> WriterTo接口包装了基本的WriteTo方法。WriteTo方法将数据写入w直到没有数据可以写入或者遇到错误。返回值n是写入的字节数，执行时遇到的任何错误也会被返回。 

## type LimitedReader
```go
type LimitedReader struct {
	R Reader
	N int64 // 剩余可读取字节数
}
```
> LimitedReader从R中读取数据，但限制可以读取的数据的量为最多N字节，每次调用Read方法都会更新N以标记剩余可以读取的字节数。

## func LimitReader
```go
func LimitReader(r Reader, n int64)Reader
```
> 返回一个 Reader，它从r中读取n个字节后以 EOF 停止。返回值接口的底层为 *LimitedReader 类型。

## func (*LimitedReader)Read
```go
func (l *LimitedReader)Read(p []byte)(n int, err error)
```

## type SectionReader
```go
type SectionReader struct {
	//
}
```
> SectionReader 实现了对底层满足 ReadAt 接口的输入流某个片段的 Read/ReadAt/Seek 方法。

## func NewSectionReader
```go
func NewSectionReader(r ReaderAt, off int64, n int64)*SectionReader
```
> 返回一个从 r 中的偏移量 off 处为起始，读取 n 个字节后以 EOF 停止的 SectionReader。

## func (*SectionReader)Size
```go
func (s *SectionReader)Size()int64
```
> Size返回该片段的字节数。

## func (*SectionReader)Read
```go
func (s *SectionReader) Read(p []byte) (n int, err error)
```

## func (*SectionReader) ReadAt
```go
func (s *SectionReader) ReadAt(p []byte, off int64) (n int, err error)
```

## func (*SectionReader) Seek
```go
func (s *SectionReader) Seek(offset int64, whence int) (int64, error)
```

## type PipeReader
```go
type PipeReader struct {
	//
}
```
> PipeReader 是一个管道的读取端。

## func Pipe
```go
func Pipe()(*PipeReader, *PipeWriter)
```
> Pipe创建一个同步的内存中的管道。它可以用于连接期望io.Reader的代码和期望io.Writer的代码。一端的读取对应另一端的写入，直接在两端拷贝数据，没有内部缓冲。可以安全的并行调用Read和Write或者Read/Write与Close方法。Close方法会在最后一次阻塞中的I/O操作结束后完成。并行调用Read或并行调用Write也是安全的：每一个独立的调用会依次进行。

## func (*PipeReader)Read
```go
func (r *PipeReader) Read(data []byte) (n int, err error)
```
> Read 实现了标准 Reader 接口：它从管道中读取数据，会阻塞直到写入端开始写入或写入端被关闭。

## func (*PipeReader) Close
```go
func (r *PipeReader) Close() error
```
> Close 关闭读取器；关闭后如果对管道的写入端进行写入操作，就会返回(0, ErrClosedPip)。

## func (*PipeReader) CloseWithError
```go
func (r *PipeReader) CloseWithError(err error) error
```
> CloseWithError类似Close方法，但将调用Write时返回的错误改为err。

## type PipeWriter
```go
type PipeWriter struct {
	//
}
```
> PipeWriter是一个管道的写入端。

## func (*PipeWriter)Write
```go
func (w *PipeWriter) Write(data []byte) (n int, err error)
```
> Write实现了标准Writer接口：它将数据写入到管道中，会阻塞直到读取器读完所有的数据或读取端被关闭。

## func (*PipeWriter)Close
```go
func (w *PipeWriter) Close() error
```
> Close关闭写入器；关闭后如果对管道的读取端进行读取操作，就会返回(0, EOF)。

## func (*PipeWriter) CloseWithError
```go
func (w *PipeWriter) CloseWithError(err error) error
```
> CloseWithError类似Close方法，但将调用Read时返回的错误改为err。

## func TeeReader
```go
func TeeReader(r Reader, w Writer) Reader
```
> TeeReader 返回一个将其从r读取的数据写入 w 的 Reader 接口。所有通过该接口对r的读取都会执行对应的对 w 的写入。没有内部的缓冲：写入必须在读取完成前完成。写入时遇到的任何错误都会作为读取错误返回。

## func MultiReader
```go
func MultiReader(readers ...Reader) Reader
```
> MultiReader 返回一个将提供的 Reader 在逻辑上串联起来的 Reader 接口。他们依次被读取。当所有的输入流都读取完毕， Read 才会返回 EOF 。如果 readers 中任一个返回了非 nil 非 EOF 的错误， Read 方法会返回该错误。

## func MultiWriter
```go
func MultiWriter(Writers ...Writer)Writer
```
> MultiWriter 创建一个 Writer 接口，会将提供给其的数据写入所有创建时提供的 Writer 接口。

## func Copy
```go
func Copy(dst Writer, src Reader)(written int64, err error)
```
> 将src的数据拷贝到dst，直到在src上到达EOF或发生错误。返回拷贝的字节数和遇到的第一个错误。
>
> 对成功的调用，返回值err为nil而非EOF，因为Copy定义为从src读取直到EOF，它不会将读取到EOF视为应报告的错误。如果src实现了WriterTo接口，本函数会调用src.WriteTo(dst)进行拷贝；否则如果dst实现了ReaderFrom接口，本函数会调用dst.ReadFrom(src)进行拷贝。

## func CopyN
```go
func CopyN(dst Writer, src Reader, n int64) (written int64, err error)
```
> 从src拷贝n个字节数据到dst，直到在src上到达EOF或发生错误。返回复制的字节数和遇到的第一个错误。只有err为nil时，written才会等于n。如果dst实现了ReaderFrom接口，本函数很调用它实现拷贝。

func ReadAtLeast
```go
func ReadAtLeast(r Reader, buf []byte, min int) (n int, err error)
```
> ReadAtLeast从r至少读取min字节数据填充进buf。函数返回写入的字节数和错误（如果没有读取足够的字节）。只有没有读取到字节时才可能返回EOF；如果读取了有但不够的字节时遇到了EOF，函数会返回ErrUnexpectedEOF。 如果min比buf的长度还大，函数会返回ErrShortBuffer。只有返回值err为nil时，返回值n才会不小于min。

## func ReadFull
```go
func ReadFull(r Reader, buf []byte)(n int, err error)
```
> ReadFull 从 r 精确地读取 len(buf) 字节数据填充进 buf 。函数返回写入的字节数和错误（如果没有读取足够的字节）。只有没有读取到字节时才可能返回 EOF ；如果读取了有但不够的字节时遇到了 EOF ，函数会返回 ErrUnexpectedEOF 。 只有返回值 err 为 nil 时，返回值 n 才会等于 len(buf)。

## func WriteString
```go
func WriteString(w Writer, s string) (n int, err error)
```
> WriteString 函数将字符串 s 的内容写入 w 中。如果 w 已经实现了 WriteString 方法，函数会直接调用该方法。