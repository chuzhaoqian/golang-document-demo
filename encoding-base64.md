## package base64
```go
import "encoding/base64"
```
> base64 编码

```go
var StdEncoding = NewEncoding(encodeStd)

var URLEncoding = NewEncoding(encodeURL)
```

## type CorruptlnputError
```go
type CorruptlnputError int64
```

## func (CorruptInputError) Error
```go
func (e CorruptInputError) Error() string
```

## type Encoding
```go
type Encoding struct {
	//
}
```
> 双向的编码/解码协议，根据一个64字符的字符集定义，RFC 4648标准化了两种字符集。默认字符集用于MIME（RFC 2045）和PEM（RFC 1421）编码；另一种用于URL和文件名，用'-'和'_'替换了'+'和'/'。

## func NewEncoding
```go
func NewEncoding(encoder string) *Encoding
```
> 使用给出的字符集生成一个 *Encoding ，字符集必须是64字节的字符串。

## func (*Encoding) DecodedLen
```go
func (enc *Encoding) DecodedLen(n int) int
```
> 返回 n 字节 base64 编码的数据解码后的最大长度。

## func (*Encoding) Decode
```go
func (enc *Encoding) Decode(dst, src []byte) (n int, err error)
```
> 将 src 的数据解码后存入 dst，最多写DecodedLen(len(src))字节数据到 dst，并返回写入的字节数。 如果src包含非法字符，将返回成功写入的字符数和 CorruptInputError。换行符（\r、\n）会被忽略。

## func (*Encoding) DecodeString
```go
func (enc *Encoding) DecodeString(s string) ([]byte, error)
```
> 返回 base64 编码的字符串 s 代表的数据。

## func (*Encoding) EncodedLen
```go
func (enc *Encoding) EncodedLen(n int) int
```
> 返回 n 字节数据进行 base64 编码后的最大长度。

## func (*Encoding) Encode 
```go
func (enc *Encoding) Encode(dst, src []byte)
```
> 将src的数据编码后存入dst，最多写EncodedLen(len(src))字节数据到dst，并返回写入的字节数。函数会把输出设置为4的倍数，因此不建议对大数据流的独立数据块执行此方法，使用NewEncoder()代替

## func (*Encoding) EncodeToString
```go
func (enc *Encoding) EncodeToString(src []byte) string
```
> 返回将src编码后的字符串。

## func NewDecoder
```go
func NewDecoder(enc *Encoding, r io.Reader) io.Reader
```
> 创建一个新的base64流解码器。

## func NewEncoder
```go
func NewEncoder(enc *Encoding, w io.Writer) io.WriteCloser
```
> 创建一个新的base64流编码器。写入的数据会在编码后再写入w，base32编码每3字节执行一次编码操作；写入完毕后，使用者必须调用Close方法以便将未写入的数据从缓存中刷新到w中。

### Example
```go
package main

import (
	"encoding/base64"
	"fmt"
)

func main() {
	input := []byte("http://www.lawuyou.com+~")

	stdStr := base64.StdEncoding.EncodeToString(input)
	fmt.Println(stdStr) // aHR0cDovL3d3dy5sYXd1eW91LmNvbSt+
	decBytes, _ := base64.StdEncoding.DecodeString(stdStr)
	fmt.Println(decBytes)

	urlStr := base64.URLEncoding.EncodeToString(input)
	fmt.Println(urlStr) // aHR0cDovL3d3dy5sYXd1eW91LmNvbSt-
	decUBytes, _ := base64.URLEncoding.DecodeString(urlStr)
	fmt.Println(decUBytes)
}
```