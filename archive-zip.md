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
