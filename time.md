# package time
```go
import "time"
```
> time 提供了时间的显示和测量用的函数。

```go
const (
    ANSIC       = "Mon Jan _2 15:04:05 2006"
    UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
    RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
    RFC822      = "02 Jan 06 15:04 MST"
    RFC822Z     = "02 Jan 06 15:04 -0700" // 使用数字表示时区的RFC822
    RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
    RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
    RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // 使用数字表示时区的RFC1123
    RFC3339     = "2006-01-02T15:04:05Z07:00"
    RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
    Kitchen     = "3:04PM"
    // 方便的时间戳
    Stamp      = "Jan _2 15:04:05"
    StampMilli = "Jan _2 15:04:05.000"
    StampMicro = "Jan _2 15:04:05.000000"
    StampNano  = "Jan _2 15:04:05.000000000"
)
```
> 这些预定义的版式用于Time.Format和Time.Parse函数

## type ParseError
```go
type ParseError struct {
	Layout     string
    Value      string
    LayoutElem string
    ValueElem  string
    Message    string
}
```
> ParseError 描述解析时间字符串时出现的错误

## func (*ParseError)Error
```go
func (e *ParseError)Error()string
```
> Error 返回 ParseError 的字符串表示

## type Weekday
```go
type Weekday int
```
> Weekday 代表一周的某一天

