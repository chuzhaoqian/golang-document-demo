# package reflect
```go
import "reflect"
```
> reflect 实现了运行时反射，允许程序操作任意类型的对象。典型用法是用静态类型interface{}保存一个值，通过调用TypeOf获取其动态类型信息，该函数返回一个Type类型值。调用ValueOf函数返回一个Value类型值，该值代表运行时的数据。Zero接受一个Type类型参数并返回一个代表该类型零值的Value类型值。

```go
const (
    SelectSend    // case Chan <- Send
    SelectRecv    // case <-Chan:
    SelectDefault // default
)
```

## type ValueError
```go
type ValueError struct {
	Method string
	Kind Kind
}
```
> 当一个 Value 类型值调用它不支持的方法时，将导致 ValueError

## func (*ValueError)Error
```go
func (e *ValueError)Error()string
```

## type Kind
```go
type Kind uint
```
> Kind 代表 Type 类型值表示的具体分类。零值表示非法分类。

```go
const (
    Invalid Kind = iota
    Bool
    Int
    Int8
    Int16
    Int32
    Int64
    Uint
    Uint8
    Uint16
    Uint32
    Uint64
    Uintptr
    Float32
    Float64
    Complex64
    Complex128
    Array
    Chan
    Func
    Interface
    Map
    Ptr
    Slice
    String
    Struct
    UnsafePointer
)
```

## func (Kind)String
```go
func (k Kind) String() string
```

## type StringHeader
```go
type StringHeader struct {
	Data uintptr 
	Len int 
}
```
> StringHeader 代表一个运行时的字符串。它不保证使用的可以移植性、安全性；实现在未来版本里也可能会改变。Data 字段也不能保证它指向的数据不会被当成垃圾收集，因此程序必须维护一个独立的、类型正确的指向底层数据的指针。

## type SliceHeader
```go
type SliceHeader struct {
	Date uintptr
	Len int
	Cap int
}
```
> SliceHeader 代表一个运行时的切片。它不保证使用的可移植性、安全性；它的实现在未来的版本里也可能会改变。而且，Data 字段也不能保证它指向的数据不会被当成垃圾收集，因此程序必须维护一个独立的、类型正确的指向底层数据的指针。

## type StructField
```go
type StructField struct {
	// Name是字段的名字。PkgPath是非导出字段的包路径，对导出字段该字段为""。
	Name string
	PkgPath string
	Type Type
	Tag structTag
	Offset uintptr	// 字段在结构体中的字节偏移量
	Index []int		// 用于Type.FieldByIndex时的索引切片
	Anonymous Bool	// 是否匿名字段
}
```
> StructField 类型描述结构体中的一个字段的信息

## type StructTag 
```go
type StructTag string
```
> StructTag 是结构体字段的标签。标签字符串是（可选的）空格分隔的一连串`key:"value"`对。每个键都是不包含控制字符、空格、双引号、冒号的非空字符串。每个值都应被双引号括起来，使用go字符串字面语法。

## func (StructTag)Get
```go
func (tag StructTag)Get(key string)string
```
> Get方法返回标签字符串中键key对应的值。如果标签中没有该键，会返回""。如果标签不符合标准格式，Get的返回值是不确定的。

## type ChanDir
```go
type ChanDir int
```
> ChanDir 表示通道类型的方向

```go
const (
    RecvDir ChanDir             = 1 << iota // <-chan
    SendDir                                 // chan<-
    BothDir = RecvDir | SendDir             // chan
)
```

## func (ChanDir)String
```go
func (d ChanDir)String()string
```

## type SelectDir
```go
type SelectDir int
```
> SelectDi 描述一个 SelectCase 的通信方向。

