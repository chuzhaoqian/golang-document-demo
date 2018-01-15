# package crypto

```go
import "crypto"
```
> crypto 包搜集了常用的密码(算法)常量。

## type PublicKey
```go
type PublicKey interface{}
```
> 代表一个使用未指定算法的公钥。

## type PrivateKey
```go
type PrivateKey interface{}
```
> 代表一个使用未指定算法的私钥。

## type Hash
```go
type Hash uint
```
> Hash 用来识别/标识指定算法的私钥。

```go
const(
	MD4	Hash = 1 + iota
	MD5
	SHA1
	SHA224
	SHA256
	SHA384
	SHA512
	MD%SHA1
	RIPEMD160
)
```

## func (Hash)Available
```go
func (h Hash)Available()bool
```
> 报告石油有 hash 函数注册到该标识值。

## fun (Hash)Size
```go
func (h Hash)Size()int
```
> 返回给定 hash 函数返回值的字节长度。

## func (Hash)New
```go
func (h Hash)New()hash.Hash
```
> 创建一个使用给定的 hash 函数的 hash.Hash 接口，如果该标志值未注册 hash 函数，将会 panic。

## func RegisterHash
```go
func RegisterHash(h Hash, f func()hash.Hash)
```
> 注册一个返回给定 hash 接口实例的函数，并指定其标志值，该函数应在实现 hash 接口的包的 init 函数中调用。