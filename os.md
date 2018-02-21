# package os
```go
import "os"
```
> os 提供了不依赖平台的操作系统函数接口。设计为 Unix 风格，错误处理是 go 风格；失败的调用会返回错误值而非错误码。
>> os 依赖于 syscall。优先使用 os 中提供的功能，而不是 syscall

```go
const (
    O_RDONLY int = syscall.O_RDONLY // 只读模式打开文件
    O_WRONLY int = syscall.O_WRONLY // 只写模式打开文件
    O_RDWR   int = syscall.O_RDWR   // 读写模式打开文件
    O_APPEND int = syscall.O_APPEND // 写操作时将数据附加到文件尾部
    O_CREATE int = syscall.O_CREAT  // 如果不存在将创建一个新文件
    O_EXCL   int = syscall.O_EXCL   // 和O_CREATE配合使用，文件必须不存在
    O_SYNC   int = syscall.O_SYNC   // 打开文件用于同步I/O
    O_TRUNC  int = syscall.O_TRUNC  // 如果可能，打开时清空文件
)
```
> 用于包装底层系统的参数用于 Open 函数，不是所有的 flag 都能在特定系统里使用的。

```go
const (
    SEEK_SET int = 0 // 相对于文件起始位置seek
    SEEK_CUR int = 1 // 相对于文件当前位置seek
    SEEK_END int = 2 // 相对于文件结尾位置seek
)
```
> 指定Seek函数从何处开始搜索（即相对位置）

```go
const (
    PathSeparator     = '/' // 操作系统指定的路径分隔符
    PathListSeparator = ':' // 操作系统指定的表分隔符
)

const DevNull = "/dev/null"
```
> DevNull是操作系统空设备的名字。在类似Unix的操作系统中，是"/dev/null"；在Windows中，为"NUL"。

```go
var (
    ErrInvalid    = errors.New("invalid argument")
    ErrPermission = errors.New("permission denied")
    ErrExist      = errors.New("file already exists")
    ErrNotExist   = errors.New("file does not exist")
)
```
> 一些可移植的、共有的系统调用错误。

```go
var (
    Stdin  = NewFile(uintptr(syscall.Stdin), "/dev/stdin")
    Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")
    Stderr = NewFile(uintptr(syscall.Stderr), "/dev/stderr")
)
```
> Stdin、Stdout和Stderr是指向标准输入、标准输出、标准错误输出的文件描述符。

```go
var Args []string
```
> Args保管了命令行参数，第一个是程序名。

## func Hostname
```go
func Hostname()(name string, err error)
```
> Hostname 返回内核提供的主机名

## func Getpagesize
```go
func Hostname()(name string, err error)
```
> Hostname 返回内核提供的主机名

## func Getpagesize
```go
func Getpagesize()int
```
> Getpagesize 返回底层的系统内存页的尺寸

## func Environ
```go
func Environ()[]string
```
> Environ 返回表示环境变量的格式为 “key=value” 的字符串的切片拷贝

## func Getenv
```go
func Getenv(key string)string
```
> Getenv 检索并返回名 key 的环境变量的值。如果不存在该环境变量会返回空字符串。

## func Setenv
```go
func Setenv(key, value string)error
```
> Setenv 设置名为 key 的环境变量。如果出错会返回错误。

## func Clearenv
```go
func Clearenv()
```
> Clearenv 删除所有环境变量

## func Exit 
```go
func Exit(code int)
```
> Exit 让当前程序以给出的状态码 code 退出。一般来说，状态码0表示成功，非0表示出错。程序会立刻终止，defer的函数不会被执行。

## func Expand
```go
func Expand(s string, mapping func(string)string)string
```
> Expand 函数替换 s 中的 ${var} 或 $var 为 mapping(var) 。例如，os.ExpandEnv(s) 等价于 os.Expand(s, os.Getenv)。

## func ExpandEnv
```go
func ExpandEnv(s string)string
```
> ExpandEnv函数替换s中的${var}或$var为名为var 的环境变量的值。引用未定义环境变量会被替换为空字符串

## func Getuid
```go
func Getuid()int
```
> Getuid 返回调用者的用户 ID

