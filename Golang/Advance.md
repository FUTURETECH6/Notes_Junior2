[TOC]

# Array

```go
var variable_name [SIZE] variable_type
var variable_name = [SIZE]variable_type{val_0, val_1, ..., val_{SIZE-1}}
```

如果数组长度不确定，可以使用 **...** 代替数组的长度，编译器会根据元素个数自行推断数组的长度：

```go
var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
// 或
balance := [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

# Pointer

# Struct

```go
package main

import "fmt"

type Books struct {
    title string
    author string
    subject string
    book_id int
}


func main() {
    // 创建一个新的结构体
    fmt.Println(Books{"Go 语言", "www.runoob.com", "Go 语言教程", 6495407})

    // 也可以使用 key => value 格式
    fmt.Println(Books{title: "Go 语言", author: "www.runoob.com", subject: "Go 语言教程", book_id: 6495407})

    // 忽略的字段为 0 或 空
    fmt.Println(Books{title: "Go 语言", author: "www.runoob.com"})
}
```

# Slice

定义

```go
var identifier []type  // 声明一个未指定大小的数组来定义切片

// 用make
var slice1 []type = make([]type, len)
// 也可以简写为
slice1 := make([]type, len)

// 也可以指定容量
make([]T, length, capacity)
```

初始化

```go
s :=[] int {1,2,3 }  // cap = len = 3
s := arr[:]
s := arr[startIndex:]
s := arr[:endIndex]
```

`len()`和`cap()`

<u>一个切片在未初始化之前默认为 nil，长度为 0</u>

## append & copy

```go
new_slice = append(old_slice, val0, val1, ...)
copy(new_slice, old_slice)
```

# Range

在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 <u>key-value 对</u>。

```go
nums := []int{2, 3, 4}
sum := 0
for _, num := range nums {
    sum += num
}

//range也可以用在map的键值对上。
kvs := map[string]string{"a": "apple", "b": "banana"}
for k, v := range kvs {
    fmt.Printf("%s -> %s\n", k, v)
}
//range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
for i, c := range "go" {
    fmt.Println(i, c)
}
```

# Map

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```



```go
var countryCapitalMap map[string]string /*创建集合 */
countryCapitalMap = make(map[string]string)

/* map插入key - value对,各个国家对应的首都 */
couantryCapitalMap [ "France" ] = "巴黎"
countryCapitalMap [ "Italy" ] = "罗马"
countryCapitalMap [ "Japan" ] = "东京"
countryCapitalMap [ "India " ] = "新德里"

/* 使用键输出地图值 */
for country := range countryCapitalMap {
    fmt.Println(country, "首都是", countryCapitalMap [country])
}

/* 查看元素在集合中是否存在 */
capital, ok := countryCapitalMap [ "American" ] /* 如果确定是真实的,则存在,否则不存在 */
/* fmt.Println(capital) */
/* fmt.Println(ok) */
if (ok) {
    fmt.Println("American 的首都是", capital)
} else {
    fmt.Println("American 的首都不存在")
}
```

## Delete

`delete()` 函数用于删除集合的元素, 参数为 map 和其对应的 key。

```go
/* 创建map */
countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}

fmt.Println("原始地图")

/* 打印地图 */
for country := range countryCapitalMap {
    fmt.Println(country, "首都是", countryCapitalMap [ country ])
}

/*删除元素*/ delete(countryCapitalMap, "France")
fmt.Println("法国条目被删除")

fmt.Println("删除元素后地图")

/*打印地图*/
for country := range countryCapitalMap {
    fmt.Println(country, "首都是", countryCapitalMap [ country ])
}
```

# TypeCast

```go
mean = float32(sum)/float32(count)
```

# Interface

派生类

```go
/* 定义接口 */
type interface_name interface {
    method_name1 [return_type]
    method_name2 [return_type]
    method_name3 [return_type]
    ...
    method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
    /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
    /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
    /* 方法实现*/
}
```

Example

```go
package main

import (
    "fmt"
)

type Phone interface {
    call()
}

type NokiaPhone struct {}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()
}
```

在上面的例子中，我们定义了一个接口Phone，接口里面有一个方法call()。然后我们在main函数里面定义了一个Phone类型变量，并分别为之赋值为NokiaPhone和IPhone。然后调用call()方法，输出结果如下：