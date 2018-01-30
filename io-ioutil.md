# package ioutil
```go
import "io/ioutil"
```
> 实现一些 I/O 的实用功能。

```go
var Discard io.Writer = devNull(0)
```
> Discard 是一个 io.Writer 接口，对所有 Write 调用都会无实际操作的成功返回。

## func NopCloser
```go
func NopCloser(r io.Reader)io.ReadCloser
```
> NopCloser 用一个无操作的 Close 方法包装 r io.Reader 返回 ReadCloser 接口。

## func ReadAll
```go
func ReadAll(r io.Reader)([]byte, error)
```
> ReadAll 从 r io.Reader 读取数据知道 EOF 或遇到 error,返回读取的数据和遇到的错误。成功的调用返回的 err 为 nil 而非 EOF。因为函数定义为读取 r io.Reader 直到 EOF,它不会将读取返回的 EOF 视为应报告的错误。

## ReadFile
```go
func ReadFile(filename string)([]byte, error)
```
> ReadFile 从 filename 指定的文件中读取数据并返回文件的内容。成功的调用返回的 err 为 nil 而非 EOF。因为本函数定义为都去整个文件，它不会将读取返回的 EOF 视为应报告的错误。

## func WriteFile
```go
func WriteFile(filename string, data []byte, perm os.FileMode)error
```
> 函数向 filename 指定的文件中写入数据。如果文件不存在将按给出的权限创建文件，否则在写入数据之前清空文件。

## func ReadDir
```go
func ReadDir(dirname string)([]os.FileInfo, error)
```
> 返回 dirname 指定的目录的目录信息的有序列表。

## func TempDir
```go
func TempDir(dir, prefix string)(name string, err error)
```
> 在 dir 目录里创建一个新的、使用 prefix 作为前缀的临时文件夹，并返回文件夹的路径。如果 dir 是空字符串，TempDir 使用默认用于临时文件的目录(os.TempDir)。不同程序同时调用该函数会创建不同的临时目录，调用本函数的程序有责任在不需要临时文件夹是摧毁它。

## func TempFile
```go
func TempFile(dir, prefix string)(f *os.File, err error)
```
> 在 dir 目录下创建一个新的、使用 prefix 为前缀的临时文件，以读写模式打开该文件并返回 os.File 指针。如果 dir 是空字符串，TempFile 使用默认用于临时文件的目录(os.TempDir)。不同程序同时调用该函数会创建不同的临时文件，调用本函数的程序有责任在不需要临时文件时摧毁它。