## func Geteuid
```go
func Geteuid()int
```
> Geteuid 返回调用者的有效用户 ID

## func Getgid
```go
func Getgid()int
```
> Getgid 返回调用者的组 ID

## func Getegid
```go
func Getegid()int
```
> Getegid 返回调用者的有效组 ID

## func Getgroups
```go
func Getgroups()([]int, error)
```
> Getgroups 返回调用者所属的所有用户组的组 ID

## func Getpid
```go
func Getpid()int
```
> Getpid 返回调用者所在进程的进程 ID

## func Getppid
```go
func Getppid()int
```
> Getppid 返回调用者所在进程的父进程的进程 ID

## type Signal
```go
type Signal interface {
	String()string
	Signal()	// 用来区分其他实现了 Stringer 接口的类型
}
```
> Signal 代表一个操作系统信号。一般底层是依赖于操作系统的：在Unix中，它是syscall.Signal类型。

## type PathError
```go
type PathError struct {
	Op string
	Path string
	Err error
}
```
> PathError 记录一个错误，以及导致错误的路径。

## func (*PathError)Error 
```go
func (e *PathError)Error()string
```

## type LinkError
```go
type LinkError struct {
	Op string
	Old string
	New string
	Err error
}
```
> LinkError 记录在 Link、Symlink、Rename 系统调用时出现的错误，以及导致错误的路径

## func (*LinkError)Error
```go
func (e *LinkError)Error()string
```

## type SyscallError
```go
type SyscallError struct {
	Syscall string
	Err error
}
```
> SyscallError 记录某个系统调用出现的错误

## func (*SyscallError)Error
```go
func (e *SyscallError)Error()string
```

## func NewSyscallError
```go
func NewSyscallError(syscall string, err error)error
```
> NewSyscallError 返回一个指定系统调用名称和错误细节的 SyscallError。如果 err 为 nil, 本函数会返回 nil

## type FileMode
```go
type FileMode uint32
```
> FileMode 代表文件的模式和权限位。这些字位在所有的操作系统都有相同的含义，因此文件的信息可以在不同的操作系统之间安全的移植。不是所有的位都能用于所有的系统，唯一共有的是用于表示目录的 ModeDir 位

```go
const (
    // 单字符是被String方法用于格式化的属性缩写。
    ModeDir        FileMode = 1 << (32 - 1 - iota) // d: 目录
    ModeAppend                                     // a: 只能写入，且只能写入到末尾
    ModeExclusive                                  // l: 用于执行
    ModeTemporary                                  // T: 临时文件（非备份文件）
    ModeSymlink                                    // L: 符号链接（不是快捷方式文件）
    ModeDevice                                     // D: 设备
    ModeNamedPipe                                  // p: 命名管道（FIFO）
    ModeSocket                                     // S: Unix域socket
    ModeSetuid                                     // u: 表示文件具有其创建者用户id权限
    ModeSetgid                                     // g: 表示文件具有其创建者组id的权限
    ModeCharDevice                                 // c: 字符设备，需已设置ModeDevice
    ModeSticky                                     // t: 只有root/创建者能删除/移动文件
    // 覆盖所有类型位（用于通过&获取类型位），对普通文件，所有这些位都不应被设置
    ModeType = ModeDir | ModeSymlink | ModeNamedPipe | ModeSocket | ModeDevice
    ModePerm FileMode = 0777 // 覆盖所有Unix权限位（用于通过&获取类型位）
)
```
> 这些被定义的位是FileMode最重要的位。另外9个不重要的位为标准Unix rwxrwxrwx权限（任何人都可读、写、运行）。这些（重要）位的值应被视为公共API的一部分，可能会用于线路协议或硬盘标识：它们不能被修改，但可以添加新的位。

## func (FileMode)IsDir
```go
func (m FileMode)IsDir()bool
```
> IsDir 报告 m 是否是一个目录

## func (FileMode)IsRegular
```go
func (m FileMode)IsRegular()bool
```
> IsRegular 报告 m 是否是一个普通文件

## func (FileMode)Perm 
```go
func (m FileMode)Perm()FileMode
```
> Perm 方法返回 m 的 Unix 权限位

## func (FileMode)String
```go
func (m FileMode)String()string
```