```go
const (
	Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

## func (Weekday)string
```go
func (d Weekday)String()string
```
> String返回该日（周几）的英文名（"Sunday"、"Monday"，……）

## type Month
```go
type Month int
```
> Month 代表一年的某个月

```go
const (
    January Month = 1 + iota
    February
    March
    April
    May
    June
    July
    August
    September
    October
    November
    December
)
```

## func (Month)String
```go
func (m Month)String()string
```
> String返回月份的英文名（"January"，"February"，……）

## type Location
```go
type Location struct {
	//
}
```
> Location 代表一个（关联到某个时间点的）地点，以及该地点所在的时区
>> ```go
var Local *Location = &localLoc
var UTC *Location = &utcLoc
```
>> Local代表系统本地，对应本地时区;UTC代表通用协调时间，对应零时区

## func LoadLocation
```go
func LoadLocation(name string)(*Location, error)
```
> LoadLocation返回使用给定的名字创建的Location
>
> 如果name是""或"UTC"，返回UTC；如果name是"Local"，返回Local；否则name应该是IANA时区数据库里有记录的地点名（该数据库记录了地点和对应的时区），如"America/New_York"
>
> LoadLocation函数需要的时区数据库可能不是所有系统都提供，特别是非Unix系统。此时LoadLocation会查找环境变量ZONEINFO指定目录或解压该变量指定的zip文件（如果有该环境变量）；然后查找Unix系统的惯例时区数据安装位置，最后查找$GOROOT/lib/time/zoneinfo.zip

## func FixedZone
```go
func FixedZone(name string, offset int)*Location
```
> FixedZone 使用给定的地点名 name 和 时间偏移量 offset（单位秒）创建并返回一个 Location

## func (*Location)String
```go
func (l *Location)String()string
```
> String 返回对时区信息的描述，返回值绑定为 LoadLocation 或 FixedZone 函数创建时的 name 参数

## type Time 
```go
type Time struct {
	//
}
```
> Time 代表一个纳秒精度的时间点
>
> 程序中应使用Time类型值来保存和传递时间，而不能用指针。就是说，表示时间的变量和字段，应为time.Time类型，而不是*time.Time.类型。一个Time类型值可以被多个go程同时使用。时间点可以使用Before、After和Equal方法进行比较。Sub方法让两个时间点相减，生成一个Duration类型值（代表时间段）。Add方法给一个时间点加上一个时间段，生成一个新的Time类型时间点
> 
> Time零值代表时间点January 1, year 1, 00:00:00.000000000 UTC。因为本时间点一般不会出现在使用中，IsZero方法提供了检验时间是否显式初始化的一个简单途径。
> 
> 每一个时间都具有一个地点信息（及对应地点的时区信息），当计算时间的表示格式时，如Format、Hour和Year等方法，都会考虑该信息。Local、UTC和In方法返回一个指定时区（但指向同一时间点）的Time。修改地点/时区信息只是会改变其表示；不会修改被表示的时间点，因此也不会影响其计算。

## func Date
```go
func Date(year int, month Month, day, hour, min, sec, nsec int, loc *Location)Time
```
> Date返回一个时区为loc、当地时间为：year-month-day hour:min:sec + nsec nanoseconds 的时间点

## func Now
```go
func Now()Time
```
> Now 返回当前本地时间

## func Parse 
```go
func Parse(layout, value string)(Time, error)
```
> Parse解析一个格式化的时间字符串并返回它代表的时间

## func ParseInLocation
```go
func ParseInLocation(layout, value string, loc *Location)(Time, error)
```
> ParseInLocation类似Parse但有两个重要的不同之处。第一，当缺少时区信息时，Parse将时间解释为UTC时间，而ParseInLocation将返回值的Location设置为loc；第二，当时间字符串提供了时区偏移量信息时，Parse会尝试去匹配本地时区，而ParseInLocation会去匹配loc

## func Unix 
```go
func Unix(sec, nsec Int64)Time
```
> Unix创建一个本地时间，对应sec和nsec表示的Unix时间（从January 1, 1970 UTC至该时间的秒数和纳秒数）。
>
> nsec的值在[0, 999999999]范围外是合法的

## func (Time)Location
```go
func (t Time)Location()*Location
```
> Location 返回 t 的地点和时区信息

## func (Time)Zone 
```go
func (t Time)Zone()(name string, offset int)
```
> Zone计算t所在的时区，返回该时区的规范名（如"CET"）和该时区相对于UTC的时间偏移量（单位秒）

## func (Time)IsZero
```go
func (t Time)IsZero()bool
```
> IsZero报告t是否代表Time零值的时间点，January 1, year 1, 00:00:00 UTC

## func (Time)Local
```go
func (t Time)Local()Time
```
> Local 返回采用本地和本地时区，但指向同一时间点的Time

## func (Time)UTC
```go
func (t Time)UTC()Time
```
> UTC 返回采用 UTC 和零时区，但指向同一时间点的 Time

## func (Time)In 
```go
func (t Time)In(loc *Location)Time
```
> In 返回采用 loc 指定的地点和时区，但指向同一时间点的 Time。如果 loc 为 nil 会 panic

## func (Time)Unix
```go
func (t Time)Unix()int64 
```
> Unix将t表示为Unix时间，即从时间点January 1, 1970 UTC到时间点t所经过的时间（单位秒）

## func (Time)UnixNano
```go
func (t Time)UnixNano()int64
```
> UnixNano 将t表示为Unix时间，即从时间点January 1, 1970 UTC到时间点t所经过的时间（单位纳秒）。如果纳秒为单位的unix时间超出了int64能表示的范围，结果是未定义的。注意这就意味着Time零值调用UnixNano方法的话，结果是未定义的

## func (Time)Equal
```go
func (t Time)Equal(u Time)bool
```
> 判断两个时间是否相同，会考虑时区的影响，因此不同时区标准的时间也可以正确比较。本方法和用t==u不同，这种方法还会比较地点和时区信息

## func (Time)Before
```go
func (t Time)Before(u Time)bool
```
> 如果t代表的时间点在u之前，返回真；否则返回假

## func (Time)After 
```go
func (t Time)Afer(u Time)bool
```
> 如果t代表的时间点在u之后，返回真；否则返回假

## func (Time)Date
```go
func (t Time)Date()(year int, month Month, day int)
```
> 返回时间点t对应的年、月、日

## func (Time)Clock
```go
func (t Time) Clock() (hour, min, sec int)
```
> 返回t对应的那一天的时、分、秒

## func (Time) Year
```go
func (t Time) Year() int
```
> 返回时间点t对应的年份

## func (Time) Month
```go
func (t Time) Month() Month
```
> 返回时间点t对应那一年的第几月

## func (Time) ISOWeek
```go
func (t Time) ISOWeek() (year, week int)
```
> 返回时间点t对应的ISO 9601标准下的年份和星期编号。星期编号范围[1,53]，1月1号到1月3号可能属于上一年的最后一周，12月29号到12月31号可能属于下一年的第一周

## func (Time)Hour 
```go
func (t Time)Hour()int
```
> 返回t对应的那一天的第几小时，范围 [0, 23]

## func (Time)Minute
```go
func (t Time)Minute()int
```
> 返回t对应的那一小时的第几分种，范围 [0, 59]

## func (Time)Second
```go
func (t Time)Second()int
```
> 返回t对应的那一分钟的第几秒，范围 [0, 59]

## func (Time)Nanosecond 
```go
func (t Time)Nanosecond()int
```
> 返回t对应的那一秒内的纳秒偏移量，范围 [0, 999999999]

## func (Time)Add 
```go
func (t Time)Add(d Duration)Time
```
> Add返回时间点t+d

## func (Time)AddDate
```go
func (t Time)AddDate(years int, months int, days int)Time
```
> AddDate返回增加了给出的年份、月份和天数的时间点Time。例如，时间点January 1, 2011调用AddDate(-1, 2, 3)会返回March 4, 2010
> 
> AddDate会将结果规范化，类似Date函数的做法。因此，举个例子，给时间点October 31添加一个月，会生成时间点December 1。（从时间点November 31规范化而来）

## func (Time)Sub 
```go
func (t Time)Sub(u Time)Duration
```
> 返回一个时间段t-u。如果结果超出了Duration可以表示的最大值/最小值，将返回最大值/最小值。要获取时间点t-d（d为Duration），可以使用t.Add(-d)

## func (Time)Round
```go
func (t Time)Round(d Duration)Time
```
> 返回距离t最近的时间点，该时间点应该满足从Time零值到该时间点的时间段能整除d；如果有两个满足要求的时间点，距离t相同，会向上舍入；如果d <= 0，会返回t的拷贝

## func (Time)Truncate
```go
func (t Time)Truncate(d Duration)Time
```
> 类似Round，但是返回的是最接近但早于t的时间点；如果d <= 0，会返回t的拷贝

## func (Time)Format
```go
func (t Time)Format(layout string)string
```
> Format根据layout指定的格式返回t代表的时间点的格式化文本表示

## func (Time)String
```go
func (t Time)String()string
```
> String返回采用如下格式字符串的格式化时间 : "2006-01-02 15:04:05.999999999 -0700 MST"

## func (Time)GobEncode
```go
func (t Time)GobEncode()([]byte, error)
```
> GobEncode实现了gob.GobEncoder接口

## func (*Time)GobDecode
```go
func (t *Time)GobDecode(data []byte)error
```
> GobEncode实现了gob.GobDecoder接口

## func (Time) MarshalBinary
```go
func (t Time) MarshalBinary() ([]byte, error)
MarshalBinary实现了encoding.BinaryMarshaler接口。

