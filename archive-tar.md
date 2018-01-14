# package tar

```go
import "archive/tar"
```
> tar 实现了tar 格式压缩文件的存取。

## type Header
```go
type Header struct{
	Name string	//文件名
	Mode int64	// 权限
	Uid int	
	Gid int
	Size int64
	ModTime time.Time	// 修改时间
	Typeflag byte	// 头的类型
	Linkname string	// 链接目标名
	Uname string
	Gname string
	Devmajor int64	// ??
	Devminor int64	// ??
	AccessTime time.Time	//访问时间
	ChangeTime time.Time	//状态改变时间 ?? ModTime
	Xattrs map[string]string
}
```
> 代表 tar 档案文件里的单个头。 某些字段可能未使用。

## func FileInfoHeader
```go
func FileInfoHeader(fi os.FileInfo, link string)(*Header, error)
```
> FileInfoHeader 返回一个根据 fi os.FileInfo 填写了部分字段的Header。如果 fi os.FileInfo 描述的一个符号连接，FileInfoHeader 将 link 参数作为链接目标。如果fi os.FileInfo 描述一个目录，会在名字后面添加斜杠。 因为 os.FileInfo 接口的 Name 方法只返回它描述的文件的无路径名，有可能需要将返回值的 Name 字段修改为文件的完整路径名。

## func (h *Header)FileInfo
```go
func (h *Header) FileInfo() os.FileInfo
```
> FileInfo 返回该 Header 对应的文件信息。(os.FileInfo 类型)

### Example
```go
package main

import (
	"archive/tar"
	"fmt"
	"os"
)

func main() {
	fileinfo, err := os.Stat("README.md")
	if err != nil {
		fmt.Println(err)
	}
	h, err := tar.FileInfoHeader(fileinfo, "")
	h.Linkname = "haha"
	h.Gname = "test"
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println("tar.Header", h)
	fmt.Println("Header.FileInfo()", h.FileInfo())
}
```

### Output
> tar.Header &{README.md 438 0 0 314 2018-01-14 14:00:37.4800466 +0800 CST 48 haha  test 0 0 0001-01-01 00:00:00 +0000 UTC 0001-01-01 00:00:00 +0000 UTC map[]}

>Header.FileInfo() {0xc0420701a0}

## type Reader

```go
type Reader struct {
	// 内涵隐藏活非导出字段
}
```
> Reader提供了对一个 tar 档案文件的顺序读取。 一个 tar 档案文件包含一些列文件。Next 方法返回档案中的下一个文件(包括第一个),返回值可以被视为 io.Reader 来获取文件的数据。

## func NewReader
```go
func NewReader(r io.Reader) *Reader
```
> NewReader 创建一个从 r io.Reader 读取的 Reader

## func (tr *Reader)Next
```go
func (tr *Reader) Next()(*Header, error)
```
> 转入 tar 档案文件下一记录，它会返回下一记录的 Header

## func(tr *Reader)Read
```go
func (tr *Reader)Read(b []byte)(n int, err error)
```
> 从 tar 档案文件的当前记录读取数据，到达记录末端时返回 (0, io.EOF),直到调用 Next 方法转入一下记录。

### Example
```golang
import (
	"archive/tar"
	"fmt"
	"io"
	"os"
)

func main() {
	f, err := os.Open("test.tar")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()
	r := tar.NewReader(f)
	for hdr, err := r.Next(); err != io.EOF; hdr, err = r.Next() {
		if err != nil {
			fmt.Println(err)
			return
		}
		fileinfo := hdr.FileInfo()
		fmt.Println(fileinfo.Name())
		f, err := os.Create("test/" + fileinfo.Name())
		if err != nil {
			fmt.Println(err)
		}
		defer f.Close()
		_, err = io.Copy(f, r)
		if err != nil {
			fmt.Println(err)
		}
	}
}
```
### Output
> README.md

> open test/README.md: The system cannot find the path specified.

> invalid argument

## type Writer
```go
type Writer struct{
	
}
```
> Writer 类型提供了POSIX.1格式的 tar dangan文件的顺序写入。一个 tar 档案文件包含一系列文件。调用 WriteHeader 来写入一个心得文件，然后调用 Write 写入文件的数据，该记录写入的数据不能超过 hdr.Size 字节。

## func NewWriter
```go
func NewWriter(w io.Writer)*Writer
```
NewWriter 创建一个写入 w io.Writer 的 *Writer

## func (tw *Writer)WriteHeader
```go
func (tw *Writer) WriteHeader(hdr *Header)error
```
> WriteHeader 写入 hdr 并准备接受文件内容。如果不是第一次调用本方法，会调用Flush。 在 Close 之后调用本方法会返回 ErrWriteAfterClose。

## func (tw *Writer)Write
```go
func (tw *Writer)Write(b []byte)(n int, err error) 
```
>Write 向 tar 档案文件的当前记录中写入数据。如果写入的数据总数超出上一次调用 WriteHeader 的参数 hdr.Size 字节，返回 ErrWriteTooLong 错误。

## func (tw *Write)Flush
```go
func(tw *Write)Flush()error
```
> Flush 结束当前文件的写入。(可选)

## func (tw *Write)Close()error
```go
func (tw *Write)Close()error
```
> Close 关闭 tar 档案文件，会将缓冲中未写入下层的 io.Writer 接口的数据刷新到下层。

### Example
```go
package main

import (
	"archive/tar"
	"fmt"
	"io"
	"os"
)

func main() {
	f, err := os.Create("test.tar")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	tw := tar.NewWriter(f)
	defer tw.Close()

	fileinfo, err := os.Stat("README.md")
	if err != nil {
		fmt.Println(err)
	}
	hdr, err := tar.FileInfoHeader(fileinfo, "")
	if err != nil {
		fmt.Println(err)
	}

	err = tw.WriteHeader(hdr)
	if err != nil {
		fmt.Println(err)
		// return
	}

	f1, err := os.Open("archive-tar.md")
	if err != nil {
		fmt.Println(err)
		return
	}
	m, err := io.Copy(tw, f1)
	if err != nil {
		fmt.Println(err)
		// return
	}
	fmt.Println(m)
}
```

### Output
> archive/tar: write too long
> 生成一个 tar 档案

>> 注释掉 io 包 和 后面 archive-tar.md 至最后 会生成一个无法打开的文件

>> archive-tar.md 换成 README.md 则一切正常



