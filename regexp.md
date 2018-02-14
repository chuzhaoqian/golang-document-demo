# package regexp
```go
import "regexp"
```
> regexp包实现了正则表达式搜索。

## func QuoteMeta
```go
func QuoteMeta(s string)string
```
> QuoteMeta 返回将s中所有正则表达式元字符都进行转义后字符串。该字符串可以用在正则表达式中匹配字面值s。例如，QuoteMeta(`[foo]`)会返回`\[foo\]`。

## func Match
```go
func Match(pattern string, b []byte)(matched bool, err error)
```
> Match检查b中是否存在匹配pattern的子序列。更复杂的用法请使用Compile函数和Regexp对象。

## func MatchString
```go
func MatchString(pattern string, s string) (matched bool, err error)
```
> MatchString类似Match，但匹配对象是字符串。

## func MatchReader
```go
func MatchReader(pattern string, r io.RuneReader) (matched bool, err error)
```
> MatchReader类似Match，但匹配对象是io.RuneReader。

## type Regexp
```go
type Regexp struct {
	//
}
```
> Regexp 代表一个编译好的正则表达式。Regexp可以被多线程安全地同时使用。

## func Compile
```go
func Compile(expr string) (*Regexp, error)
```
> Compile解析并返回一个正则表达式。如果成功返回，该Regexp就可用于匹配文本。

## func CompilePOSIX
```go
func CompilePOSIX(expr string)(*Regexp, error)
```
> 类似Compile但会将语法约束到POSIX ERE（egrep）语法，并将匹配模式设置为leftmost-longest。

## func MustCompile
```go
func MustCompile(str string)*Regexp
```
> MustCompile类似Compile但会在解析失败时panic，主要用于全局正则表达式变量的安全初始化。

## func MustCompilePOSIX
```go
func MustCompilePOSIX(str string)*Regexp
```
> MustCompilePOSIX类似CompilePOSIX但会在解析失败时panic，主要用于全局正则表达式变量的安全初始化。

## func (*Regexp)String 
```go
func (re *Regexp)String()string
```
> String返回用于编译成正则表达式的字符串。

## func (*Regexp)LiteralPrefix
```go
func (re *Regexp)LiteralPrefix()(prefix string, complete bool)
```
> LiteralPrefix 返回一个字符串字面值prefix，任何匹配本正则表达式的字符串都会以prefix起始。 如果该字符串字面值包含整个正则表达式，返回值complete会设为真。

## func (*Regexp)NumSubexp
```go
func (re *Regexp)NumSubexp()int
```
> NumSubexp 返回该正则表达式中捕获分组的数量

## func (*Regexp)SubexpNames
```go
func (re *Regexp)SubexpNames()[]string
```
> SubexpNames返回该正则表达式中捕获分组的名字。第一个分组的名字是names[1]，因此，如果m是一个组匹配切片，m[i]的名字是SubexpNames()[i]。因为整个正则表达式是无法被命名的，names[0]必然是空字符串。该切片不应被修改。

## func (*Regexp)Longest
```go
func (re *Regexp)Longest
```
> Longest 让正则表达式在之后的搜索中都采用"leftmost-longest"模式。在匹配文本时，该正则表达式会尽可能早的开始匹配，并且在匹配过程中选择搜索到的最长的匹配结果。

## func (*Regexp) Match
```go
func (re *Regexp) Match(b []byte) bool
```
> Match检查b中是否存在匹配pattern的子序列。

## func (*Regexp) MatchString
```go
func (re *Regexp) MatchString(s string) bool
```
> MatchString类似Match，但匹配对象是字符串。

## func (*Regexp) MatchReader
```go
func (re *Regexp) MatchReader(r io.RuneReader) bool
```
> MatchReader类似Match，但匹配对象是io.RuneReader。

## func (*Regexp)Find
```go
func (re *Regexp)Find(b []byte)[]byte
```
> Find 返回保管正则表达式re在b中的最左侧的一个匹配结果的[]byte切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindString
```go
func (re *Regexp)FindString(s string)string
```
> FindString返回保管正则表达式re在s中的最左侧的一个匹配结果的字符串。如果没有匹配到，会返回""；但如果正则表达式成功匹配了一个空字符串，也会返回""。如果需要区分这种情况，请使用FindStringIndex 或FindStringSubmatch。

