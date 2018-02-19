# package runtime
```go
import "runtime"
```
> runtime 提供和 go 运行时环境的互操作

```go
const Compiler = "gc"
```
> Compiler 是编译工具链的名字，工具链会构建可执行的二进制文件

```go
const GOARCH string = theGoarch
```
> GOARCH是可执行程序的目标处理器架构（将要在该架构的机器上执行）：386、amd64或arm。

```go
const GOOS string = theGoos
```
> GOOS是可执行程序的目标操作系统（将要在该操作系统的机器上执行）：darwin、freebsd、linux等。

```go
var MemProfileRate int = 512 * 1024
```
> MemProfileRate 控制会在内存profile里记录和报告的内存分配采样频率。内存profile记录器平均每分配MemProfileRate字节进行一次分配采样。
> 
> 要在profile里包含每一个申请的块，可以将MemProfileRate设为1。要完全关闭profile的记录，设置本变量为0。
> 
> 处理内存profile的工具假设profile记录速度在整个程序的生命期是固定的，并等于当前值。修改内存profile的程序应该只进行一次，且尽可能早的修改（例如，在main函数的开始处）。

## type Error
```go
type Error interface {
	error
    // RuntimeError是一个无操作的函数，仅用于区别运行时错误和普通错误。
    // 具有RuntimeError方法的错误类型就是运行时错误类型。
    RuntimeError()
}
```
> Error 接口用来识别运行时错误。

## type TypeAssertionError
```go
type TypeAssertionError struct {
	//
}
```
> TypeAssertionError 表示一次失败的类型断言。

## func (*TypeAssertionError) Error
```go
func (e *TypeAssertionError) Error() string
```

## func (*TypeAssertionError) RuntimeError
```go
func (*TypeAssertionError) RuntimeError()
```

## func GOROOT
```go
func GOROOT()string
```
> GOROOT返回Go的根目录。如果存在GOROOT环境变量，返回该变量的值；否则，返回创建Go时的根目录。

## func Version
```go
func version()string
```
> 返回Go的版本字符串。它要么是递交的hash和创建时的日期；要么是发行标签如"go1.3"。

## func NumCPU
```go
func NumCPU()int
```
> NumCPU 返回本地机器的逻辑 CPU 个数

## func GOMAXPROCS
```go
func GOMAXPROCS(n int)int
```
> GOMAXPROCS 设置可同时执行的最大CPU数，并返回先前的设置。 若 n < 1，它就不会更改当前设置。本地机器的逻辑CPU数可通过 NumCPU 查询。本函数在调度程序优化后会去掉。

## func SetCPUProfileRate
```go
func SetCPUProfileRate(hz int)
```
> SetCPUProfileRate设置CPU profile记录的速率为平均每秒hz次。如果hz<=0，SetCPUProfileRate会关闭profile的记录。如果记录器在执行，该速率必须在关闭之后才能修改。
>
> 绝大多数使用者应使用runtime/pprof包或testing包的-test.cpuprofile选项而非直接使用SetCPUProfileRate。

## func CPUProfile
```go
func CPUProfile()[]byte
```
> CPUProfile返回二进制CPU profile堆栈跟踪数据的下一个chunk，函数会阻塞直到该数据可用。 如果profile的记录被关闭，且在记录器开着的时候积累的profile数据都被返回了，CPUProfile会返回nil。调用者在再次调用本函数之前应先保存返回的数据。
>
> 绝大多数使用者应使用 runtime/pprof包或testing包的-test.cpuprofile选项而非直接使用 CPUProfile。

## func GC 
```go
func GC()
```
> GC 执行一次垃圾回收

## func SetFinalizer
```go
func SetFinalizer(x, f interface{})
```
> SetFinalizer将x的终止器设置为f。当垃圾收集器发现一个不能接触的（即引用计数为零，程序中不能再直接或间接访问该对象）具有终止器的块时，它会清理该关联（对象到终止器）并在独立go程调用f(x)。这使x再次可以接触，但没有了绑定的终止器。如果SetFinalizer没有被再次调用，下一次垃圾收集器将视x为不可接触的，并释放x。
>
> ...

