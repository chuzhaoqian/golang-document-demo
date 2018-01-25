# package errors
```go
import "errors"
```
> errors 包实现了创建错误值得函数。

## func New
```go
func New(test string)error
```
> 使用字符创创建一个错误， fmt.Errorf

### Example
```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	err := errors.New("errors")
	fmt.Println(err)

	ferr := fmt.Errorf("fmt erros")
	fmt.Println(ferr)
}
```