# package zip

```go
import "archive/zip"
```
> zip 提供了 zip 档案文件的读写。 本包不支持跨硬盘的压缩。 为了向下兼容， FileHeader 同时拥有32位和64位的 Size 字段。64位字段总是包含正确的值，对普通格式的档案未见他们的值是相同的。对 zpi64 格式的档案文件 32位字段将是 0xffffffff,必须使用64位字段。

## Constants
```go
const(
	Store unit16 = 0
	Deflate uint16 = 0
)
```
> 预定义压缩算法

## Variables
```go
var(
	ErrFormat = errors.New("zip: not a balid zip FileHeader")
	ErrAlgorithm = errors.New("zip: unsupported compression algorithm")
	ErrChecksun = errors.New("zip: checksum error")
)
```

## type Compressor
```go
type Compressor func(io.Writer)(io.WriteCloser, error)
```
> Compressor 函数类型会返回一个 ioWriteCloser,该接口会将数据压缩后写入提供的接口。关闭时，应将缓冲中的数据刷新到底层接口中。

## type Decompressor
```go
type Decompressor func(io.Reader) io.ReadCloser
```
> Decompressor 函数类型会返回一个 io.ReadCloser,该接口的 Read 方法会将读取自提供的接口的数据提前解压缩。程序员有责任在读取结束时关闭该 io.ReadCloser。

## func RegisterCompressor
```go
func RegisterCompressor(method uint16, comp Compressor)
```
> RegisterCompressor 使用指定的方法 ID 注册一个 Compressor 类型函数。常用的方法 Store 和 Deflate 是内建的。

## func RegisterDecompressor
```go
func RegisterDecompressor(method uint16, d Decompressor)
```
> RegisterDecompressor 使用指定的方法 ID 注册一个 Decompressor 类型的函数。 

## type FileHeader
```go
type FileHeader{
	Name string
	CreatorVersion uint16
	ReaderVersion uint16
	Flags uint16
	Method uint16
	ModifiedTime uint16
	ModifiedDate uint16
	CRC32 uint32
	CompressedSize uint32	//弃用
	UncompressedSize uint32		//弃用
	CompressedSize64 uint64
	UncompressedSize64 uint64
	Extra []byte
	ExternalAttrs uint32
	Comment string
}
```
> FileHeader 描述 zip 文件中的一个文件。

## func FileInfoHeader
```go
func FileInfoHeader(fi os.FileInfo)(*FileHeader, error)
```
> FileInoHeader 返回一个根据 fi 填写了部分字段的 Header。因为 os.FileInfo 接口的 Name 方法只返回它描述的文件的无路径名，有可能需要将返回值的 Name 字段修改为文件的完整路径名。

## func (*FileHeader)FileIno
```go
func (h *FileHeader)FileInfo() os.FileInfo
```
> FileInfo 返回一个根据 h *FileHeader 的信息生成的 os.FFileInfo。

## func (h *FileHeader)Mode
```go
func (h *FileHeader) Mode()(Mode os.FileMode)
```
> Mode 返回 h *FileHeader 的权限和模式位。

## func (h *FileHeader)SetMode
```go
func (h *FileHeader)SetMode(mode os.FileMode)
```
> SetMode 修改 h *FileHeader 的权限和模式位

## func （h *FileHeader）ModTime
```go
func (h *FileHeader)ModTime() time.Time
```
> 返回最后一次修改的 UTC 时间。(精度 2s)

## func (h *FileHeader)SetModTime
```go
func (h *FileHeader)SetModTime(t thim.Time)
```
> 将 ModifiedTime 和 ModifiedDate 字段设置为指定的 UTC 时间。(精度 2s)

## type File
```go
type File struct{
	FileHeader
	// 内含隐藏或非导出字段
}
```
## func (*File)DataOffset
```go
func (f *File)DataOffset()(offset int64,err error)
```
> DataOffset 返回文件的可能存在的压缩数据相对于 zip 文件起始的偏移量。大多数调用者使用的 Open 代替，该方法会主动解压缩数据并验证。