## type SelectCase
```go
type SelectCase struct {
    Dir  SelectDir // case的方向
    Chan Value     // 使用的通道（收/发）
    Send Value     // 用于发送的值
}
```
> SelectCase描述select操作中的单条case。Case的类型由通信方向Dir决定。
> 如果Dir是SelectDefault，该条case代表default case。Chan和Send字段必须是Value零值。如果Dir是SelectSend，该条case代表一个发送操作。Chan字段底层必须是一个chan类型，Send的底层必须是可以直接赋值给该chan类型成员类型的类型。如果Chan是Value零值，则不管Send字段是不是零值，该条case都会被忽略。如果Dir是SelectRecv，该条case代表一个接收操作。Chan字段底层必须是一个chan类型，而Send必须是一个Value零值。如果Chan是Value零值，该条case会被忽略，但Send字段仍需是Value零值。当该条case被执行时，接收到的值会被Select返回。

## func Select 
```go
func Select(cases []SelectCase) (chosen int, recv Value, recvOK bool)
```
> Select 函数执行 cases 切片描述的 select 操作。 类似 go 的select语句，它会阻塞直到至少一条case可以执行，从可执行的case中（伪）随机的选择一条，并执行该条case。它会返回选择执行的case的索引，以及如果执行的是接收case时，会返回接收到的值，以及一个布尔值说明该值是否对应于通道中某次发送的值（用以区分通道关闭时接收到的零值，此时recvOK会设为false）。

## type Method
```go
type Method struct {
    // Name是方法名。PkgPath是非导出字段的包路径，对导出字段该字段为""。
    // 结合PkgPath和Name可以从方法集中指定一个方法。
    Name    string
    PkgPath string
    Type  Type  // 方法类型
    Func  Value // 方法的值
    Index int   // 用于Type.Method的索引
}
```
> Method 代表一个方法。

## type Type
```go
type Type interface {
	// Kind 返回该接口的具体分类
	Kind() Kind
	// Name 返回该类型的自身包内的类型名，如果是未命名类型会返回""
	Name() string
	// PkgPath 返回类型的包路径
	// 如果类型为内建类型(string, error)或未命名类型(*T, struct{}, []int)，会返回""
	PkgPath() string
	// 返回类型的字符串表示。该字符串可能会使用短包名（如用base64代替"encoding/base64"）
	// 也不保证每个类型的字符串表示不同。如果要比较两个类型是否相等，请直接用Type类型比较。
	String() string
	// 返回要保存一个该类型的值需要多少字节；类似unsafe.Sizeof
	Size() uintptr
	// 返回当从内存中申请一个该类型值时，会对齐的字节数
	Align() int
	// 返回当该类型作为结构体的字段时，会对齐的字节数
	FieldAlign() int
	// 如果该类型实现了 u 代表的接口，会返回真
	Implements(u Type) bool
	// 如果该类型的值可以直接赋值给u代表的类型，返回真
	AssignableTo(u Type) bool
	// 如该类型的值可以转换为u代表的类型，返回真
	ConvertibleTo(u Type) bool
	// 返回该类型的字位数。如果该类型的Kind不是Int、Uint、Float或Complex，会panic
	Bits() int
	// 返回array类型的长度，如非数组类型将panic
    Len() int
	// 返回该类型的元素类型，如果该类型的Kind不是Array、Chan、Map、Ptr或Slice，会panic
    Elem() Type
	// 返回map类型的键的类型。如非映射类型将panic
    Key() Type
	// 返回一个channel类型的方向，如非通道类型将会panic
    ChanDir() ChanDir
	// 返回struct类型的字段数（匿名字段算作一个字段），如非结构体类型将panic
    NumField() int
	// 返回struct类型的第i个字段的类型，如非结构体或者i不在[0, NumField())内将会panic
    Field(i int) StructField
	// 返回索引序列指定的嵌套字段的类型，
    // 等价于用索引中每个值链式调用本方法，如非结构体将会panic
    FieldByIndex(index []int) StructField
	// 返回该类型名为name的字段（会查找匿名字段及其子字段），
    // 布尔值说明是否找到，如非结构体将panic
    FieldByName(name string) (StructField, bool)
	// 返回该类型第一个字段名满足函数match的字段，布尔值说明是否找到，如非结构体将会panic
    FieldByNameFunc(match func(string) bool) (StructField, bool)
	// 如果函数类型的最后一个输入参数是"..."形式的参数，IsVariadic返回真
    // 如果这样，t.In(t.NumIn() - 1)返回参数的隐式的实际类型（声明类型的切片）,如非函数类型将panic
    IsVariadic() bool
	// 返回func类型的参数个数，如果不是函数，将会panic
    NumIn() int
	// 返回func类型的第i个参数的类型，如非函数或者i不在[0, NumIn())内将会panic
    In(i int) Type
	// 返回func类型的返回值个数，如果不是函数，将会panic
    NumOut() int
	// 返回func类型的第i个返回值的类型，如非函数或者i不在[0, NumOut())内将会panic
    Out(i int) Type
	// 返回该类型的方法集中方法的数目
    // 匿名字段的方法会被计算；主体类型的方法会屏蔽匿名字段的同名方法；
    // 匿名字段导致的歧义方法会滤除
    NumMethod() int
	// 返回该类型方法集中的第i个方法，i不在[0, NumMethod())范围内时，将导致panic
    // 对非接口类型T或*T，返回值的Type字段和Func字段描述方法的未绑定函数状态
    // 对接口类型，返回值的Type字段描述方法的签名，Func字段为nil
    Method(int) Method
	// 根据方法名返回该类型方法集中的方法，使用一个布尔值说明是否发现该方法
    // 对非接口类型T或*T，返回值的Type字段和Func字段描述方法的未绑定函数状态
    // 对接口类型，返回值的Type字段描述方法的签名，Func字段为nil
    MethodByName(string) (Method, bool)
    //
}
```
> Type类型用来表示一个go类型。
>
> 不是所有 go 类型的 Type 值都能使用所有方法。请参见每个方法的文档获取使用限制。在调用有分类限定的方法时，应先使用 Kind 方法获知类型的分类。调用该分类不支持的方法会导致运行时的 panic。

