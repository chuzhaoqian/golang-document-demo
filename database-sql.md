# package sql
```go
import "database/sql"
```
> sql 提供了保证 SQL 或类 SQL数据库的泛用接口。
>
> 使用 sql 包时必须注入(至少)一个数据库驱动。https://github.com/go-sql-driver/mysql 支持database/sql，全部采用go写。后面使用的例子也会使用此包。
>
> go get -u github.com/go-sql-driver/mysql 安装这个驱动包

```go
var ErrNoRows = errors.New("sql: no rows in result ser")
```
> 当 QueryRow 方法没有返回一个 row 时，调用返回值的 Scan 方法会返回 ErrNoRows。此时， QueryRow 返回一个占位的 *Row 值，延迟本错误知道调用 Scan 方法时才释放。

## func Register
```go
func Register(name string, driver driver.Driver)
```
> Register 注册并命名一个数据库，可以在 Open 函数中使用该命名启用该驱动。如果 Register 注册同一名称两次，或者 driver 参数为 nil，会导致 panic。

## type Scanner
```go
type Scanner interface{
	// Scan 方法从数据驱动获取一个值。
	// 参数 src 的类型保证为如下类型之一：
	// int64 float64 bool []byte string time.Time nil(表示NULL)
	// 如果不能不丢失信息的保存一个值，应返回错误。
	Scan(src interface{})error
}
```
> Scanner 接口会被 Rows 或 Row 的 Scan 方法使用。

## type NullBool
```go
type NullBool struct{
	Bool bool
	Valid bool // 如果 Bool 不是 NULL 则 valid 为真
}
```
> NullBool 代表一个可为 NULL 的布尔值。NullBool 实现了 Scanner 接口，因此可以作为 Rows/Row 的 Scan 方法的参数保存扫描结果，类似 NullString。

## func (*NullBool)Scan
```go
func (n *NullBool)Scan(value interface{}error
```
> Scan 实现了 Scanner 接口。

## func (NullBool)Value
```go
func (n NullBool)Value()(driver.Value, error)
```
> Value 实现了 driver.Valuer 接口。

## type NullInt64
```go
type NullInt64 struct{
	Int64 int64
	Valid bool // 如果 Int64 不是 NULL 则 Valid 为真
}
```
> NullInt64 代表一个可为 NULL 的 int64 值。NullInt64 实现了 Scanner 接口，因此可以作为 Rows/Row 的 Scan 方法的参数保存扫描结果，类似 NullString。

## func (*NullInt64)Scan
```go
func (n *NullInt64)Scan(Value interface{})error
```
> Scan 实现了 Scanner 接口。

## func (NullInt64)Value
```go
func (n NullInt64)Value()(driver.Value, error)
```
> Value 实现了 driver.Value 接口。

## type NullFloat64
```go
type NullFloat64 struct{
	Float64 float64
	Valid bool // 如果 Float64 不是 NULL 则 Valid 为真
}
```
> NullFloat64 代表一个可以 NULL 的 float64 值。NullFloat64 实现了 Scanner 接口，因此可以作为 Rows/Row 的 Scan 方法的参数保存扫描结果，类似 NullString。

## func (*NullFloat64)Scan
```go
func (n *NullFloat64)Scan(value interface{})error
```
> Scan 实现了 Scanner 接口。

## func (NullFloat64)Value
```go
func (n NullFloat64)Value()(driver.Value, error)
```
> Value 实现了 driver.Valuer 接口。

## type NullString
```go
type NullString struct{
	String string
	Valid bool // 如果 String 不是 NULL 则 Valid 为真
}
```
> NullString 代表一个可谓 NULL 的字符串。NullString 实现了 Scanner 接口，因此可以作为 Rows/Row 的 Scan 方法的参数保存扫描结果。

## func (*NullString)Scan
```go
func (ns *NullString)Scan(value interface{})error
```

## func (NullString)Value
```go
func (ns NullString)Value()(driver.Value, error)
```