## type MemStats
```go
type MemStats struct {
	// 一般统计
    Alloc      uint64 // 已申请且仍在使用的字节数
    TotalAlloc uint64 // 已申请的总字节数（已释放的部分也算在内）
    Sys        uint64 // 从系统中获取的字节数（下面XxxSys之和）
    Lookups    uint64 // 指针查找的次数
    Mallocs    uint64 // 申请内存的次数
    Frees      uint64 // 释放内存的次数
    // 主分配堆统计
    HeapAlloc    uint64 // 已申请且仍在使用的字节数
    HeapSys      uint64 // 从系统中获取的字节数
    HeapIdle     uint64 // 闲置span中的字节数
    HeapInuse    uint64 // 非闲置span中的字节数
    HeapReleased uint64 // 释放到系统的字节数
    HeapObjects  uint64 // 已分配对象的总个数
    // L低层次、大小固定的结构体分配器统计，Inuse为正在使用的字节数，Sys为从系统获取的字节数
    StackInuse  uint64 // 引导程序的堆栈
    StackSys    uint64
    MSpanInuse  uint64 // mspan结构体
    MSpanSys    uint64
    MCacheInuse uint64 // mcache结构体
    MCacheSys   uint64
    BuckHashSys uint64 // profile桶散列表
    GCSys       uint64 // GC元数据
    OtherSys    uint64 // 其他系统申请
    // 垃圾收集器统计
    NextGC       uint64 // 会在HeapAlloc字段到达该值（字节数）时运行下次GC
    LastGC       uint64 // 上次运行的绝对时间（纳秒）
    PauseTotalNs uint64
    PauseNs      [256]uint64 // 近期GC暂停时间的循环缓冲，最近一次在[(NumGC+255)%256]
    NumGC        uint32
    EnableGC     bool
    DebugGC      bool
    // 每次申请的字节数的统计，61是C代码中的尺寸分级数
    BySize [61]struct {
        Size    uint32
        Mallocs uint64
        Frees   uint64
    }
}
```
> MemStats 记录内存申请和分配的统计信息。

## func ReadMemStats
```go
func ReadMemStats(m *MemStats)
```
> ReadMemStats 将内存申请和分配的统计信息填写进 m

## type MemProfileRecord
```go
type MemProfileRecord struct {
	AllocBytes, FreeBytes     int64       // 申请和释放的字节数
    AllocObjects, FreeObjects int64       // 申请和释放的对象数
    Stack0                    [32]uintptr // 该记录的调用栈踪迹，以第一个零值成员截止
}
```
> MemProfileRecord 用于描述某个调用栈序列申请和释放的活动对象等信息

## func (*MemProfileRecord)InUseBytes
```go
func (r *MemProfileRecord)InUseBytes()int64
```
> InUseBytes 返回正在使用的字节数（AllocBytes – FreeBytes）

## func (*MemProfileRecord) InUseObjects
```go
func (r *MemProfileRecord) InUseObjects() int64
```
> InUseObjects 返回正在使用的对象数（AllocObjects - FreeObjects）

## func (*MemProfileRecord) Stack
```go
func (r *MemProfileRecord) Stack() []uintptr
```
> Stack返回关联至此记录的调用栈踪迹，即r.Stack0的前缀。

## func MemProfile
```go
func MemProfile(p []MemProfileRecord, inuseZero bool) (n int, ok bool)
```
> MemProfile返回当前内存profile中的记录数n。若len(p)>=n，MemProfile会将此分析报告复制到p中并返回(n, true)；如果len(p)<n，MemProfile则不会更改p，而只返回(n, false)。
> 
> 如果inuseZero为真，该profile就会包含无效分配记录（其中r.AllocBytes>0，而r.AllocBytes==r.FreeBytes。这些内存都是被申请后又释放回运行时环境的）。
> 
> 大多数调用者应当使用runtime/pprof包或testing包的-test.memprofile标记，而非直接调用MemProfile。

## func Breakpoint
```go
func Breakpoint()
```
> Breakpoint 执行一个断点陷阱。

## func Stack
```go
func Stack(buf []byte, all bool) int
```
> Stack将调用其的go程的调用栈踪迹格式化后写入到buf中并返回写入的字节数。若all为true，函数会在写入当前go程的踪迹信息后，将其它所有go程的调用栈踪迹都格式化写入到buf中。

## func Caller
```go
func Caller(skip int) (pc uintptr, file string, line int, ok bool)
```
> Caller报告当前go程调用栈所执行的函数的文件和行号信息。实参skip为上溯的栈帧数，0表示Caller的调用者（Caller所在的调用栈）。（由于历史原因，skip的意思在Caller和Callers中并不相同。）函数的返回值为调用栈标识符、文件名、该调用在文件中的行号。如果无法获得信息，ok会被设为false。