## func (*Regexp)FindIndex
```go
func (re *Regexp)FindIndex(b []byte)(loc []int)
```
> 返回保管正则表达式re在b中的最左侧的一个匹配结果的起止位置的切片（显然len(loc)==2）。匹配结果可以通过起止位置对b做切片操作得到：b[loc[0]:loc[1]]。如果没有匹配到，会返回nil。

## func (*Regexp)findStringIndex
```go
func (re *Regexp)FindStringIndex(s string)(loc []int)
```
> 返回保管正则表达式re在b中的最左侧的一个匹配结果的起止位置的切片（显然len(loc)==2）。匹配结果可以通过起止位置对b做切片操作得到：b[loc[0]:loc[1]]。如果没有匹配到，会返回nil。

## func (*Regexp)FindReaderIndex
```go
func (re *Regexp)FindReaderIndex(r io.RuneReader)(loc []int)
```
> 返回保管正则表达式re在b中的最左侧的一个匹配结果的起止位置的切片（显然len(loc)==2）。匹配结果可以在输入流r的字节偏移量loc[0]到loc[1]-1（包括二者）位置找到。如果没有匹配到，会返回nil。

## func (*Regexp)FindSubmatch
```go
func (re *Regexp)FindSubmatch(b []byte)[][]byte
```
> 返回一个保管正则表达式re在b中的最左侧的一个匹配结果以及（可能有的）分组匹配的结果的[][]byte切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindStringSubmatch
```go
func (re *Regexp)FindStringSubmatch(s string)[]string
```
> 返回一个保管正则表达式re在s中的最左侧的一个匹配结果以及（可能有的）分组匹配的结果的[]string切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindSubmatchIndex
```go
func (re *Regexp)FindSubmatchIndex(b []byte)[]int
```
> 返回一个保管正则表达式re在b中的最左侧的一个匹配结果以及（可能有的）分组匹配的结果的起止位置的切片。匹配结果和分组匹配结果可以通过起止位置对b做切片操作得到：b[loc[2*n]:loc[2*n+1]]。如果没有匹配到，会返回nil。

## func (*Regexp)FindStringSubmatchIndex
## func (*Regexp)FindReaderSubmatchIndex

## func (*Regexp)FindAll
```go
func (re *Regexp)FindAll(b []byte, n int)[][]byte
```
> 返回保管正则表达式re在b中的所有不重叠的匹配结果的[][]byte切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllString
```go
func (re *Regexp)FindAllString(s string, n int)[]string
```
> 返回保管正则表达式re在b中的所有不重叠的匹配结果的[]string切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllIndex
```go
func (re *Regexp)FindAllIndex(b []byte, n int)[][]int
```
> 返回保管正则表达式re在b中的所有不重叠的匹配结果的起止位置的切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllStringIndex
```go
func (re *Regexp)FindAllStringIndex(s string, n int)[][]int
```
> 返回保管正则表达式re在b中的所有不重叠的匹配结果的起止位置的切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllSubmatch
```go
func (re *Regexp)FindAllSubmatch(b []byte, n int)[][][]byte
```
> 返回一个保管正则表达式re在b中的所有不重叠的匹配结果及其对应的（可能有的）分组匹配的结果的[][][]byte切片。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllStringSubmatch
```go
func (re *Regexp)FindAllStringSubmatch(s string, n int)[][]string
```
> 返回一个保管正则表达式re在b中的所有不重叠的匹配结果及其对应的（可能有的）分组匹配的结果的[][]string切片。如果没有匹配到，会返回nil

## func (*Regexp)FindAllSubmatchIndex
```go
func (re *Regexp)FindAllSubmatchIndex(b []byte, n int)[][]int
```
> 返回一个保管正则表达式re在b中的所有不重叠的匹配结果及其对应的（可能有的）分组匹配的结果的起止位置的切片（第一层表示第几个匹配结果，完整匹配和分组匹配的起止位置对在第二层）。如果没有匹配到，会返回nil。

## func (*Regexp)FindAllStringSubmatchIndex
```go
func (re *Regexp)FindAllStringSubmatchIndex(s string, n int)[][]int
```
> 返回一个保管正则表达式re在b中的所有不重叠的匹配结果及其对应的（可能有的）分组匹配的结果的起止位置的切片（第一层表示第几个匹配结果，完整匹配和分组匹配的起止位置对在第二层）。如果没有匹配到，会返回nil。