## func (*file)Open
```go
func (f *File)Open()(rc io.ReadCloser, err error)
```
> Open 方法返回一个 io.ReadCloser 接口，提供读取文件内容的方法。可以同时读取多个文件。

## type Reader
```go
type Reader struct{
	File []*File
	Comment string
}
```

## func NewReader
```go
func NewReader(r io.ReaderAt, size int64)(*Reader, error)
```
> NewReader 返回一个从 r io.ReaderAt 读取数据的 *Reader, r 被假设其大小为 size 字节。

## type ReadCloser
```go
type ReadCloser struct {
	Reader
	//
}
```

## func OpenReader
```go
func OpenReader(name string)(*ReadCloser, error)
```
> OpenReader 会打开 name 指定的 zip 文件并返回一个 *ReadCloser。

## func (*ReadCloser)Close
```go
func (*rc *ReadCloser)Close()error
```
> Close 关闭 zip 文件 ，使它不能用于 I/O。

## type Writer
```go
type Writer struct{
	//
}
```

### Example
```go
package main

import (
	"archive/zip"
	"fmt"
	"io"
	"log"
	"os"
)

func main() {
	// 打开一个zip格式文件
	r, err := zip.OpenReader("file.zip")
	if err != nil {
		log.Fatal(err)
	}
	defer r.Close()

	// 迭代压缩文件中的文件，打印出文件中的内容
	for _, f := range r.File {
		fmt.Printf("文件名 %s:\n", f.Name)
		rc, err := f.Open()
		if err != nil {
			log.Fatal(err)
		}
		_, err = io.CopyN(os.Stdout, rc, int64(f.UncompressedSize64))
		if err != nil {
			log.Fatal(err)
		}
		rc.Close()
		fmt.Println()
	}
}
```

## func NewWriter
```go
func NewWriter(w io.Writer)*Writer
```
> NewWriter 创建并返回一个将 zip 文件写入 w 的 *Writer。

## func (*Writer)CreateHeader
```go
func (w *Writer)CreateHeader(fh *FileHeader)(io.Writer, error)
```
> 使用给出的 *FileHeader 来作为文件的元数据添加一个文件进 zip 文件。 本方法返回一个 io.Writer 接口(用于写入新添加文件的内容)。新增文件的内容必须在下一次调用 CreateHeader,Create 或 Close 方法之前全部写入。

## func (*Writer)Create
```go
func (w *Writer)Create(name string)(io.Writer, error)
```
> 使用给出的文件名添加一个文件进 zip 文件。本方法返回一个 io.Writer 接口(用户写入新添加文件的内容)。文件名必须是相对路径，不能以设备或斜杠开始，只接受/作为路径分割。新增文件的内容必须在下一次调用 CreateHeader、Create 或 Close 方法zhi钱全部写入。

## func (*Writer)Close
```go
func (w *Writer)Close()error
```
> Close 关闭该 *Writer。 本方法不会也没办法关闭下层的 io.Writer 接口。

### Example

```go
package main

import (
	"archive/zip"
	"bytes"
	"log"
	"os"
)

func main() {
	// 创建一个缓冲区用来保存压缩文件内容
	buf := new(bytes.Buffer)

	// 创建一个压缩文档
	w := zip.NewWriter(buf)

	// 将文件加入压缩文档
	var files = []struct {
		Name, Body string
	}{
		{"readme.txt", "This archive contains some text files."},
		{"gopher.txt", "Gopher names:\nGeorge\nGeoffrey\nGonzo"},
		{"todo.txt", "Get animal handling licence.\nWrite more examples."},
	}
	for _, file := range files {
		f, err := w.Create(file.Name)
		if err != nil {
			log.Fatal(err)
		}
		_, err = f.Write([]byte(file.Body))
		if err != nil {
			log.Fatal(err)
		}
	}

	// 关闭压缩文档
	err := w.Close()
	if err != nil {
		log.Fatal(err)
	}

	// 将压缩文档内容写入文件
	f, err := os.OpenFile("file.zip", os.O_CREATE|os.O_WRONLY, 0666)
	if err != nil {
		log.Fatal(err)
	}
	buf.WriteTo(f)
}
```