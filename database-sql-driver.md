# package driver
```go
import "database/sql/driver"
```
> driver 定义了硬背数据库驱动实现的接口，这些接口会被 sql 包使用。
>
> 定义小目标，随后自己写个 driver 2018-01-19

## type Value
```go
type Value interface{}
```
> Value 是驱动必须能处理的值。他要么是 nil,要么是如下类型的实例：

```go
int64
float64
bool
[]byte
string [*]Rows.Next 不会返回该值类型
time.Time
```

## type Valuer
```go
type Valuer interface{
	// Value 返回一个驱动支持的 Value 类型的值
	Value()(Value, error)
}
```
> Valuer 是提供 Value 方法的接口。实现了 Valuer 接口的类型可以将自身转换为驱动支持的 Value 类型值。

## func IsValue
```go
func IsValue(v interface{})bool
```
> IsValue 报告 v 是否是合法的 Value 类型参数。和 IsScanValue 不同，IsValue 接受字符串类型。

## func IsScanValue
```go
func IsScanValue(v interface())bool
```
> IsScanValue 报告 v 是否是合法的 Value 扫描类型参数。和 IsValue 不同，IsScanValue 不接受字符串类型。

## type ValueConverter
```go
type valueConverter interface{
	// Converter 将一个值转换为驱动支持的 Value 类型
	ConverValue(v interface{})(Value, error)
}
```
> Valueconverter 接口提供了 Converter 方法。
>
> dirver 提供了各种 valueConverter 接口的实现，以保证不同驱动之前的实现和转换的一致性。ValueConverter 接口有如下用途：

```go
* 转换sql包提供的Value类型值到数据库指定列的类型，并保证它的匹配，例如保证某个int64值满足一个表的uint16列。
* 转换数据库提供的值到驱动的Value类型。
* 在扫描时被sql包用于将驱动的Value类型转换为用户的类型。
```

## type ColumnConverter
```go
type ColumnConverter interface{
	// ColumnConverter 返回指定列的 Valueconverter
	// 如果该列未指定类型，或不应特殊处理，应返回 DefaultValueConverter
	ColumnConverter(idx int)ValueConverter
}
```
> 如果 Stmt 有自己的列类型，可以实现 Columnconverter 接口，返回值可以将任意类型转换为驱动的 Value 类型。

## type NotNull
```go
type NotNull struct{
	Converter ValueConverter
}
```
> NotNull 实现了 ValueConverter 接口， 不允许 nil 值，否则会将值交给 Converter 字段处理。

## func (NotNull)ConvertValue
```go
func (n NotNull)ConvertValue(v interface{})(Value, error)
```

## type Null
```go
type Null Struct{
	Converter ValueConverter
}
```
> Null 实现了 ValueConverter 接口，允许 nil 值，否则会将值交给 Converter 字段处理。

## func (Null)ConvertValue
```go
func (n Null)ConvertValue(v interface{})(Value, error)
```

## type Driver
```go
type Driver interface{
	// Open 返回一个心得鱼数据库的连接，参数 name 的格式是驱动特定的。
	// Open 可能返回一个缓存的连接(之前关闭的连接)，但这么做是不必要的；
	// sql 包会维护闲置连接池以便有效的重用连接。
	// 返回的连接同一时间智慧被一个 go 程使用。
	Open(name string)(Conn, error)
}
```
> Driver 接口必须被数据库驱动实现。

## type Conn
```go
type Conn interface{
	// Prepre 返回一个准备好的、绑定到该状态的状态。
	Prepare(query string)(Stmt, error)
	
	// Close 作废并停止任何现在准备好的状态和事务，将该连接标注为不再使用。
	// 因为 sql 保护着一个连接池，只有当闲置连接过剩时才会调用 Close 方法。
	// 驱动的实现中不需要添加自己的连接缓冲池。
	Close() error
	
	// Begin 开始并返回一个新的事务。
	Begin()(Tx, error)
}
```
> Conn 是与数据库的连接。该连接不会被多线程并行使用。连接被假定为具有状态的。

## type Execer
```go
type Execer interface{
	Exec(query string, args []Value)(Result, error)
}
```
> Execer 是一个可选的、可能被 Conn 接口实现的接口。
>
> 如果一个 Conn 未实现 Execer 接口，sql 包的 DB.Exec 会首先准备一个查询，执行状态，然后关闭状态。Exec 可能会返回 ErrSkip。

## type Queryer
```go
type Queryer interface{
	Query(query string, args []Value)(Rows, error)
}
```
> Queryer 是一个可选的、可能被 Conn 接口实现的接口。
>
> 如果一个 Conn 未实现 Queryer 接口，sql 包的 DB.Query 会首先准备一个查询，执行状态，然后关闭状态。Query 可能会返回 ErrSkip。

## type Stmt
```go
type Stmt interface{
	// Close 关闭 Stmt
	// 如果 Stmt 被任何查询使用中的换，将不会被关闭。
	Closer()error
	
	// NumInput 返回占位参数的个数。
	// 如果 NumInput 返回值 >= 0，sql 包会提前检查调用者提供的参数个数，
	// 并且会在调用 Exec 或 Query 方法千返回数目不对的错误。
	// NumInput 可以返回 -1，如驱动占位参数的数量。
	// 此时 sql 包不会提前检查参数个数。
	NumInput()int
	
	// Exec 执行查询，而不会返回结果，如 insert 或 update。
	Exec(args []Value)(Result, error)
	
	// Query执行查询并返回结果，如 select。
	Query(args []Value)(Rows, error)
}
```
> Stmt 是准备好的状态。它会绑定到一个连接，不应该被多 go 程 同时使用。

## type Tx
```go
type Tx interface{
	Commit()error
	Rollbace()error
}
```
> Tx 是一次事务。

## type Result
```go
type Result interface{
	// LastInsertId 返回 insert 等命令后数据库自动生成的 ID
	LastInsertId()(int64, error)
	
	// RowsAffected 返回被查询影响的行数
	RowsAffected()(int64, error)
}
```
> Result 是查询执行的结果。