## type FileInfo
```go
type FileInfo interface {
	Name() string	// 文件的名字(不含扩展名)
	Size() int64	// 普通文件返回值表示大小；其他文件返回值含义各系统不同
	Mode() FileMode	// 文件的模式位
	ModTime() time.Time		// 文件的修改时间
	IsDir() bool	// 等价于 Mode()IsDir()
	Sys() interface{}	// 底层数据来源(可以返回 nil) 
}
```
》 FileInfo 用来描述一个文件对象

## func Stat
```go
func Atat(name string)(fi FileInfo, err error)
```
> Stat返回一个描述name指定的文件对象的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接指向的文件的信息，本函数会尝试跳转该链接。如果出错，返回的错误值为*PathError类型。

## func Lstat
```go
func Lstat(name string)(fi FileInfo, err error)
```
> Lstat返回一个描述name指定的文件对象的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接的信息，本函数不会试图跳转该链接。如果出错，返回的错误值为*PathError类型。

## func IsPathSeparator
```go
func IsPathSeparator(c uint8)bool
```
> IsPathSeparator 返回字符 c 是否是一个路径分隔符。

## func IsExist
```go
func IsExist(err error)bool
```
> 返回一个布尔值说明该错误是否表示一个文件或目录已经存在。ErrExist 和一些系统错误会使它返回真。

## func IsNotExist
```go
func IsNotExist(err error)bool
```
> 返回一个布尔值说明该错误是否表示一个文件或目录不存在。ErrNotExist 和一些系统调用错误会使它返回真。

## func IsPermission
```go
func IsPermission(err error)bool
```
> 返回一个布尔值说明该错误是否表示因权限不足而被拒绝。ErrPermission和一些系统调用错误会使它返回真。

## func Getwd
```go
func Getwd()(dir string, err error)
```
> Getwd 返回一个对应当前工作目录的根路径。如果当前目录可以经过多条路径抵达，Getwd 会返回其中一个。

## func Chdir
```go
Func Chdir(dir string)error
```
> Chdir 将当前工作目录修改为 dir 指定的目录。如果出错，会返回 *PathError 底层类型的错误。

## func Chmod
```go
func Chmod(name string, mode FileMode)error
```
> Chmod 修改 name 指定的文件对象 mode。如果 name 指定的文件是一个符号链接，它会修改该链接的目的地文件的 mode。如果出错，会返回 *PathError 底层类型的错误

## func Chown
```go
func Chown(name string, uid, gid int)error
```
> Chmod 修改 name 指定的文件对象的用户 id 和组 id。如果 name 指定的文件是一个符号链接，它会修改该链接的目的地文件的用户 id 和组 id。如果出错，会返回 *PathError 底层类型的错误

## func Lchown
```go
func Lchown(name, uid, gid int)error
```
> Chmod修改name指定的文件对象的用户id和组id。如果name指定的文件是一个符号链接，它会修改该符号链接自身的用户id和组id。如果出错，会返回*PathError底层类型的错误。

## func Chtimes
```go
func Chtimes(name string, atime time.Time, mtime timt.Time)error
```
> Chtimes 修改 name 指定的文件对象的访问时间和修改时间，类似 Unix 的 utime() 或 utimes() 函数。底层的文件系统可能会截断/舍入时间单位到更低的精确度。如果出错，会返回*PathError底层类型的错误。

## func Mkdir
```go
func Mkdir(name string, perm FileMode)error
```
> Mkdir 使用指定的权限和名称创建一个目录。如果出错，会返回 *PathError 底层类型的错误。

## func MkdirAll
```go
func MkdirAll(name string, perm FileMode)error
```
> MkdirAll 使用指定的权限和名称创建一个目录，包括任何必要的上级目录， 并返回 nil，否则返回错误。权限位 perm 会应用在每个被本函数创建的目录上。如果 path 指定了一个已经存在的目录， MkdirAll 不做任何操作并返回 nil

## func Rename
```go
func Rename(oldpath, newpath string)error
```
> Rename 修改一个文件的名字，移动一个文件。可能会有一些操作系统特定的限制。