## type RawBytes
```go
type RawBytes []byte
```
> RawBytes 是一个字节切片，保管对内存的引用，为数据库自身所使用。在 Scanner 接口的 Scan 方法写入 RawBytes 数据后，该切片只在有限次调用 Next,Scan 或 Close 犯法之前合法。

## type Result
```go
type Result interface{
	// LastInsertId 返回一个数据库生成的回应命令的整数。
	// 当插入新行时，一般来自一个“自增”列
	// 不是所有的数据库都支持该功能，该状态的语法也各有不同。
	LastInsertId()(int64,error)
	
	// RowsAffected 返回被 update，indert 或 delete 命令影响的行数。
	// 不是所有的数据口都支持该功能
	RowsAffected()(int64, error)
}
```
> Result 是对已执行的 SQL 命令的总结。

## type DB
```go
type DB strct{
	//
}
```
> DB 是一个数据操作句柄，代表一个具有零到多个底层连接的连接池。它可以安全的被多个 go 程同时使用。
>
> sql 包会自动创建和释放连接；它也会维护一个闲置连接的连接池。如果数据库具有单连接状态的概念，该状态只有在事务中被观察时才可信。一旦调用了 DB.Begin,返回的 Tx 会绑定到单个连接。当调用事务 Tx 的 Commit 或 Rollback 后，该事务使用的连接会归还到 DB 的闲置连接池中。连接池的大小可以用 setMaxIdleConns 方法控制。

## func Open
```go
func Open(driverName, dataSourceName string)(*DB, error)
```
> Open 打开一个 dirverName 指定的数据库，dataSourceName 指定数据源，一般包至少包括数据库文件命和连接信息。
>
> Open 函数可能只是验证其参数，而不创建与数据库的连接。如果要检查数据源的名称是否合法，应该嗲用返回值的 Ping 方法。
>
> 返回的 DB 可以安全的被多个 go 程同时使用，并维护滋生的闲置连接池。这样一来，Open 函数只需调用一次。很少需要关闭 DB。

## func (*DB)Driver
```go
func (db *DB)Driver()driver.Driver
```
> Driver 方法返回数据库下层驱动。

## func (*DB)Ping
```go
func (db *DB)Ping()error
```
> Ping 检查与数据库的连接是否有效，如果需要会创建连接。

### Example
```go
package main

import (
	"database/sql"
	"fmt"

	_ "github.com/go-sql-driver/mysql"
)

func main() {
	db, err := sql.Open("mysql", "root:Lawuyou@tcp(localhost:8889)/test?charset=utf8")
	fmt.Println(err) // 为什么总是 nil
	dbErr := db.Ping()
	if dbErr != nil {
		fmt.Println(dbErr)
		panic(dbErr)
	}
}
```

## func (*DB)Close
```go
func (db *DB)Close()error
```
> Close 关闭数据库，释放任何打开的资源。一般不会关闭 DB,因为 DB 句柄通常被多个 go 程共享，并长期活跃。

## func (*DB)SetMaxOpenConns
```go
func (db *DB)SetMaxOpenConns(n int)
```
> SetMaxOpenConns 设置与数据库建立连接的最大数据。默认无限制。

## func (*DB)SetMaxIdleConns(n int)
```go
func (db *DB)SetMaxIdleConns(n int)
```
> SetMaxIdleConns 设置连接池中的最大闲置连接数。

## func (*DB)Exec
```go
func (db *DB)Exec(query string, args ...interface{})(Result, error)
```
> Exec 执行一次命令(查询，删除，更新，插入等)，不会返回任何执行结果。参数 args 表示 query 中的占位参数。

## func (*DB)Query
```go
func (db *DB)Query(query string, args ...interface{})(*Rows, error)
```
> Query 执行一次查询，返回多行结果(Rows)，一般用于执行 select 命令。参数 args 表示 query 中的占位参数。

## func (*DB)QueryRow
```go
func (db *DB)QueryRow(query string, args ...interface{})*Row
```
> QueryRow 执行一次查询，并期望返回最多一行数据(Row)。QueryRow 总是返回非 nil 的值，直到返回值的 Scan 方法被调用时，才会返回被延迟的错误。