## func Callers
func Callers(skip int, pc []uintptr) int
```
> 函数把当前go程调用栈上的调用栈标识符填入切片pc中，返回写入到pc中的项数。实参skip为开始在pc中记录之前所要跳过的栈帧数，0表示Callers自身的调用栈，1表示Callers所在的调用栈。返回写入p的项数。

## type StackRecord
```go
type StackRecord struct {
    Stack0 [32]uintptr // 该记录的调用栈踪迹，以第一个零值成员截止
}
```
> StackRecord描述单条调用栈。

## func (*StackRecord) Stack
```go
func (r *StackRecord) Stack() []uintptr
```
> Stack返回与记录相关联的调用栈踪迹，即r.Stack0的前缀。

## type Func
```go
type Func struct {
    // 
}
```

## func FuncForPC
```go
func FuncForPC(pc uintptr) *Func
```
> FuncForPC返回一个表示调用栈标识符pc对应的调用栈的*Func；如果该调用栈标识符没有对应的调用栈，函数会返回nil。每一个调用栈必然是对某个函数的调用。

## func (*Func) Name
```go
func (f *Func) Name() string
```
> Name返回该调用栈所调用的函数的名字。

## func (*Func) FileLine
```go
func (f *Func) FileLine(pc uintptr) (file string, line int)
```
> FileLine返回该调用栈所调用的函数的源代码文件名和行号。如果pc不是f内的调用栈标识符，结果是不精确的。

## func (*Func) Entry
```go
func (f *Func) Entry() uintptr
```
> Entry返回该调用栈的调用栈标识符。

## func NumCgoCall
```go
func NumCgoCall() int64
```
> NumCgoCall返回当前进程执行的cgo调用次数。

## func NumGoroutine
```go
func NumGoroutine() int
```
> NumGoroutine返回当前存在的Go程数。

## func Goexit
```go
func Goexit()
```
> Goexit终止调用它的go程。其它go程不会受影响。Goexit会在终止该go程前执行所有defer的函数。
>
> 在程序的main go程调用本函数，会终结该go程，而不会让main返回。因为main函数没有返回，程序会继续执行其它的go程。如果所有其它go程都退出了，程序就会崩溃。

## func Gosched
```go
func Gosched()
```
> Gosched使当前go程放弃处理器，以让其它go程运行。它不会挂起当前go程，因此当前go程未来会恢复执行。

## func GoroutineProfile
```go
func GoroutineProfile(p []StackRecord) (n int, ok bool)
```
> GoroutineProfile返回活跃go程的堆栈profile中的记录个数。若len(p) >= n，函数就会将profile中的记录复制到p中并返回(n, true)。若len(p) < n，则不会修改p，而只返回(n, false)。
>
> 绝大多数调用者应当使用runtime/pprof包，而非直接调用GoroutineProfile。

## func LockOSThread
```go
func LockOSThread()
```
> 将调用的go程绑定到它当前所在的操作系统线程。除非调用的go程退出或调用UnlockOSThread，否则它将总是在该线程中执行，而其它go程则不能进入该线程。

## func UnlockOSThread
```go
func UnlockOSThread()
```
> 将调用的go程解除和它绑定的操作系统线程。若调用的go程未调用LockOSThread，UnlockOSThread不做操作。

## func ThreadCreateProfile
```go
func ThreadCreateProfile(p []StackRecord) (n int, ok bool)
```
> 返回线程创建profile中的记录个数。如果len(p)>=n，本函数就会将profile中的记录复制到p中并返回(n, true)。若len(p)<n，则不会更改p，而只返回(n, false)。
>
> 绝大多数使用者应当使用runtime/pprof包，而非直接调用ThreadCreateProfile。

## type BlockProfileRecord
```go
type BlockProfileRecord struct {
    Count  int64
    Cycles int64
    StackRecord
}
```
> BlockProfileRecord用于描述某个调用栈序列发生的阻塞事件的信息。

## func SetBlockProfileRate
```go
func SetBlockProfileRate(rate int)
```
> SetBlockProfileRate控制阻塞profile记录go程阻塞事件的采样频率。对于一个阻塞事件，平均每阻塞rate纳秒，阻塞profile记录器就采集一份样本。
>
> 要在profile中包括每一个阻塞事件，需传入rate=1；要完全关闭阻塞profile的记录，需传入rate<=0。

## func BlockProfile
```go
func BlockProfile(p []BlockProfileRecord) (n int, ok bool)
```
> BlockProfile返回当前阻塞profile中的记录个数。如果len(p)>=n，本函数就会将此profile中的记录复制到p中并返回(n, true)。如果len(p)<n，本函数则不会修改p，而只返回(n, false)。
>
> 绝大多数使用者应当使用runtime/pprof包或testing包的-test.blockprofile标记， 而非直接调用 BlockProfile。
