## func Truncate
```go
func Truncate(name string, size int64)error
```
> Truncate 修改 name 指定的文件的大小。如果该文件为一个符号链接，将修改链接指向的文件的大小。如果出错，会返回 *PathError 底层类型的错误

## func Remove
```go
func Remove(name string)error
```
> Remove 删除 name 指定的文件或目录。如果出错，会返回 *PathError 底层类型的错误

## func RemoveAll
```go
func RemoveAll(path string)error
```
> RemoveAll 删除 path 指定的文件，或目录及它包含的任何下级对象。它会尝试删除所有东西，除非遇到错误并返回。如果 path 指定的对象不存在，RemoveAll 会返回 nil 而不返回错误。

## func Readlink
```go
func Readlink(name string)(string, error)
```
> Readlink 获取 name 指定的符号链接文件指向的文件的路径。如果出错，会返回*PathError底层类型的错误。

## func Symlink
```go
func Symlink(oldname, newname string)error
```
> Symlink 创建一个名为 newname 指向 oldname 的符号链接。如果出错，会返回 *LinkError 底层类型的错误

## func Link
```go
func Link(oldname, newname string)error
```
> Link 创建一个名为 newname 指向 oldname 的硬链接。如果出错，会返回 *LinkError 底层类型的错误。

## func SameFile
```go
func SameFile(fi1, fi2 FileInfo)bool
```
> SameFile 返回 fi1 和 fi2 是否在描述同一个文件。例如，在Unix这表示二者底层结构的设备和索引节点是相同的；在其他系统中可能是根据路径名确定的。SameFile应只使用本包Stat函数返回的FileInfo类型值为参数，其他情况下，它会返回假。

## func TempDir
```go
func TempDir()string
```
> TempDir 返回一个用于保管临时文件的默认目录。

## type File 
```go
type File struct {
	//
}
```
> File 代表一个打开的文件对象

## func Create
```go
func Create(name string)(file *File, err error)
```
> Create 采用模式 0666 任何人都可读写，不可执行）创建一个名为name的文件，如果文件已存在会截断它（为空文件）。如果成功，返回的文件对象可用于I/O；对应的文件描述符具有O_RDWR模式。如果出错，错误底层类型是*PathError。

## func Open
```go
func Open(name string)(file *File, err error)
```
> Open 打开一个文件用于读取。如果操作成功，返回的文件对象的方法可用于读取数据；对应的文件描述符具有O_RDONLY模式。如果出错，错误底层类型是*PathError。

## func OpenFile
```go
func OpenFile(name string, flag int, perm FileMode)(file *File, err error)
```
> OpenFile 是一个更一般性的文件打开函数，大多数调用者都应用 Open 或 Create 代替本函数。它会使用指定的选项（如O_RDONLY等）、指定的模式（如0666等）打开指定名称的文件。如果操作成功，返回的文件对象可用于 I/O。如果出错，错误底层类型是 *PathError。

## func NewFile
```go
func NewFile(fd uintptr, name string)*File
```
> NewFile 使用给出的 Unix 文件描述符和名称穿件一个文件。

## func Pipe
```go
func Pipe(r *File, w *File, err error)
```
> Pipe 返回一对关联的文件对象。从r的读取将返回写入w的数据。本函数会返回两个文件对象和可能的错误。

## func (*File)Name
```go
func (f *File)Name()string
```
> Name 方法返回（提供给 Open/Create 等方法的）文件名称。

## func (*File)Stat
```go
func (f *File)Stat()(fi FileInfo, err error)
```
> Stat 返回描述文件 f 的 FileInfo 类型值。如果出错，错误底层类型是*PathError。

## func (*File)Fd 
```go
func (f *File)Fd()uintptr
```
> Fd 返回与文件 f 对应的整数类型的 Unix 文件描述符

## func (*File)Chdir
```go
func (f *File)Chdir()error
```
> Chdir 将当前工作目录修改为 f，f必须是一个目录。如果出错，错误底层类型是 *PathError。

## func (*File)Chmod
```go
func (f *File)Chmod(mode FileMode)error
```
> Chmod 修改文件的模式。如果出错，错误底层类型是*PathError。

## func (*File)Chown
```go
func (f *File)Chown(uid, gid int)error
```
> Chown 修改文件的用户ID组ID。如果出错，错误底层类型是*PathError。