## func (*DB)Prepare
```go
func (db *DB)Prepare(query string)(*Stmt, error)
```
> Prepare 创建一个准备好的状态用于之后的查询和命令。返回值可以同时执行多个查询和命令。

## func (*DB)Begin
```go
func (db *DB)Begin()(*Tx, error)
```
> Begin 开始一个事务。隔离水平由数据库驱动决定。

## type Row
```go
type Row struct{
	//
}
```
> QueryRow 方法返回 Row,代表单行查询结果。

## func (*Row)Scan
```go
func (r *Row)Scan(dest ...interface{})error
```
> Scan 将改行查询结果各列分别保存进 dest 参数指定的值中。如果该查询匹配多行，Scan 会使用第一行结果并丢弃其余各行。如果没有匹配查询行，Scan 会返回 ErrNoRows。

## type Rows
```go
type Rows struct{
	
}
```
> Rows 是查询的结果。它的游标指向结果集的第零行，使用 Next 方法来遍历各行结果

## func (*Rows)Columns
```go
func (rs *Rows)Columns()([]string, error)
```
> Columns 返回列名。如果 Rows 已经关闭会返回错误。

## func (*Rows)Scan
```go
func (rs *Rows)Scan(dest ...interface{})error
```
> Scan 将当前行各列结果填充进 dest 指定的各个值中。
>
> 如果某个参数的类型为 *[]byte,Scan 会保存对应数据的拷贝，该拷贝为调用者所有，可安全的修改或无限期的保存。如果参数类型为 *RawBytes 可以避免拷贝。
>
> 如果某个参数的类型为 *interface{}，Scan 会不做转换的拷贝底层驱动提供的值。如果值的类型为[]byte，会进行数据的拷贝，调用者可以安全使用该值。

## func (*Rows)Next
```go
func (rs *Rows)Next()bool
```
> Next 准备用于 Scan 方法的下一行结果。如果成功会返回真，如果没有下一行或出现错误会返回假。Err 应该被调用以区分这两种情况。每次调用 Scan 方法，甚至包括第一次调用该方法，都必须在前面先调用 Next 方法。

## func Close
```go
func (rs *Rows)Close()error
```
> Close 关闭 Rows,阻止对其更多列举。如果 Next 方法返回假，Rows 会自动关闭，满足。检查 Err 方法结果的条件。多次调用无效，不会影响 Err 方法的结果。

## func (*Rows)Err
```go
func (rs *Rows)Err()error
```
> Err 返回可能的在迭代时出现的错误。Err 需要在显示或隐式调用 Close 方法后调用。

## type Stmt
```go
type Stmt struct{
	
}
```
> Stmt 是准备好的状态。Stmt 可以安全的被多个go程同时使用。

## func (*Stmt)Exec
```go
func (s *Stmt)Exec(args ...interface{})(Result, error)
```
> Exec 使用提供的参数执行准备好的命令状态，返回 Restul 类型的该状态执行结果。

## func (*Stmt)Query
```go
func (s *Stmt)Query(args ...interface{})(*Rows, error)
```
> Query 使用提供的参数执行准备好的查询状态，返回 Rows 类型查询结果。

## func (*Stmt)QueryRow
```go
func (s *Stmt)QueryRow(	args ...interface{})*Row
```
> QueryRow 使用提供的参数执行准备好的查询状态。如果在执行时遇到了错误，该错误会被延迟，知道返回值的 Scan 方法被调用时菜释放。返回值总是非 nil 的。如果没有查询到结果， *Row 类型返回值的 Scan 方法会返回 ErrNoRows;否则 Scan 方法会扫描结果第一行并对其其余行。

## func (*Stmt)Close
```go
func (s *Stmt)Close()error
```
> Close 关闭状态。

## type Tx
```go
type Tx struct{
	//
}
```
> Tx 代表一个进行中的数据库事务。一次事务必须对 Commit 或 Rollback 的调用结束。调用 Commit 或 Rollback 后，所有对事务的操作都会失败并返回错误值 ErrTxDone。

