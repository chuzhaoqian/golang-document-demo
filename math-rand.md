# package rand
```go
import "math/rand"
```
> rand 实现了伪随机数生成器。

```go
type Source interface {
	Int63() int64
	Seed(seed int64)
}
```
> Source 代表一个生成均匀分布在范围 [0, 1<<63] 的 int64 值得(伪随机的)资源。

## func NewSource
```
func NewSource(seed int64)Source
```
> 使用给定的种子创建一个伪随机资源。

## type Rand
```go
type Rand struct {
	//
}
```
> Rand 生成服从多种分布的随机数。

## func New 
```go
func New(src Source)*Rand
```
> 返回一个使用 src 生成的随机数来生成其他各种分布的随机数值的 *Rand

## func (*Rand)Seed
```go
func (r *Rand)Seed(seed int64)
```
> 使用给定的 seed 来初始化生成器到一个确定的状态

## func (*Rand)Int 
```go
func (r *Rand)Int()int 
```
> 返回一个非负的伪随机 int 值

## func (*Rand)Int31
```go
func (r *Rand)Int31()int32
```
> 返回一个 int332 类型的非负的 31 位伪随机数

## func (*Rand)Int63
```go
func (r *Rand)Int63()int64
```
> 返回一个 int64 类型的非负的 63 位伪随机数

## func (*Rand)Uint32
```go
func (r *Rand)Uint32()uint32 
```
> 返回一个 uint32 类型的非负的 32 位伪随机数

## func (*Rand)Intn
```go
func (r *Rand)Intn(n int)int
```
> 返回一个取值范围在 [0,n)的伪随机 int 值，如果 n <= 0 会 panic

## func (*Rand)Int31n
```go
func (r *Rand)Int31n(n int32)int32
```
> 返回一个取值范围在 [0,n)的伪随机 int32 值，如果 n <= 0 会 panic

## func (*Rand)Int63n
```go
func (r *Rand)Int63n(n int64)int64
```
> 返回一个取值范围在 [0,n)的伪随机 int64 值，如果 n <= 0 会 panic

## func (*Rand)Float32
```go
func (r *Rand)Float32()float32 
```
> 返回一个取值范围在[0.0, 1.0)的伪随机float32值。

## func (*Rand)Float64
```go
func (r *Rand) Float64() float64
```

## func (*Rand)NormFloat64
```go
func (r *Rand)NormFloat64()float64
```
> 返回一个服从标准状态分布(标准差=1，期望=0)、取值范围在[-math.MaxFloat64,+math.MaxFloat64]的 float64 值

## func (*Rand)ExpFloat64
```go
func (r *Rand)ExpFloat64()float64
```
> 返回一个服从标准指数分布（率参数=1，率参数是期望的倒数）、取值范围在(0, +math.MaxFloat64]的float64值

## func (*Rand)Perm
```go
func (r *Rand)Perm(n int)[]int
```
> 返回一个有 n 个元素的，[0,n) 范围内整数的伪随机排列的切片

## type Zipf
```go
type Zipf struct {
	//
}
```
> Zipf 生成服从齐普夫分布的随机数。

## func NewZipf
```go
func NewZipf(r *Rand, s float64, v float64, imax unit64)*Zipf
```
> NewZipf 返回一个 [0 imax] 范围内的齐普夫随机数生成器。齐普夫分布：值k出现的几率p(k)正比于(v+k)**(-s)，其中s>1且k>=0且v>=1。

## func (*Zipf)Uint64()uint64
```go
func (z *Zipf)Uint64()uint64
```
> Uint64返回一个服从Zipf对象描述的齐普夫分布的随机数。

## func Seed
```go
func Seed(seed int64)
```
使用给定的 seed 将默认资源初始化到一个确定的状态；如未调用 Seed,默认资源的行为就好像调用了 Seed(1)

## func Int
```go
func Int()int
```
> 返回一个非负的伪随机 int 值。

## func Int31
```go
func Int31()int32
```
> 返回一个 int32 类型的非负的 31 位位随机数

## func Int63
```go
func Int63()int64
```
> 返回一个 int64 类型的非负的 63 位位随机数

## func Uint32
```go
func Uint32()uint32
```
> 返回一个 uinit32 类型的非负的 32 位位随机数

## func Intn
```go
func Intn(n int)int
```
> 返回一个取值范围在[0,n)的伪随机int值，如果n<=0会panic。

## func Int31n
```go
func Int31n(n int32)int32
```
> 返回一个取值范围在[0,n)的伪随机int32值，如果n<=0会panic。

## fun Int63n
```go
func Int63n(n int64)int64
```
返回一个取值范围在[0, n)的伪随机int64值，如果n<=0会panic。

## func Float32
```go
func Float32()float32
```
> 返回一个取值范围在[0.0, 1.0)的伪随机float32值。

## func Float64
```go
func Float64()float64
```
> 返回一个取值范围在[0.0, 1.0)的伪随机float64值。

## func NormFloat64
```go
func NormFloat64()float64
```
> 返回一个服从标准正态分布（标准差=1，期望=0）、取值范围在[-math.MaxFloat64, +math.MaxFloat64]的float64值

## func ExpFloat64
```go
func ExpFloat64()float64
```
> 返回一个服从标准指数分布（率参数=1，率参数是期望的倒数）、取值范围在(0, +math.MaxFloat64]的float64值

## func Perm
```go
func Perm(n int)[]int
```
> 返回一个有n个元素的，[0,n)范围内整数的伪随机排列的切片。
