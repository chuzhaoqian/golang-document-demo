# package sort
```go
import "sort"
```
> sort 提供了排序切片和用户自定义数据集的函数

## type Interface
```go
type Interface interface {
	// Len 方法返回集合中的元素个数
	Len() int 
	// Less 方法报告索引 i 的元素是否比索引 j 的元素小
	Less(i, j int)bool
	// Swap 方法交换索引 i 和 j 的两个元素
	Swap(i, j int)
}
```
> 一个满足 sort.Interface 接口的（集合）类型可以被本包的函数进行排序。方法要求集合中的元素可以被整数索引。

## type IntSlice
```go
type Intslice []int
```
> IntSlice 给 []int 添加方法以满足 Interface 接口，以便排序为递增序列

## func (IntSlice)Len
```go
func (p IntSlice)Len()int
```

## func (IntSlice)Less
```go
func (p IntSlice)Less(i, j int)bool
```

## func (IntSlice)Swap
```go
func (p IntSlice)Swap(i, j int)
```

## func (IntSlice)Sort 
```go
func (p IntSlice)Sort()
```
> Sort 等价于调用 Sort(p)

## func (IntSlice)Search
```go
func (p IntSlice)Search(x int)int
```
> Search 等价于调用 SearchInts(p, x)

## type Float64Slice
```go
type Float64Slice []float64 
```
> Float64Slice 给 []float64 添加方法以满足 Interface 接口，以便排序为递增序列

## func (Float64Slice)Len
```go
func (p Float64Slice)Len()int
```

## func (Float64Slice)Less
```go
func (p Float64Slice)Less(i, j int)bool
```

## func (Float64Slice)Swap
```go
func (p Float64Slice)Swap(i, j int)
```

## func (Float64Slice)Sort
```go
func (p Float64Slice)Sort()
```
> Sort 等价于调用 Sort(p)

## func (Float64Slice)Search
```go
func (p Float64Slice)Search(s float64)int
```
> Search 等价于调用 SearchFloat64(p, x)

## type StringSlice
```go
type StringSlice []string 
```
> StringSlice 给 []string 添加方法以满足 Interface 接口，以便排序为递增序列

## func (StringSlice)Len
```go
func (p StringSlice)Len()int
```

## func (StringSlice)Less
```go
func (p StringSlice)Less(i, j int)
```

## func (StringSlice)Swap
```go
func (p StringSlice)Swap(i, j int)
```

## func (StringSlice)Sort
```go
func (StringSlice)Sort()
```
> Sort等价于调用Sort(p)

## func (StringSlice)Search
```go
func (p StringSlice)Search(x string)int
```
> Search 等价于调用 SearchString(p, x)

## func Sort
```go
func Sort(data Interface)
```
> Sort 排序 data。它调用1次data.Len确定长度，调用O(n*log(n))次data.Less和data.Swap。本函数不能保证排序的稳定性（即不保证相等元素的相对次序不变）。

## func Stable
```go
func Stable(data Interface)
```
> Stable排序data，并保证排序的稳定性，相等元素的相对次序不变。它调用1次data.Len，O(n*log(n))次data.Less和O(n*log(n)*log(n))次data.Swap。

## func IsSorted
```go
func IsSorted(data Interface)bool
```
> IsSorted 报告 data 是否已经被排序。

## func Reverse
```go
func Reverse(data Interface)Interface
```
> Reverse 对该接口排序可生成递减序列。

## func Search
```go
func Search(n int, f func(int)bool)int
```
> Search函数采用二分法搜索找到[0, n)区间内最小的满足f(i)==true的值i。也就是说，Search函数希望f在输入位于区间[0, n)的前面某部分（可以为空）时返回假，而在输入位于剩余至结尾的部分（可以为空）时返回真；Search函数会返回满足f(i)==true的最小值i。如果没有该值，函数会返回n。注意，未找到时的返回值不是-1，这一点和strings.Index等函数不同。Search函数只会用区间[0, n)内的值调用f。
> 
> 一般使用Search找到值x在插入一个有序的、可索引的数据结构时，应插入的位置。这种情况下，参数f（通常是闭包）会捕捉应搜索的值和被查询的数据集。

## func Ints 
```go
func Ints(a []int)
```
> Ints 函数将 a 排序为递增顺序

## func IntsAreSorted
```go
func IntsAreSorted(a []int)bool
```
> IntsAreSorted 检查 a 是否已排序为递增顺序

## func SearchInts
```go
func SearchInts(a []int, x int)int
```
> SearchInts 在递增顺序的a中搜索x，返回x的索引。如果查找不到，返回值是x应该插入a的位置（以保证a的递增顺序），返回值可以是len(a)

## func Float64s 
```go
func Float64s(a []float64)
```
> Float64s 函数将a排序为递增顺序

## func Float64sAreSorted
```go
func Float64sAreSorted(a []float64) bool
```
> Float64sAreSorted检查a是否已排序为递增顺序

## func SearchFloat64s
```go
func SearchFloat64s(a []float64, x float64) int
```
> SearchFloat64s在递增顺序的a中搜索x，返回x的索引。如果查找不到，返回值是x应该插入a的位置（以保证a的递增顺序），返回值可以是len(a)

## func Strings
```go
func Strings(a []string)
```
> Strings函数将a排序为递增顺序

## func StringsAreSorted
```go
func StringsAreSorted(a []string) bool
```
> StringsAreSorted检查a是否已排序为递增顺序

## func SearchStrings
```go
func SearchStrings(a []string, x string) int
```
> SearchStrings在递增顺序的a中搜索x，返回x的索引。如果查找不到，返回值是x应该插入a的位置（以保证a的递增顺序），返回值可以是len(a)