## func (*File)Readdir
```go
func (f *File)Readdir(n int)(fi []FileInfo, err error)
```
> Readdir 读取目录 f 的内容，返回一个有 n 个成员的 []FileInfo，这些 FileInfo 是被 Lstat 返回的，采用目录顺序。对本函数的下一次调用会返回上一次调用剩余未读取的内容信息。
>
> 如果n>0，Readdir函数会返回一个最多n个成员的切片。这时，如果Readdir返回一个空切片，它会返回一个非nil的错误说明原因。如果到达了目录f的结尾，返回值err会是io.EOF。
>
> 如果n<=0，Readdir函数返回目录中剩余所有文件对象的FileInfo构成的切片。此时，如果Readdir调用成功（读取所有内容直到结尾），它会返回该切片和nil的错误值。如果在到达结尾前遇到错误，会返回之前成功读取的FileInfo构成的切片和该错误。

## func (*File)Readdirnames
```go
func (f *File)Readdirnames(n int)(name []string, err error)
```
> Readdir读取目录f的内容，返回一个有n个成员的[]string，切片成员为目录中文件对象的名字，采用目录顺序。对本函数的下一次调用会返回上一次调用剩余未读取的内容的信息。
>
> 如果n>0，Readdir函数会返回一个最多n个成员的切片。这时，如果Readdir返回一个空切片，它会返回一个非nil的错误说明原因。如果到达了目录f的结尾，返回值err会是io.EOF。
>
> 如果n<=0，Readdir函数返回目录中剩余所有文件对象的名字构成的切片。此时，如果Readdir调用成功（读取所有内容直到结尾），它会返回该切片和nil的错误值。如果在到达结尾前遇到错误，会返回之前成功读取的名字构成的切片和该错误。

## func (*File)Truncate
```go
func (f *File)Truncate(size int64)error
```
> Truncate 改变文件的大小，它不会改变 I/O 的当前位置。如果截断文件，多出的部分就会被丢弃。如果出错，错误底层类型是*PathError。

## func (*File)Read 
```go
func (f *File)Read(b []byte)(n int, err error)
```
> Read 方法从 f 中读取最多 len(b) 字节数据并写入 b。返回读取的字节数和可能遇到的任何错误。文件终止标志是读取 0 个字节且返回值 err 为 io.EOF。

## func (*File)ReadAt
```go
func (f *File)ReadAt(b []byte, off int64)(n int, err error)
```
> ReadAt 从指定的位置（相对于文件开始位置）读取 len(b) 字节数据并写入b。它返回读取的字节数和可能遇到的任何错误。当 n<len(b)时，本方法总是会返回错误；如果是因为到达文件结尾，返回值err会是io.EOF。

## func (*File)Write
```go
func (f *File)Write(b []byte)(n int, err error)
```
> Write 向文件中写入 len(b) 字节数据。它返回写入的字节数和可能遇到的任何错误。吐过返回值 n!=len(b)，本方法会返回一个非 nil 的错误。

## func (*File)WriteString
```go
func (f *File)WriteString(s string)(ret int, err error)
```
> WriteString 类似 Write,但接受一个字符串参数。

## func (*File)WriteAt
```go
func (f *File)WriteAt(b []byte, off int64)(n int, err error)
```
> WriteAt在指定的位置（相对于文件开始位置）写入 len(b) 字节数据。它返回写入的字节数和可能遇到的任何错误。如果返回值 n!=len(b)，本方法会返回一个非nil的错误。

## func (*File)Seek 
```go
func (f *File)Seek(offset int64, whence int)(ret int64, err error)
```
> Seek 设置下一次读/写的位置。offset 为相对偏移量，而whence 决定相对的位置：0为相对文件开头，1为相对当前位置，2为相对文件结尾。它返回新的偏移量（相对开头）和可能的错误。

## func (*File)Sync
```go
func (f *File)Sync()(err error)
```
> Sync 递交文件的当前内容进行稳定的存储。一般来说，这表示将文件系统的最近写入的数据在内存中的拷贝刷新到硬盘中稳定保存。

## func (*File)Close
```go
func (f *File)Close()error
```
> Close 关闭文件 f，使文件不能用于读写。它返回可能出现的错误

