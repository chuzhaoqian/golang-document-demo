# package exec
```go
import "os/exec"
```
> exec 执行外部命令。包装了 os.StartProcess 函数以便更容易的修正输入和输出，使用管道连接 I/O，以及作其它的一些调整。

```go
var ErrNotFound = errors.New("executable file not found in $PATH")
```
> 如果路径搜索没有找到可执行文件时，就会返回本错误

## type Error
```go
type Error struct {
	Name string
	Err error 
}
```
> Error 类型记录执行失败的程序名和失败的原因

## func (*Error)Error
```go
func (e *Error)Error()string
```

## type ExitError
```go
type ExitError struct {
	*os.ProcessState
}
```
> ExitError 包裹某个命令的一次未成功的返回

## func (*ExitError)Error
```go
func (e *ExitError)Error()string
```

## func LookPath
```go
func LookPath(file string)(string, error)
```
> 在环境变量 PATH 指定的目录中搜索可执行的文件，如file中有斜杠，则只在当前目录搜索。返回完整路径或者相对于当前目录的一个相对路径。


## Example
```go
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	lspath, err := exec.LookPath("go")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(lspath)
}
```

## type Cmd
```go
type Cmd struct {
    // Path 是将要执行的命令的路径
    // 该字段不能为空，如为相对路径会相对 Dir 字段
    Path string
    // Arag 保管命令的参数，包括命令名作为第一个参数；如果为空切片或nil，相当于无参数命令
    Args []string
    // Enc 指定进程的环境，如为 nil，则是在当前进程的环境下执行
    Env []string
    // Dir 指定命令的工作目录。如为空字符串，会在调用者的进程当前目录下执行
    Dir string
    // Stdin指定进程的标准输入，如为nil，进程会从空设备读取（os.DevNull）
    Stdin io.Reader
    // Sedout 和 Stderr 指定进程的标准输出和标准错误输出
    // 如果任一个为nil，Run方法会将对应的文件描述符关联到空设备（os.DevNull）
    // 如果两个字段相同，同一时间最多有一个线程可以写入。
    Stdout io.Writer
    Stderr io.Writer
    // ExtraFiles指定额外被新进程继承的已打开文件流，不包括标准输入、标准输出、标准错误输出。
    ExtraFiles []*os.File
    // SysProcAttr保管可选的、各操作系统特定的sys执行属性。
    // Run方法会将它作为os.ProcAttr的Sys字段传递给os.StartProcess函数。
    SysProcAttr *syscall.SysProcAttr
    // Process是底层的，只执行一次的进程。
    Process *os.Process
    // ProcessState包含一个已经存在的进程的信息，只有在调用Wait或Run后才可用。
    ProcessState *os.ProcessState
    // 
}
```
> Cmd 代表一个正在准备或在执行中的外部命令

## func Command
```go
func Command(name string, arg ...string)*Cmd
```
> 返回一个 *Cmd,用于使用给出的参数执行 name 指定的程序。返回值只设定了 Path 和 Args 两个参数。如果 name 不含路径分隔符，将使用 LookPath 获取完整路径；否则直接使用 name。参数 arg 不应保航命令名。

## func (*Cmd)StdinPipe
```go
func (c *Cmd)StdinPipe()(io.WriteCloser, error)
```
> StdinPipe 方法返回一个命令 Start 后与命令标准输入关联的管道。Wait 方法货值命令结果后悔关闭这个管道。必要时调用者可以调用 Close 方法来强行关闭管道，例如命令在输入关闭后才会执行返回时需要显示关闭管道。

## func (*Cmd)StdoutPipe
```go
func (c *Cmd)StdoutPipe()(io.Readcloser, error)
```
> StdoutPipe 方法返回一个在命令 Start 后与命令标准输出关联的管道。Wait 方法获知命令结束后会关闭这个管道，一般不需要显式的关闭该管道。但是在从管道读取完全部数据之前调用 Wait 是错误的；同样使用 StdoutPipe 方法时调用 Run 函数也是错误的。

## func (*Cmd)StderrPipe
```go
func (c *Cmd)StderrPipe()(io.ReadCloser, error)
```
> StderrPipe方法返回一个在命令Start后与命令标准错误输出关联的管道。Wait方法获知命令结束后会关闭这个管道，一般不需要显式的关闭该管道。但是在从管道读取完全部数据之前调用Wait是错误的；同样使用StderrPipe方法时调用Run函数也是错误的。请参照StdoutPipe的例子。

## func (*Cmd)Run
```go
func (c *Cmd)Run()error
```
> Run 执行 c 保航的命令，并祖册知道完成。如果命令成功执行，stdin、stdout、stderr的转交没有问题，并且返回状态码为0，方法的返回值为nil；如果命令没有执行或者执行失败，会返回 *ExitError 类型的错误；否则返回的 error 可能是表示 I/O 问题。

## func (*Cmd)Start
```go
func (c *Cmd)Start()error
```
> Start 开始执行 c 包含的命令，但并不会等待该命令完成即返回。Wait 方法会返回命令的返回状态码并在命令返回后释放相关的资源

## func (*Cmd)Wait
```go
func (c *Cmd)Wait()error
```
> Wait 会阻塞直到该命令执行完成，该命令必须是被 Start 方法开始执行的。如果命令成功执行，stdin、stdout、stderr的转交没有问题，并且返回状态码为0，方法的返回值为nil；如果命令没有执行或者执行失败，会返回*ExitError类型的错误；否则返回的error可能是表示I/O问题。Wait方法会在命令返回后释放相关的资源

## func (*Cmd)Output
```go
func (c *Cmd)Output()([]byte, error)
```
> 执行命令并返回标准输出的切片。

## func (*Cmd)CombinedOutput
```go
func (c *Cmd) CombinedOutput() ([]byte, error)
```
> 执行命令并返回标准输出和错误输出合并的切片。