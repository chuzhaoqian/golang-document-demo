# package md5
```go
import "md5"
```

## const BlockSize = 64
> MD5 字节块大小。

### Example
```go
package main

import (
	"crypto/md5"
	"fmt"
)

func main() {
	fmt.Println(md5.BlockSize)
}
```

## func Sum
```go
func Sum(data []byte)[Size]byte
```
> 返回数据 data 的 MD5 校验和。

### Example
```go
package main

import (
	"crypto/md5"
	"fmt"
)

func main() {
	result := md5.Sum([]byte("123456"))
	fmt.Println(result)
	fmt.Printf("%x", result)
}
```

## func New
```go
func New()hash.Hash
```
> 返回一个新的使用 MD5 校验的 hash.Hash 接口。

### Example
```go
package main

import (
	"crypto/md5"
	"fmt"
	"io"
)

func main() {
	h := md5.New()
	io.WriteString(h, "1")
	io.WriteString(h, "2")
	fmt.Println("%x", h.Sum(nil))
}
```