## type ProcAttr
```go
type ProcAttr struct {
	// 如果 Dir 非空，子进程会在创建进程前先进入该目录。(即设为当前工作目录)
	Dir string
	// 如果 Env 非空，它会作为新进程的环境变量。必须采用 Environ 返回值得格式
	// 如果 Env 为空字符串，将使用 Environ 函数的返回值
	Env []string
	// Files 指定被新进程继承的活动文件对象
	// 前三个绑定为标准输入、标准输出、标准错误输出
	// 依赖底层操作系统的实现可能会支持额外的数据出入途径
	// nil 条目相当于在进程开始时关闭的文件对象
	Files []*File
	// 操作系统特定的创建属性
	// 注意何止本字段意味着程序可能会运作失常设置在某些操作系统中无法通过编译
	Sys *syscall.SysProcAttr
}
```
> ProcAttr保管将被StartProcess函数用于一个新进程的属性。

## type Proess
```go
type Process struct {
	Pid int
	//
}
```
> Process 保管一个被 StarProcess 创建的进程的信息。

## func FindProcess
```go
func FindProcess(pid int)(p *Process, err error)
```
> FindProcess 根据进程 id 查找一个运行中的进程。函数返回的进程对象可以用于获取其关于底层操作系统进程的信息。

## func StartProcess
```go
func StartProcess(name string, argv []string, attr *ProcAttr)(*Process, error)
```
> StartProcess 使用提供的属性、程序名、命令行参数开始一个新的进程。StartProcess 函数是一个低水平的接口。os/exec 提供了高水平的接口，应该尽量使用该包。如果出错，错误的底层类型会是*PathError。

## func (*Process)Signal
```go
func (p *Process)Signal(sig Signal)error
```
> Signal 方法向进程发送一个信号。在windows中向进程发送Interrupt信号尚未实现。

## func (*Process)Kill
```go
func (p *Process)Kill()error
```
> Kill 让进程立刻退出

## func (*Process)Wait
```go
func (p *Process)Wait()(*ProcessState, error)
```
> Wait 阻塞知道进程退出，然后返回一个描述 ProcessState 描述进程的状态和可能的错误。Wait 方法会释放绑定到进程 p 的所有资源。在大多数操作系统中，进程 p 必须是当前进程的子进程，否则会返回错误。

## func (*Process)Release
```go
func (p *Process)Release()error
```
> Release 释放进程 p 绑定的所有资源，使它们(资源)不能再被(进程 p)使用。只有没有调用 Wait 方法时才需要调用本方法

## type ProcessState
```go
type ProcessState struct {
	//
}
```
> ProcessState 保管 Wait 函数报告的某个已退出进程的信息

## func (*ProcessState)Pid
```go
func (p *ProcessState)Pid()int
```
> Pid 返回一个已退出的进程的进程 id

## func (*ProcessState)Exited
```go
func (p *ProcessState)Exited()bool
```
> Exited 报告进程是否已退出

## func (*ProcessState)Success
```go
func (p *ProcessState)Success()bool
```
> Success报告进程是否成功退出，如在 Unix 里以转台吗 0 退出

## func (*ProcessState)SysteTime
```go
func (p *ProcessState)SystemTime()time.Duration
```
> SystemTime 返回已退出进程及子进程耗费的系统 CPU 时间

## func (*ProcessState)UserTime
```go
func (p *ProcessState)UserTime()time.Duration
```
> UserTime 返回已退出进程及其子进程耗费的用户 CPU 时间

## func (*ProcessState)Sys
```go
func (p *ProcessState)Sys()interface{}
```
> Sys返回该已退出进程系统特定的退出信息。需要将其类型转换为适当的底层类型，如Unix里转换为*syscall.WaitStatus类型以获取其内容。

## func (*ProcessState)SysUsage
```go
func (p *ProcessState)SysUsage()interface{}
```
> SysUsage 返回该已退出进程系统特定的资源使用信息。需要将其类型转换为适当的底层类型，如 Unix 里转换为 *syscall.Rusage 类型以获取其内容。

## func (*ProcessState)String
```go
func (p *ProcessState)String()string
```