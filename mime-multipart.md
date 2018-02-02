# package multipart
```go
import "mime/multipart"
```
> multipart 实现了 MIME 的 multipart 解析，一般上传图片使用。

## type File
```go
type File interface {
	io.Reader
	io.ReaderAt
	io.Seeker
	io.Closer
}
```
> File 是一个接口，实现了对一个 multipar 信息中文件记录的访问。它的内容可以保持在内存或硬盘中，如果保持在硬盘中，底层类型就会是 *os.File

## type FileHeader
```go
type FileHeader struct {
	Filename string
	Header textproto.MIMEHeader
}
```
> FileHeader 描述一个 multipar 请求的(一个)文件子路的信息

## type (*FileHeader)Open
```go
func (fh *FileHeader)Open()(File, error)
```
> Open 方法打开并返回其关联的文件。

## type Part
```go
type Part struct {
	// 主体的头域，如果存在，是按 Go 的 http.Header 风格标准化的， “foo-bar” 改编为 “Foo-Bar”
	// 有一个特殊情况，"Content-Transfer-Encoding"头的值是"quoted-printable"。
	// 该头将从本map中隐藏，而主体会在调用Read时透明的解码。
	Header textproto.MIMEHeader
	// 
}
```
> Part 代表 multipart 主体的单独一个记录。

## func (*Part)FileName 
```go
func (p *Part)FileName()string
```
> 返回 Part 的 Content-Disposition 头的文件名参数。

## func (*Part)ormName()string
```go
func (p *Part)FormName()string
```
> 如果 p *Part 的 Content-Disposition 头值为"form-data"，则返回名字参数；否则返回空字符串。

## func (*Part)Read
```go
func (p *Part)Read(d []byte)(n int,error)
```
> Read 方法读取一个记录的主体，也就是其头域之后到下一记录之前的部分。

## func (*Part)Close
```go
func (p *Part)Closer
```

## type Form 
```go
type From struct {
	Value map[string][]string
	File map[string][]string
}
```
> Form 是一个解析过的 multipart。它的 File 参数部分保存在内存或者硬盘上，可以使用 *FileHeader 类型属性值的 Open 方法访问。它的 Value 参数部分保存为字符串，两者都以属性名为键

## func (*Form)RemoveAll
```go
func (f *Form)RemoveAll()error
```
> 删除 Form 关联的所有临时文件

## type Reader
```go
type Reader struct {
	//
}
```
> Reader 是 MIME 的 multipar 主体所有记录的迭代器。Reader 的底层会根据需要解析输入，不支持 Seek

## func NewReader
```go
func NewReader(r io.Reader, boundary string)*Reader
```
> 函数使用给出的MIME边界和r创建一个 multipart 读取器。边界一般从信息的"Content-Type" 头的"boundary"属性获取。可使用mime.ParseMediaType函数解析这种头域。

## func (*Reader)ReadForm
```go
func (r *Reader)ReadForm(maxMemory int64)(f *Form, err error)
```
> ReadForm 解析整个 multipart 信息中所有 Content-Disposition头的值为"form-data"的记录。它会把最多maxMemory字节的文件记录保存在内存里，其余保存在硬盘的临时文件里。

## func (*Reader)NextPart
```go
func (r *Reader)NextPart()(*Part, error)
```
> NextPart 返回 multipar 的下一个记录或者返回错误。如果没有更多记录会返回 io.EOF。

## type Writer
```go
type Writer struct {
	//
}
```
> Writer 类型用于生成 multipart 信息

## func NewWriter
```go
func NewWriter(w io.Writer)*Writer
```
> NewWriter 函数返回一个设定了一个随机边界的 Writer，数据写入 w

## func (*Writer)FormDataContentType
```go
func (w *Writer)FormDataContentType()string
```
> 方法返回 w 对应的 HTTPmultipart 请求的 Content-Type 的值，多以 multipart/form-data 起始

## func (*Writer)Boundary()string
```go
func (w *Writer)Boundary()string
```
> 方法返回该值 Writer 的边界

## func (*Writer)SetBoundary
```go
func (w *Writer)SetBoundary(boundary string)error
```
> SetBoundary 方法重写 Writer 默认的随机生成的边界为提供的 boundary 参数。方法必须在创建任何记录之前调用，boundary 只能包含特定的 ascii 字符，并且长度应在 1-69 字节之间。

## func (*Writer)CreatePart
```go
func (w *Writer)CreatePart(header textproto.MIMEHeader)(io.Writer, error)
```
> CreatePart 方法使用提供的 header 创建一个新的 multipart 记录。该记录的主体应该写入返回的 Writer 接口。调用本方法后，任何之前的记录都不能再写入。

## func CreateFormField
```go
func (w *Writer)CreateFormField(fieldname string)(io.Writer, error)
```
> CreateFormFielf 方法使用给出的属性名调用 CreatePart 方法

## func CreateFormFile
```go
func (w *Writer)CreateFormFile(fieldname string)(io.Writer, error)
```
> CreateFormFile是CreatePart方法的包装， 使用给出的属性名和文件名创建一个新的form-data头。

## func (*Writer)WriteField
```go
func (w *Writer)WriteFielf(fieldname, value string)error
```
> WriteField 方法调用 CreateFormField 并写入给出的 value。

## func (*Writer)Close()
```go
func (w *Writer)Close()error
```
> Close方法结束multipart信息，并将结尾的边界写入底层io.Writer接口。