## func TypeOf
```go
func TypeOf(i interface{})Type
```
> TypeOf 返回接口中保存的值的类型,TypeOf(nil)返回nil

## func PtrTo
```go
func PtrTo(t Type) Type
```
> PtrTo返回类型t的指针的类型。

## func SliceOf
```go
func SliceOf(t Type) Type
```
> SliceOf返回类型t的切片的类型。

## func MapOf
```go
func MapOf(key, elem Type) Type
```
> MapOf返回一个键类型为key，值类型为elem的映射类型。如果key代表的类型不是合法的映射键类型（即它未实现go的==操作符），本函数会panic。

## func ChanOf
```go
func ChanOf(dir ChanDir, t Type) Type
```
> ChanOf返回元素类型为t、方向为dir的通道类型。运行时GC强制将通道的元素类型的大小限定为64kb。如果t的尺寸大于或等于该限制，本函数将会panic。

## type Value
```go
type Value struct {
    // 
}
```
> Value为go值提供了反射接口。
>
> 不是所有go类型值的Value表示都能使用所有方法。请参见每个方法的文档获取使用限制。在调用有分类限定的方法时，应先使用Kind方法获知该值的分类。调用该分类不支持的方法会导致运行时的panic。
> 
> Value类型的零值表示不持有某个值。零值的IsValid方法返回false，其Kind方法返回Invalid，而String方法返回"<invalid Value>"，所有其它方法都会panic。绝大多数函数和方法都永远不返回Value零值。如果某个函数/方法返回了非法的Value，它的文档必须显式的说明具体情况。
> 
> 如果某个go类型值可以安全的用于多线程并发操作，它的Value表示也可以安全的用于并发。

## func ValueOf
```go
func ValueOf(i interface{}) Value
```
> ValueOf 返回一个初始化为 i 接口保管的具体值的 Value，ValueOf(nil)返回Value零值。