## func (*Tx)Exec
```go
func (tx *Tx)Exec(query string, args ...interface{})(Result, error)
```
> Exec 执行命令，但不会返回结果。

## func (*Tx)Query
```go
func (tx *Tx)Query(query string, args ...interface{})(*Rows, error)
```
> Query 执行查询并返回零到多行结果(Rows)。

## func (*Tx)QueryRow
```go
func (*Tx)QueryRow(query string, args ...interface{})*Row
```
> QueryRow 执行查询并期望返回最多一行结果(Row)。QueryRow 总是返回非 nil 结果，查询失败的错误会延迟到调用该结果的 Scan 方法时释放。

## func (*Tx)Prepare
```go
func (tx *Tx)Prepare(query string)(*Stmt, error)
```
> Prepare 准备一个专用于该事务的状态。返回的该事务专属状态操作在 Tx 递交会回滚后不能再使用。要在该事务中使用已存在的状态。

## func (*Tx)Stmt
```go
func (tx *Tx)Prepare(*Stmt)*Stmt
```
> Stmt 使用已存在的状态生成一个该事务特定的状态。

## func (*Tx)Commit
```go
func (tx *Tx)Commit()error
```
> Commit 递交事务。

## func (*Tx)Rollbace
```go
func (tx *Tx)Rollback()error
```
> Rollback 放弃并回滚事务。

### Example
```go
package main

import (
	"database/sql"
	"fmt"

	_ "github.com/go-sql-driver/mysql"
)

func main() {
	db, err := sql.Open("mysql", "root:Lawuyou@tcp(localhost:8889)/test?charset=utf8")
	fmt.Println(err) // 为什么总是 nil
	dbErr := db.Ping()
	if dbErr != nil {
		//		fmt.Println(dbErr)
		panic(dbErr)
	}

	//	CREATE TABLE `userinfo` (
	//		`uid` INT(10) NOT NULL AUTO_INCREMENT,
	//		`username` VARCHAR(64) NULL DEFAULT NULL,
	//		`departname` VARCHAR(64) NULL DEFAULT NULL,
	//		`created` DATE NULL DEFAULT NULL,
	//		PRIMARY KEY (`uid`)
	//	);

	insterSql := "insert userinfo set username=?,departname=?,created=?"
	stmt, prepareErr := db.Prepare(insterSql)
	if prepareErr != nil {
		panic(prepareErr)
	}

	res, execErr := stmt.Exec("czq", "研发部门", "2018-01-23")
	if execErr != nil {
		panic(execErr)
	}

	id, resErr := res.LastInsertId()
	if resErr != nil {
		panic(resErr)
	}
	fmt.Println("insert id :", id)

	updateSql := "update userinfo set username=? where uid=?"
	stmt, updateErr := db.Prepare(updateSql)
	if updateErr != nil {
		panic(updateErr)
	}

	resUp, execUpErr := stmt.Exec("czq", id)

	if execUpErr != nil {
		panic(execUpErr)
	}
	affect, affectErr := resUp.RowsAffected()
	fmt.Println("影响", affect, affectErr)

	rows, queryErr := db.Query("select * from userinfo")
	if queryErr != nil {
		panic(queryErr)
	}

	for rows.Next() {
		var uid int
		var username string
		var department string
		var created string
		// 这里不能缺少参宿 或者返回错误
		//		scanErr := rows.Scan(&uid)
		scanErr := rows.Scan(&uid, &username, &department, &created)
		if scanErr != nil {
			panic(scanErr)
		}
		fmt.Println(uid)
	}

	delSql := "delete from userinfo where uid=?"
	stmtDel, errDel := db.Prepare(delSql)
	if errDel != nil {
		panic(errDel)
	}

	resDel, execDelErr := stmtDel.Exec(id)
	if execDelErr != nil {
		panic(execDelErr)
	}

	affectDel, affetDelErr := resDel.RowsAffected()
	if affetDelErr != nil {
		panic(affetDelErr)
	}
	fmt.Println("删除", affectDel)

	db.Close()
}
```