# package strings
```go
import "strings"
```
> strings 实现了用于操作字符串的简单函数

## func EqualFold
```go
func EqualFold(s, t string)bool
```
> 判断两个utf-8编码字符串（将unicode大写、小写、标题三种格式字符视为相同）是否相同。

## func HasPrefix
```go
func HasPrefix(s, prefix string)bool
```
> 判断s是否有前缀字符串prefix

## func HasSuffix
```go
func HasSuffix(s, suffix string)bool
```
> 判断s是否有后缀字符串 suffix

## func Contains
```go
func Contains(s, substr string)bool
```
> 判断字符串s是否包含子串substr

## func ContainsRune
```go
func ContainsRune(s string, r rune)bool
```
> 判断字符串s是否包含utf-8码值r

## func ContainsAny
```go
func ContainsAny(s, chars string)bool
```
> 判断字符串s是否包含字符串chars中的任一字符

## func Count
```go
func Count(s, sep string)int
```
> 返回字符串s中有几个不重复的sep子串

## func Index
```go
func Index(s, sep string)int
```
> 子串sep在字符串s中第一次出现的位置，不存在则返回-1

## func IndexByte
```go
func IndexByte(s string, c byte)int
```
> 字符c在s中第一次出现的位置，不存在则返回-1

## func IndexRune
```go
func IndexRune(s string, r rune)int
```
> unicode码值r在s中第一次出现的位置，不存在则返回-1

## func IndexAny
```go
func IndexAny(s, chars string)int
```
> 字符串chars中的任一utf-8码值在s中第一次出现的位置，如果不存在或者chars为空字符串则返回-1

## func IndexFunc
```go
func IndexFunc(s string, f func(rune)boo)int
```
> s中第一个满足函数f的位置i（该处的utf-8码值r满足f(r)==true），不存在则返回-1

## func LastIndex
```go
func LastIndex(s, sep string)int
```
> 子串sep在字符串s中最后一次出现的位置，不存在则返回-1

## func LastIndexAny
```go
func LastIndexAny(s, chars string)int
```
> 字符串chars中的任一utf-8码值在s中最后一次出现的位置，如不存在或者chars为空字符串则返回-1

## func LastIndexFunc
```go
func LastIndexFunc(s string, f func(rune)bool)int
```
> s中最后一个满足函数f的unicode码值的位置i，不存在则返回-1

## func Title
```go
func Title(s string)string
```
> 返回s中每个单词的首字母都改为标题格式的字符串拷贝
>> BUG: Title用于划分单词的规则不能很好的处理Unicode标点符号。

## func ToLower
```go
func ToLower(s string)string
```
> 返回将所有字母都转为对应的小写版本的拷贝

## func ToLowerSpecial
```go
func ToLowerSpecial(_case unicode.SpecialCase, s string) string
```
> 使用_case规定的字符映射，返回将所有字母都转为对应的小写版本的拷贝

## func ToUpper
```go
func ToUpper(s string)string
```
> 返回将所有字母都转为对应的大写版本的拷贝

## func ToUpperSpecial
```go
func ToUpperSpecial(_case unicode.SpecialCase, s string) string
```
> 使用_case规定的字符映射，返回将所有字母都转为对应的大写版本的拷贝

## func ToTitle
```go
func ToTitle(s string)string
```
> 返回将所有字母都转为对应的标题版本的拷贝

## func ToTitleSpecial
```go
func ToTitleSpecial(_case unicode.SpecialCase, s string) string
```
> 使用_case规定的字符映射，返回将所有字母都转为对应的标题版本的拷贝

## func Repeat
```go
func Repeat(s string, count int)string
```
> 返回count个s串联的字符串

## func Replace
```go
func Replace(s, old, new string, n int)string
```
> 返回将s中前n个不重叠old子串都替换为new的新字符串，如果n<0会替换所有old子串

## func Map
```go
func Map(mapping func(rune) rune, s string) string
```
> 将s的每一个unicode码值r都替换为mapping(r)，返回这些新码值组成的字符串拷贝。如果mapping返回一个负值，将会丢弃该码值而不会被替换。（返回值中对应位置将没有码值）

## func Trim
```go
func Trim(s, cutset string)string
```
> 返回将s前后端所有cutset包含的utf-8码值都去掉的字符串

## func TrimSpace
```go
func TrimSpace(s string)string
```
> 返回将s前后端所有空白（unicode.IsSpace指定）都去掉的字符串

## func TrimFunc
```go
func TrimFunc(s string, f func(rune)bool)string
```
> 返回将s前后端所有满足f的unicode码值都去掉的字符串