## func (*Time) UnmarshalBinary
```go
func (t *Time) UnmarshalBinary(data []byte) error
```
> UnmarshalBinary实现了encoding.BinaryUnmarshaler接口。

## func (Time) MarshalJSON
```go
func (t Time) MarshalJSON() ([]byte, error)
```
> MarshalJSON实现了json.Marshaler接口。返回值是用双引号括起来的采用RFC 3339格式进行格式化的时间表示，如果需要会提供小于秒的精度。

## func (*Time) UnmarshalJSON
```go
func (t *Time) UnmarshalJSON(data []byte) (err error)
```
> UnmarshalJSON实现了json.Unmarshaler接口。时间被期望是双引号括起来的RFC 3339格式。

## func (Time) MarshalText
```go
func (t Time) MarshalText() ([]byte, error)
```
> MarshalText实现了encoding.TextMarshaler接口。返回值是采用RFC 3339格式进行格式化的时间表示，如果需要会提供小于秒的精度。

## func (*Time) UnmarshalText
```go
func (t *Time) UnmarshalText(data []byte) (err error)
```
> UnmarshalText实现了encoding.TextUnmarshaler接口。时间被期望采用RFC 3339格式

## type Duration
```go
type Duration int64
```
> Duration类型代表两个时间点之间经过的时间，以纳秒为单位。可表示的最长时间段大约290年
>> ```go
const (
    Nanosecond  Duration = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
)
```

