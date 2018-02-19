# package sync
```go
import "sync"
```
> sync 提供基本的同步基元，如互斥锁。除了Once和WaitGroup类型，大部分都是适用于低水平程序线程，高水平的同步使用channel通信更好一些

## type Locker
```go
type Locker interface {
	Lock()
	Unlock()
}
```
> Locker 接口代表一个可以加锁和解锁的对象

## type Once
```go
type Once struct {
	//
}
```
> Once 是只执行一次的对象

## func （*Once）Do
```go
func (o *Once)Do(f func())
```
> Do方法当且仅当第一次被调用时才执行函数f

## type Mutex
```go
type Mutex struct {
	//
}
```
> Mutex 是一个互斥锁，可以创建为其他结构体的字段；零值为解锁状态。Mutex类型的锁和线程无关，可以由不同的线程加锁和解锁

## func (*Mutex)Lock
```go
func (m *Mutex)Lock()
```
> Lock方法锁住m，如果m已经加锁，则阻塞直到m解锁

## func (*Mutex)Unlock
```go
func (m *Mutex)Unlock()
```
> Unlock 方法解锁m，如果m未加锁会导致运行时错误。锁和线程无关，可以由不同的线程加锁和解锁

## type RWMutex
```go
type RWMutex struct {
	//
}
```
> RWMutex 是读写互斥锁。该锁可以被同时多个读取者持有或唯一个写入者持有。RWMutex可以创建为其他结构体的字段；零值为解锁状态。RWMutex类型的锁也和线程无关，可以由不同的线程加读取锁/写入和解读取锁/写入锁

## func (*REMutex)Lock
```go
func (rw *RWMutex)Lock()
```
> Lock 方法将 rw 锁定为写入状态，禁止其他线程读取或写入

## func (*RWMutex)Unlock
```go
func (rw *RWMutex)Unlock()
```
> Unlock 方法解除rw的写入锁状态，如果m未加写入锁会导致运行时错误

## func (*RWMutex)RLock
```go
func (rw *RWMutex)Rlock()
```
> RLock 方法将rw锁定为读取状态，禁止其他线程写入，但不禁止读取

## func (*RWMutex)RUnlock
```go
func (rw *RWMutex)RUnlock()
```
> Runlock 方法解除rw的读取锁状态，如果m未加读取锁会导致运行时错误

## func (*RWMutex)RLocker
```go
func (rw *RWMutex)RLocker()Locker
```
> RLocker 方法返回一个互斥锁，通过调用rw.Rlock和rw.Runlock实现了Locker接口

## type Cond
```go
type Cond struct {
	// 在观测或更改条件时L会冻结
	L Locker
	//
}
```
> Cond实现了一个条件变量，一个线程集合地，供线程等待或者宣布某事件的发生
>
> 每个Cond实例都有一个相关的锁（一般是*Mutex或*RWMutex类型的值），它必须在改变条件时或者调用Wait方法时保持锁定。Cond可以创建为其他结构体的字段，Cond在开始使用后不能被拷贝

## func NewCond
```go
func NewCond(l Locker)*Cond
```
> 使用锁创建一个 *Cond

## func (*Cond)Broadcast
```go
func (c *Cond) Broadcast()
```
> Broadcast唤醒所有等待c的线程。调用者在调用本方法时，建议（但并非必须）保持c.L的锁定

## func (*Cond)Signal
```go
func (c *Cond)Signal()
```
> Signal 唤醒等待c的一个线程（如果存在）。调用者在调用本方法时，建议（但并非必须）保持c.L的锁定

## func (*Cond)Wait
```go
func (c *Cond)Wait()
```
> Wait自行解锁c.L并阻塞当前线程，在之后线程恢复执行时，Wait方法会在返回前锁定c.L。和其他系统不同，Wait除非被Broadcast或者Signal唤醒，不会主动返回
>
> 因为线程中Wait方法是第一个恢复执行的，而此时c.L未加锁。调用者不应假设Wait恢复时条件已满足，相反，调用者应在循环中等待

## type WaitGroup
```go
type WaitGroup struct {
	//
}
```
> WaitGroup 用于等待一组现成的结束。父线程调用Add方法来设定应等待的线程的数量。每个被等待的线程在结束时应调用Done方法。同时，主线程里可以调用Wait方法阻塞至所有线程结束

## func (*WaitGroup)Add
```go
func (wg *WaitGroup)Add(delta int)
```
> Add 方法向内部计数加上delta，delta可以是负数；如果内部计数器变为0，Wait方法阻塞等待的所有线程都会释放，如果计数器小于0，方法panic。注意Add加上正数的调用应在Wait之前，否则Wait可能只会等待很少的线程。一般来说本方法应在创建新的线程或者其他应等待的事件之前调用

## func (*WaitGroup)Done
```go
func (wg *WaitGroup)Done()
```
> Done方法减少WaitGroup计数器的值，应在线程的最后执行

## func (*WaitGroup)Wait
```go
func (wg *WaitGroup)Wait()
```
> Wait 方法阻塞直到WaitGroup计数器减为0

## type Pool
```go
type Pool struct {
	// 可选参数New指定一个函数在Get方法可能返回nil时来生成一个值
    // 该参数不能在调用Get方法时被修改
    New func() interface{}
    //
}
```
> Pool是一个可以分别存取的临时对象的集合...

## func (*Pool)Get
```go
func (p *Pool)Get()interface{}
```
> Get方法从池中选择任意一个item，删除其在池中的引用计数，并提供给调用者。Get方法也可能选择无视内存池，将其当作空的。调用者不应认为Get的返回这和传递给Put的值之间有任何关系。
>
> 假使Get方法没有取得item：如p.New非nil，Get返回调用p.New的结果；否则返回nil

## func (*Pool) Put
```go
func (p *Pool) Put(x interface{})
```
> Put方法将x放入池中