## func Zero
```go
func Zero(typ Type) Value
```
> Zero 返回一个持有类型 typ 的零值的 Value。注意持有零值的 Value 和Value 零值是两回事。Value 零值表示不持有任何值。例如Zero(TypeOf(42))返回一个Kind为Int、值为0的Value。Zero的返回值不能设置也不会寻址。

## func New
```go
func New(typ Type) Value
```
> New返回一个Value类型值，该值持有一个指向类型为typ的新申请的零值的指针，返回值的Type为PtrTo(typ)。

## func NewAt
```go
func NewAt(typ Type, p unsafe.Pointer) Value
```
> NewAt返回一个Value类型值，该值持有一个指向类型为typ、地址为p的值的指针。

## func Indirect
```go
func Indirect(v Value) Value
```
> 返回持有v持有的指针指向的值的Value。如果v持有nil指针，会返回Value零值；如果v不持有指针，会返回v。

## func MakeSlice
```go
func MakeSlice(typ Type, len, cap int) Value
```
> MakeSlice创建一个新申请的元素类型为typ，长度len容量cap的切片类型的Value值。

## func MakeMap
```go
func MakeMap(typ Type) Value
MakeMap创建一个特定映射类型的Value值。

## func MakeChan
```go
func MakeChan(typ Type, buffer int) Value
```
> MakeChan创建一个元素类型为typ、有buffer个缓存的通道类型的Value值。

## func MakeFunc
```go
func MakeFunc(typ Type, fn func(args []Value) (results []Value)) Value
```
> MakeFunc返回一个具有给定类型、包装函数fn的函数的Value封装。...

## func Append
```go
func Append(s Value, x ...Value) Value
```
> 向切片类型的Value值s中添加一系列值，x等Value值持有的值必须能直接赋值给s持有的切片的元素类型。

## func AppendSlice
```go
func AppendSlice(s, t Value) Value
```
> 类似Append函数，但接受一个切片类型的Value值。将切片t的每一个值添加到s。

## func (Value) IsValid
```go
func (v Value) IsValid() bool
```
> IsValid 返回v是否持有一个值。如果v是Value零值会返回假，此时v除了IsValid、String、Kind之外的方法都会导致panic。绝大多数函数和方法都永远不返回Value零值。如果某个函数/方法返回了非法的Value，它的文档必须显式的说明具体情况。

## func (Value) IsNil
```go
func (v Value) IsNil() bool
```
> IsNil 报告v持有的值是否为nil。v持有的值的分类必须是通道、函数、接口、映射、指针、切片之一；否则IsNil函数会导致panic。注意IsNil并不总是等价于go语言中值与nil的常规比较。例如：如果v是通过使用某个值为nil的接口调用ValueOf函数创建的，v.IsNil()返回真，但是如果v是Value零值，会panic。

## func (Value) Kind
```go
func (v Value) Kind() Kind
```
> Kind 返回 v 持有的值的分类，如果 v 是 Value 零值，返回值为 Invalid

## func (Value) Type
```go
func (v Value) Type() Type
```
> 返回 v 持有的值的类型的 Type 表示。

## func (Value) Convert
```go
func (v Value) Convert(t Type) Value
```
> Convert 将 v 持有的值转换为类型为 t 的值，并返回该值的 Value 封装。如果 go 转换规则不支持这种转换，会 panic。

## func (Value) Elem
```go
func (v Value) Elem() Value
```
> Elem 返回 v 持有的接口保管的值的 Value 封装，或者 v 持有的指针指向的值的 Value 封装。如果 v 的 Kind 不是 Interface 或 Ptr 会 panic；如果 v 持有的值为 nil，会返回 Value 零值。

## func (Value) Bool
```go
func (v Value) Bool() bool
```
> 返回v持有的布尔值，如果v的Kind不是Bool会panic

## func (Value) Int
```go
func (v Value) Int() int64
```
> 返回v持有的有符号整数（表示为int64），如果v的Kind不是Int、Int8、Int16、Int32、Int64会panic

## func (Value) OverflowInt
```go
func (v Value) OverflowInt(x int64) bool
```
> 如果v持有值的类型不能无溢出的表示x，会返回真。如果v的Kind不是Int、Int8、Int16、Int32、Int64会panic

## func (Value) Uint
```go
func (v Value) Uint() uint64
```
> 返回v持有的无符号整数（表示为uint64），如v的Kind不是Uint、Uintptr、Uint8、Uint16、Uint32、Uint64会panic

## func (Value) OverflowUint
```go
func (v Value) OverflowUint(x uint64) bool
```
> 如果v持有值的类型不能无溢出的表示x，会返回真。如果v的Kind不是Uint、Uintptr、Uint8、Uint16、Uint32、Uint64会panic

## func (Value) Float
```go
func (v Value) Float() float64
```
> 返回v持有的浮点数（表示为float64），如果v的Kind不是Float32、Float64会panic

## func (Value) OverflowFloat
```go
func (v Value) OverflowFloat(x float64) bool
```
> 如果v持有值的类型不能无溢出的表示x，会返回真。如果v的Kind不是Float32、Float64会panic

## func (Value) Complex
```go
func (v Value) Complex() complex128
```
> 返回v持有的复数（表示为complex64），如果v的Kind不是Complex64、Complex128会panic

## func (Value) OverflowComplex
```go
func (v Value) OverflowComplex(x complex128) bool
```
> 如果v持有值的类型不能无溢出的表示x，会返回真。如果v的Kind不是Complex64、Complex128会panic

## func (Value) Pointer
```go
func (v Value) Pointer() uintptr
```
> 将v持有的值作为一个指针返回。本方法返回值不是unsafe.Pointer类型，以避免程序员不显式导入unsafe包却得到unsafe.Pointer类型表示的指针。如果v的Kind不是Chan、Func、Map、Ptr、Slice或UnsafePointer会panic。
> 
> 如果v的Kind是Func，返回值是底层代码的指针，但并不足以用于区分不同的函数；只能保证当且仅当v持有函数类型零值nil时，返回值为0。
> 
> 如果v的Kind是Slice，返回值是指向切片第一个元素的指针。如果持有的切片为nil，返回值为0；如果持有的切片没有元素但不是nil，返回值不会是0。

## func (Value) Bytes
```go
func (v Value) Bytes() []byte
```
> 返回 v 持有的 []byte 类型值。如果v持有的值的类型不是 []byte 会panic。

## func (Value) String
```go
func (v Value) String() string
```
> 返回v持有的值的字符串表示。因为go的String方法的惯例，Value的String方法比较特别。和其他获取v持有值的方法不同：v的Kind是String时，返回该字符串；v的Kind不是String时也不会panic而是返回格式为"<T value>"的字符串，其中T是v持有值的类型。

## func (Value) InterfaceData
```go
func (v Value) InterfaceData() [2]uintptr
```
> 返回v持有的接口类型值的数据。如果v的Kind不是Interface会panic

## func (Value) Slice
```go
func (v Value) Slice(i, j int) Value
```
> 返回v[i:j]（v持有的切片的子切片的Value封装）；如果v的Kind不是Array、Slice或String会panic。如果v是一个不可寻址的数组，或者索引出界，也会panic

## func (Value) Slice3
```go
func (v Value) Slice3(i, j, k int) Value
```
> 是Slice的3参数版本，返回v[i:j:k] ；如果v的Kind不是Array、Slice或String会panic。如果v是一个不可寻址的数组，或者索引出界，也会panic。

## func (Value) Cap
```go
func (v Value) Cap() int
```
> 返回v持有值的容量，如果v的Kind不是Array、Chan、Slice会panic

## func (Value) Len
```go
func (v Value) Len() int
```
> 返回v持有值的长度，如果v的Kind不是Array、Chan、Slice、Map、String会panic

## func (Value) Index
```go
func (v Value) Index(i int) Value
```
> 返回v持有值的第i个元素。如果v的Kind不是Array、Chan、Slice、String，或者i出界，会panic

## func (Value) MapIndex
```go
func (v Value) MapIndex(key Value) Value
```
> 返回v持有值里key持有值为键对应的值的Value封装。如果v的Kind不是Map会panic。如果未找到对应值或者v持有值是nil映射，会返回Value零值。key的持有值必须可以直接赋值给v持有值类型的键类型。

## func (Value) MapKeys
```go
func (v Value) MapKeys() []Value
```
> 返回一个包含v持有值中所有键的Value封装的切片，该切片未排序。如果v的Kind不是Map会panic。如果v持有值是nil，返回空切片（非nil）。

## func (Value) NumField
```go
func (v Value) NumField() int
```
> 返回v持有的结构体类型值的字段数，如果v的Kind不是Struct会panic

## func (Value) Field
```go
func (v Value) Field(i int) Value
```
> 返回结构体的第i个字段（的Value封装）。如果v的Kind不是Struct或i出界会panic

## func (Value) FieldByIndex
```go
func (v Value) FieldByIndex(index []int) Value
```
> 返回索引序列指定的嵌套字段的Value表示，等价于用索引中的值链式调用本方法，如v的Kind非Struct将会panic

## func (Value) FieldByName
```go
func (v Value) FieldByName(name string) Value
```
> 返回该类型名为name的字段（的Value封装）（会查找匿名字段及其子字段），如果v的Kind不是Struct会panic；如果未找到会返回Value零值。

## func (Value) FieldByNameFunc
```go
func (v Value) FieldByNameFunc(match func(string) bool) Value
```
> 返回该类型第一个字段名满足match的字段（的Value封装）（会查找匿名字段及其子字段），如果v的Kind不是Struct会panic；如果未找到会返回Value零值。

## func (Value) Recv
```go
func (v Value) Recv() (x Value, ok bool)
```
> 方法从v持有的通道接收并返回一个值（的Value封装）。如果v的Kind不是Chan会panic。方法会阻塞直到获取到值。如果返回值x对应于某个发送到v持有的通道的值，ok为真；如果因为通道关闭而返回，x为Value零值而ok为假。

## func (Value) TryRecv
```go
func (v Value) TryRecv() (x Value, ok bool)
```
>TryRecv尝试从v持有的通道接收一个值，但不会阻塞。如果v的Kind不是Chan会panic。如果方法成功接收到一个值，会返回该值（的Value封装）和true；如果不能无阻塞的接收到值，返回Value零值和false；如果因为通道关闭而返回，返回值x是持有通道元素类型的零值的Value和false。

## func (Value) Send
```go
func (v Value) Send(x Value)
```
>方法向v持有的通道发送x持有的值。如果v的Kind不是Chan，或者x的持有值不能直接赋值给v持有通道的元素类型，会panic。

## func (Value) TrySend
```go
func (v Value) TrySend(x Value) bool
```
>TrySend尝试向v持有的通道发送x持有的值，但不会阻塞。如果v的Kind不是Chan会panic。如果成功发送会返回真，否则返回假。x的持有值必须可以直接赋值给v持有通道的元素类型。

## func (Value) Close
```go
func (v Value) Close()
```
>关闭v持有的通道，如果v的Kind不是Chan会panic

## func (Value) Call
```go
func (v Value) Call(in []Value) []Value
```
> Call方法使用输入的参数in调用v持有的函数。例如，如果len(in) == 3，v.Call(in)代表调用v(in[0], in[1], in[2])（其中Value值表示其持有值）。如果v的Kind不是Func会panic。它返回函数所有输出结果的Value封装的切片。和go代码一样，每一个输入实参的持有值都必须可以直接赋值给函数对应输入参数的类型。如果v持有值是可变参数函数，Call方法会自行创建一个代表可变参数的切片，将对应可变参数的值都拷贝到里面。

## func (Value) CallSlice
```go
func (v Value) CallSlice(in []Value) []Value
```
> CallSlice调用v持有的可变参数函数，会将切片类型的in[len(in)-1]（的成员）分配给v的最后的可变参数。例如，如果len(in) == 3，v.Call(in)代表调用v(in[0], in[1], in[2])（其中Value值表示其持有值，可变参数函数的可变参数位置提供一个切片并跟三个点号代表"解切片"）。如果v的Kind不是Func或者v的持有值不是可变参数函数，会panic。它返回函数所有输出结果的Value封装的切片。和go代码一样，每一个输入实参的持有值都必须可以直接赋值给函数对应输入参数的类型。

## func (Value) NumMethod
```go
func (v Value) NumMethod() int
```
> 返回v持有值的方法集的方法数目。

## func (Value) Method
```go
func (v Value) Method(i int) Value
```
> 返回v持有值类型的第i个方法的已绑定（到v的持有值的）状态的函数形式的Value封装。返回值调用Call方法时不应包含接收者；返回值持有的函数总是使用v的持有者作为接收者（即第一个参数）。如果i出界，或者v的持有值是接口类型的零值（nil），会panic。

## func (Value) MethodByName
```go
func (v Value) MethodByName(name string) Value
```
> 返回v的名为name的方法的已绑定（到v的持有值的）状态的函数形式的Value封装。返回值调用Call方法时不应包含接收者；返回值持有的函数总是使用v的持有者作为接收者（即第一个参数）。如果未找到该方法，会返回一个Value零值。

## func (Value) CanAddr
```go
func (v Value) CanAddr() bool
```
> 返回是否可以获取v持有值的指针。可以获取指针的值被称为可寻址的。如果一个值是切片或可寻址数组的元素、可寻址结构体的字段、或从指针解引用得到的，该值即为可寻址的。

## func (Value) Addr
```go
func (v Value) Addr() Value
```
> 函数返回一个持有指向v持有者的指针的Value封装。如果v.CanAddr()返回假，调用本方法会panic。Addr一般用于获取结构体字段的指针或者切片的元素（的Value封装）以便调用需要指针类型接收者的方法。

## func (Value) UnsafeAddr
```go
func (v Value) UnsafeAddr() uintptr
```
> 返回指向v持有数据的地址的指针（表示为uintptr）以用作高级用途，如果v不可寻址会panic。

## func (Value) CanInterface
```go
func (v Value) CanInterface() bool
```
> 如果CanInterface返回真，v可以不导致panic的调用Interface方法。

## func (Value) Interface
```go
func (v Value) Interface() (i interface{})
```
> 本方法返回v当前持有的值（表示为/保管在interface{}类型），等价于：
>> var i interface{} = (v's underlying value)
>
>如果v是通过访问非导出结构体字段获取的，会导致panic。

## func (Value) CanSet
```go
func (v Value) CanSet() bool
```
> 如果v持有的值可以被修改，CanSet就会返回真。只有一个Value持有值可以被寻址同时又不是来自非导出字段时，它才可以被修改。如果CanSet返回假，调用Set或任何限定类型的设置函数（如SetBool、SetInt64）都会panic。

## func (Value) SetBool
```go
func (v Value) SetBool(x bool)
```
> 设置v的持有值。如果v的Kind不是Bool或者v.CanSet()返回假，会panic。

## func (Value) SetInt
```go
func (v Value) SetInt(x int64)
```

## func (Value) SetUint
```go
func (v Value) SetUint(x uint64)
```

## func (Value) SetFloat
## func (Value) SetComplex
## func (Value) SetBytes
## func (Value) SetString
## func (Value) SetPointer
## func (Value) SetCap
## func (Value) SetLen
## func (Value) SetMapIndex
## func (Value) Set
## func Copy
## func DeepEqual