## func ParseDuration
```go
func ParseDuration(s string)(Duration, error)
```
> ParseDuration解析一个时间段字符串。一个时间段字符串是一个序列，每个片段包含可选的正负号、十进制数、可选的小数部分和单位后缀，如"300ms"、"-1.5h"、"2h45m"。合法的单位有"ns"、"us" /"µs"、"ms"、"s"、"m"、"h"

## func Since
```go
func Since(t Time)Duration
```
> Since 返回从 t 到现在经过的时间，等价于 time.Now().Sub(t)

## func (Duration)Hours
```go
func (d Duration)Hours()float64
```
> Hours 将时间段表示为 float64 类型的小时数

## func (Duration)Mimutes
```go
func (d Duration)Minutes()float64
```
> 将时间段表示为float64类型的分钟数

## func (Duration) Seconds
```go
func (d Duration) Seconds() float64
```
> 将时间段表示为float64类型的秒数

## func (Duration) Nanoseconds
```go
func (d Duration) Nanoseconds() int64
```
> 将时间段表示为int64类型的纳秒数，等价于int64(d)

## func (Duration)String
```go
func (d Duration)String()string
```
> 返回时间段采用 "72h3m0.5s" 格式的字符串表示。最前面可以有符号，数字+单位为一个单元，开始部分的0值单元会被省略；如果时间段<1s，会使用"ms"、"us"、"ns"来保证第一个单元的数字不是0；如果时间段为0，会返回"0"

## type Timer
```go
type Timer struct {
	C <-chan Time
	//
}
```
> Timer 类型代表单词时间事件。当Timer到期时，当时的时间会被发送给C，除非Timer是被AfterFunc函数创建的

## func NewTimer
```go
func NewTimer(d Duration)*Timer
```
> NewTimer 创建一个 Timer，它会在最少过去时间段d后到期，向其自身的C字段发送当时的时间

## func AferFunc
```go
func AferFunc(d Duration, f func())*Timer
```
> AfterFunc 另起一个go程等待时间段d过去，然后调用f。它返回一个Timer，可以通过调用其Stop方法来取消等待和对f的调用

## func (*Timer)Reset
```go
func (t *Timer)Reset(d Duration)bool
```
> Reset使t重新开始计时，（本方法返回后再）等待时间段d过去后到期。如果调用时t还在等待中会返回真；如果t已经到期或者被停止了会返回假

## func (*Timer)Stop
```go
func (t *Timer)Stop()bool
```
> Stop停止Timer的执行。如果停止了t会返回真；如果t已经被停止或者过期了会返回假。Stop不会关闭通道t.C，以避免从该通道的读取不正确的成功

## type Ticker
```go
type Ticker struct {
	C <-chan Time // 周期性传递时间信息的通道
	//
}
```
> Ticker 保管一个通道，并每隔一段时间向其传递"tick"

## func NewTicker
```go
func NewTicker(d Duration)*Ticker
```
> NewTicker 返回一个新的Ticker，该Ticker包含一个通道字段，并会每隔时间段d就向该通道发送当时的时间。它会调整时间间隔或者丢弃tick信息以适应反应慢的接收者。如果d<=0会panic。关闭该Ticker可以释放相关资源

## func (*Ticker)Stop
```go
func (t *Ticker)Srop
```
> Stop 关闭一个Ticker。在关闭后，将不会发送更多的tick信息。Stop不会关闭通道t.C，以避免从该通道的读取不正确的成功

## func Sleep
```go
func Sleep(d Duration)
```
> Sleep阻塞当前go程至少d代表的时间段。d<=0时，Sleep会立刻返回

## func After
```go
func After(d Duration)<-chan Time
```
> After 会在另一线程经过时间段d后向返回值发送当时的时间。等价于NewTimer(d).C

## func Tick
```go
func Tick(d Duration) <-chan Time
```
> Tick是NewTicker的封装，只提供对Ticker的通道的访问。如果不需要关闭Ticker，本函数就很方便