## func TrimLeft
```go
func TrimLeft(s string, cutset string)string
```
> 返回将s前端所有cutset包含的utf-8码值都去掉的字符串

## func TrimLeftFunc
```go
func TrimLeftFunc(s string, f func(rune) bool) string
```
> 返回将s前端所有满足f的unicode码值都去掉的字符串

## func TrimPrefix
```go
func TrimPrefix(s, prefix string) string
```
> 返回去除s可能的前缀prefix的字符串

## func TrimRight
```go
func TrimRight(s string, cutset string) string
```
> 返回将s后端所有cutset包含的utf-8码值都去掉的字符串

## func TrimRightFunc
```go
func TrimRightFunc(s string, f func(rune) bool) string
```
> 返回将s后端所有满足f的unicode码值都去掉的字符串

## func TrimSuffix
```go
func TrimSuffix(s, suffix string) string
```
> 返回去除s可能的后缀suffix的字符串

## func Fields
```go
func Fields(s string)[]string
```
> 返回将字符串按照空白（unicode.IsSpace确定，可以是一到多个连续的空白字符）分割的多个字符串。如果字符串全部是空白或者是空字符串的话，会返回空切片

## func FieldsFunc
```go
func FieldsFunc(s string, f func(rune) bool) []string
```
> 类似Fields，但使用函数f来确定分割符（满足f的unicode码值）。如果字符串全部是分隔符或者是空字符串的话，会返回空切片

## func Split
```go
func Split(s, sep string)[]string
```
> 用去掉s中出现的sep的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个字符串

## fun SplitN
```go
func SplitN(s, sep string, n int) []string
```
> 用去掉s中出现的sep的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个字符串。参数n决定返回的切片的数目：
>> n > 0 : 返回的切片最多n个子字符串；最后一个子字符串包含未进行切割的部分
>>
>> n == 0: 返回nil; 
>>
>> n < 0 : 返回所有的子字符串组成的切片

## func SplitAfter
```go
func SplitAfter(s, sep string) []string
```
> 用从s中出现的sep后面切断的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个字符串

## func SplitAfterN
```go
func SplitAfterN(s, sep string, n int) []string
```
> 用从s中出现的sep后面切断的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个字符串。参数n决定返回的切片的数目：
>> n > 0 : 返回的切片最多n个子字符串；最后一个子字符串包含未进行切割的部分
>> 
>> n == 0: 返回nil
>> 
>> n < 0 : 返回所有的子字符串组成的切

## func Join
```go
func Join(a []string, sep string)string
```
> 将一系列字符串连接为一个字符串，之间用sep来分隔

## type Reader
```go
type Reader struct {
	//
}
```
> Reader类型通过从一个字符串读取数据，实现了io.Reader、io.Seeker、io.ReaderAt、io.WriterTo、io.ByteScanner、io.RuneScanner接口

## func NewReader
```go
func NewReader(s string)*Reader
```
> NewReader 创建一个从s读取数据的 Reader。本函数类似bytes.NewBufferString，但是更有效率，且为只读的

## func (*Reader)Len 
```go
func (r *Reader)Len()int
```
> Len 返回 r 包含的字符串还没有被读取的部分

## func (*Reader)Read 
```go
func (r *Reader)Read(b []byte)n int, err error)
```

## func (*Reader)ReadByte
```go
func (r *Reader)ReadByte()(b byte, err error)
```

## func (*Reader)UnreadByte
```go
func (r *Reader)UnreadByte()error
```

## func (*Reader)ReadRune
```go
func (r *Reader)ReadRune()(ch rune, size int, err error)
```

## func (*Reader)UnreadRune
```go
func (r *Reader)UnreadRune()error
```

## func (*Reader)Seek
```go
func (r *Reader) Seek(offset int64, whence int) (int64, error)
```
## func (*Reader)ReadAt
```go
func (r *Reader) ReadAt(b []byte, off int64) (n int, err error)
```
## func (*Reader)WriteTo
```go
func (r *Reader) WriteTo(w io.Writer) (n int64, err error)
```
> WriteTo实现了io.WriterTo接口

## type Replacer
```go
type Replacer struct{
	//
}
```
> Replacer类型进行一系列字符串的替换

## func NewReplacer
```go
func NewReplacer(oldnew ...string) *Replacer
```
> 使用提供的多组old、new字符串对创建并返回一个*Replacer。替换是依次进行的，匹配时不会重叠

## func (*Replacer) Replace
```go
func (r *Replacer) Replace(s string) string
```
> Replace 返回s的所有替换进行完后的拷贝

## func (*Replacer) WriteString
```gp
func (r *Replacer) WriteString(w io.Writer, s string) (n int, err error)
```
> WriteString 向w中写入s的所有替换进行完后的拷贝