## func (*Regexp)Split
```go
func (re *Regexp)Split(s string, n int)[]string
```
> Split将re在s中匹配到的结果作为分隔符将s分割成多个字符串，并返回这些正则匹配结果之间的字符串的切片。
>
> 返回的切片不会包含正则匹配的结果，只包含匹配结果之间的片段。当正则表达式re中不含正则元字符时，本方法等价于strings.SplitN。
>
> 参数n绝对返回的子字符串的数量：n > 0 : 返回最多n个子字符串，最后一个子字符串是剩余未进行分割的部分。n == 0: 返回nil (zero substrings)n < 0 : 返回所有子字符串

## func (*Regexp)Expand
```go
func (re *Regexp)Expand(dst []byte, template []byte, src []byte, match []int)[]byte
```
> Expand返回新生成的将template添加到dst后面的切片。在添加时，Expand会将template中的变量替换为从src匹配的结果。match应该是被FindSubmatchIndex返回的匹配结果起止位置索引。（通常就是匹配src，除非你要将匹配得到的位置用于另一个[]byte）
>
> 在template参数里，一个变量表示为格式如：$name或${name}的字符串，其中name是长度>0的字母、数字和下划线的序列。一个单纯的数字字符名如$1会作为捕获分组的数字索引；其他的名字对应(?P<name>...)语法产生的命名捕获分组的名字。超出范围的数字索引、索引对应的分组未匹配到文本、正则表达式中未出现的分组名，都会被替换为空切片。
> 
> $name格式的变量名，name会尽可能取最长序列：$1x等价于${1x}而非${1}x，$10等价于${10}而非${1}0。因此$name适用在后跟空格/换行等字符的情况，${name}适用所有情况。
> 
> 如果要在输出中插入一个字面值'$'，在template里可以使用$$。

## func (*Regexp)ExpandString
```go
func (re *Regexp)Expandstring(dst []byte, template string, src string, match []int) []byte
```
> ExpandString类似Expand，但template和src参数为字符串。它将替换结果添加到切片并返回切片，以便让调用代码控制内存申请。

## func (*Regexp)ReplaceAllLiteral
```go
func (re *Regexp)ReplaceAllLiteral(src, repl []byte)[]byte
```
> ReplaceAllLiteral 返回src的一个拷贝，将src中所有re的匹配结果都替换为repl。repl参数被直接使用，不会使用Expand进行扩展。

## func (*Regexp)ReplaceAllLiteralString
```go
func (re *Regexp)ReplaceAllLiteralString(src, repl string)string
```
> ReplaceAllLiteralString 返回src的一个拷贝，将src中所有re的匹配结果都替换为repl。repl参数被直接使用，不会使用Expand进行扩展。

## func (*Regexp)ReplaceAll 
```go
func (re *Regexp)ReplaceAll(src, repl []byte)[]byte
```
> 返回src的一个拷贝，将src中所有re的匹配结果都替换为repl。在替换时，repl中的'$'符号会按照Expand方法的规则进行解释和替换，例如$1会被替换为第一个分组匹配结果。

## func (*Regexp)RepalceAllString
```go
func (re *Regexp)ReplaceAllString(src, repl string)string
```
> 返回src的一个拷贝，将src中所有re的匹配结果都替换为repl。在替换时，repl中的'$'符号会按照Expand方法的规则进行解释和替换，例如$1会被替换为第一个分组匹配结果。

## func (*Regexp)ReplaceAllFunc
```go
func (re *Regexp)ReplaceAllFunc(src []byte, repl func([]byte)[]byte)[]byte
```
> 返回src的一个拷贝，将src中所有re的匹配结果（设为matched）都替换为repl(matched)。repl返回的切片被直接使用，不会使用Expand进行扩展。

## func (*Regexp)ReplaceAllStringFunc
```go
func (re *Regexp)ReplaceAllStringFunc(src string, repl func(string) string) string
```
> 返回src的一个拷贝，将src中所有re的匹配结果（设为matched）都替换为repl(matched)。repl返回的字符串被直接使用，不会使用Expand进行扩展。