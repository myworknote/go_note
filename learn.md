# go相关资源

中文文档：http://docscn.studygolang.com/doc/

指南：http://tour.studygolang.com/list

# 语言基础

## 包,变量,函数

```go
//每个 Go 程序都是由包构成的。声明一个 main 包，GO程序从 main 包开始运行。
package main
/*引入包
import "fmt"
import "math"
or
import (
	"fmt"
	"math"
)
*/
import "fmt"

//申明变量
var i, j int = 1, 2
//强制类型转换,函数内可简化 f := float64(i)
var f float64 = float64(i)
// 和C不同，Go在不同类型的项之间赋值时需要显式转换
// var x float64  =  i; //报错
//申明常量,常量不能用 := 语法声明。
const Pi  float64 = 3.14
//数值常量申明
const (
	Big = 1 << 2
	Small = Big >> 1
)


//定义函数
func add(x int, y int) int {
	//类型在变量名 之后。
	var scale int = 200
	return x + y + scale
}
//当连续两个或多个函数的已命名形参类型相同时，除最后一个类型以外，其它都可以省略。
func sub(x int, y int) int {
	return x - y
}

//函数可以返回任意数量的返回值
func swap(x, y string) (string, string) {
	return y, x
}

//返回值可被命名,它们会被视作定义在函数顶部的变量
//没有参数的 return 语句返回已命名的返回值。
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

//实现 main 函数，main 函数是默认执行的。
func main() {
    fmt.Println("Hello, World!")
    fmt.Println(add(10,20))
    fmt.Println(sub(10,20))
	// =是赋值， := 是声明变量并赋值,并且系统自动推断类型。
	a, b := swap("hello", "world")
	fmt.Println(a, b)
	fmt.Println(Big,Small)
}
//其他：
//大写字母开头的名称是导出的，小写字母开头不导出。
```

> //运行
>
> go run hello.go

> 1. 大写字母开头的名称是导出的，小写字母开头不导出。
> 2. 函数外的每个语句都必须以关键字开始（var, func 等等），因此 := 结构不能在函数外使用.
> 3. 没有明确初始值的变量声明会被赋予它们的 **零值**。数值类型为 0，布尔类型为 false，字符串为 ""（空字符串）。
> 4. 在声明一个变量而不指定其类型时,变量的类型由右值推导得出

## 流程控制

```go
package main

import "fmt"
import "math"
import "runtime"
import "time"

func switch_test(){
	//Go 的 switch 语句， 自动提供了每个 case 后面所需的“ break”` 语句。case 无需为常量，且取值不必为整数
	switch os:=runtime.GOOS;os{
		case "windows":
			fmt.Println("windows platform")
		case "linux":
			fmt.Println("Linux")
		default:
			fmt.Println("default")
	}
	//没有条件的 switch 同 switch true 一样。
	t := time.Now()
	switch {
		case t.Hour() < 12:
			fmt.Println("Good morning!")
		case t.Hour() < 17:
			fmt.Println("Good afternoon.")
		default:
			fmt.Println("Good evening.")
	}	
}

func pow(x, n, lim float64) float64 {
	//if 语句可以在条件表达式前执行一个简单的语句。
	//该语句声明的变量作用域仅在 if 之内。
	if v := math.Pow(x, n); v < lim {
		return v
	}else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// 这里开始就不能使用 v 了
	return lim
}

func defer_test(){
	//defer 语句会将函数推迟到外层函数返回之后执行。
	//推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。
	//推迟的函数调用会被压入一个栈中。当外层函数返回时，被推迟的函数会按照后进先出的顺序调用。
	defer fmt.Println("defer print 1")
	defer fmt.Println("defer print 2")	
	fmt.Println("hello world")
}


func main(){
	sum := 0
	//for循环
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
	
	//c中的while循环，在GO中也使用for,只是去掉了分号
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)	
	
	//如果省略循环条件，该循环就不会结束，因此无限循环可以写得很紧凑。
	/*
	for {
	}
	
	*/
	//if 语句与 for 循环类似，表达式外无需小括号 ( ) ，而大括号 { } 则是必须的
	if sum > 0 {
		//
	}
}
```



> 1. Go 的 for 语句后面的三个构成部分外没有小括号， 大括号 `{ }` 则是必须的。初始化语句和后置语句是可选的。
> 2. 没有条件的 switch 同 `switch true` 一样

## 指针

Go 拥有指针。指针保存了值的内存地址。

类型 `*T` 是指向 `T` 类型值的指针。其零值为 `nil`。

```go
package main

import (
	"fmt"
)

//指针
func pointer_test(){
	i:=42
	var p *int = nil //初始化指针为nil
	p = &i //指向操作数的指针
	tmp :=&i //申明并初始化指针
	fmt.Println(*p,*tmp)
	
	*p = 100
	fmt.Println(*p,*tmp)
}

//结构体
func struct_test(){
	//申明结构体
	type Vector struct{
		 x int
		 y int
	}
	//定义结构体变量
	var vector Vector = Vector{10,20}
	var (
		v1 = Vector{x :10,y : 20}
		v2 = Vector{}
		v3 = Vector{y:20}
		vp = &Vector{x:100,y:200} //指向结构体的指针
	)
	fmt.Println(v1,v2,v3,vp)
	
	
	//指向结构体的指针
	p := &vector;
	
	fmt.Println(vector,vector.x,vector.y)
	//允许使用隐式引用方式(p.x)访问结构体变量来简化书写
	fmt.Println((*p).x,p.x)	
}

//数组
func array_test(){
	//类型 [n]T 表示拥有 n 个 T 类型的值的数组。数组的长度是其类型的一部分，因此数组不能改变大小
	var arr [10]int = [10]int{1,2,3,4,5,6,7,8,9,10}
	fmt.Println(arr)
	//类型 []T 表示一个元素类型为 T 的切片。
	var s = arr[1:3] //半开区间，包括第一个元素，但排除最后一个元素。
	//打印切片/数组的长度和容量,切片的长度不能超过底层数组的容量
	fmt.Println(s,len(s),cap(s),len(arr),cap(arr)) //2 9 10 10
	
	//通过内置函数make创建切片,这也是创建动态数组的方式。
	a :=make([]int,5) // len(a)=5 cap(a)=5
	fmt.Println(a,len(a),cap(a))	
	/*
	可以指定容量
	b := make([]int, 0, 5) // len(b)=0, cap(b)=5
	b = b[:cap(b)] // len(b)=5, cap(b)=5
	b = b[1:]      // len(b)=4, cap(b)=4
	*/
	
	//通过内置函数append可向切片追加元素,结果是一个包含原切片所有元素加上新添加元素的切片(新的切片)。
	b := append(a,1,2,3,4,5)
	b[0] = 100
	fmt.Println(a,len(a),cap(a))
	fmt.Println(b,len(b),cap(b))
	
	//range遍历切片
	/*	可以使用_忽略指定遍历(下划线不会被定义为变量)
		for i, _ := range pow
		for _, value := range pow	
		//只需要索引则忽略第二个变量
		for i := range pow
	*/
	for index,item:=range b{
		fmt.Println(index,item)
	}
}

//map测试
func map_test(){
	type Vector struct{
		x int
		y int
	}
	var m map[string]Vector //申明一个map,初始为nil
	m = make(map[string]Vector) //使用make函数创建一个map
	
	//直接初始化
	m = map[string]Vector{
		"pos1":Vector{x:1,y:2},
		"pos2":{x:3,y:4},  //顶级类型只是一个类型名，可以在文法的元素中省略它。
		//奇怪的是,最后一个逗号必须加,否则报错
	}
	
	m["pos"] = Vector{x:10,y:20}
	fmt.Println(m)
	/*
	操作map
	v :=m["pos"] //取
	m["pos"] = {x:20,y:200} //改
	delete(m,"pos")  //删
	elem, ok = m[key] //通过双赋值检测某个键是否存在：若 key 在 m 中，ok 为 true ；否则，ok 为 false。
	*/
}

//函数类型
func func_test(){
	//定义函数类型
	type Myfunc  func(float64, float64) float64
	//定义函数变量
	var add func(float64, float64) float64
	//函数变量赋值
	add =  func(x,y float64) float64{
		return x + y
	}
	
	//调用测试+闭包测试
	var wrapper = func(fn Myfunc) func(float64,float64) float64{
		//返回闭包
		sum := 0.0
		return func(x ,y float64) float64{
			sum += fn(x,y)
			return sum
		}	
	}
	fmt.Println(wrapper(add)(1.0,2.0))
	fmt.Println(wrapper(add)(1.0,2.0))
}

func main(){
	pointer_test()
	struct_test()
	array_test()
	map_test()
	func_test()
}
```





> 1. 切片就像数组的引用.切片并不存储任何数据，它只是描述了底层数组中的一段。更改切片的元素会修改其底层数组中对应的元素。
>
> 2. nil 切片的长度和容量为 0 且没有底层数组。
> 3. 当 Slice底层数组太小，不足以容纳所有给定的值时，它就会分配一个更大的数组。返回的切片会指向这个新分配的数组。
> 4. 当使用 `for` 循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。
> 5. 当从映射中读取某个不存在的键时，结果是映射的元素类型的零值。

## **方法和接口**

### 方法

Go 没有类。不过你可以为结构体类型定义方法。方法就是一类带特殊的 **接收者** 参数的函数。

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct{
	x float64
	y float64
}
//为结构体构建一个方法,接收者为结构体
func (v Vertex) Abs() float64{
	return math.Sqrt(v.x * v.x + v.y*v.y)
}
//可以为非结构体类型声明方法。
//以值为接收者的方法被调用时，接收者既能为值又能为指针
type MyFloat float64
func (v MyFloat)Abs() float64{
	if v<0 {
		return float64(-v)
	}
	return float64(v)
}

//可以为指针接收者声明方法,指针接收者的方法可以修改接收者指向的值
//不能是内建类型的指针类型
//指针为接收者的方法被调用时，接收者既能为值又能为指针：
func (v *Vertex)Scale(s float64){
	v.x = v.x * s
	v.y= v.y * s	
}

func Scale(v *Vertex, s float64) {
	v.x = v.x * s
	v.y= v.y * s
}

func main(){
	v :=Vertex {10,20}
	fmt.Println(v)
	//结构体方法
	fmt.Println(v.Abs())
	
	//非结构体方法
	var f MyFloat = 1000.
	fmt.Println(f.Abs())
	
	//指针对象方法
	var p *Vertex = &v
	//p.Scale(2.0)
	v.Scale(2.0) //OK
	fmt.Println(p)
	fmt.Println(v)
	//fmt.Println(p.Abs()) //OK
	
	//测试常规函数
	Scale(&v,100.0)
	fmt.Println(v)
}
```



> 1.方法只是个带接收者参数的函数。
>
> 2.接收者的类型定义和方法声明必须在同一包内；不能为内建类型声明方法。
>
> 3.指针为接收者的方法被调用时，接收者既能为值又能为指针.`v.Scale(5)` 解释为 `(&v).Scale(5)`。
>
> 4.以值为接收者的方法被调用时，接收者既能为值又能为指针.方法调用 `p.Abs()` 会被解释为 `(*p).Abs()`。

### 接口

接口类型 是由一组方法签名定义的集合。接口类型的变量可以保存任何实现了这些方法的值。

(理解可以认为是所谓的鸭子类型)。



> 1.接口值可以看做包含值和具体类型的元组：(value, type)，接口值调用方法时会执行其底层类型的同名方法。
>
> 2.保存了 nil 具体值的接口其自身并不为 nil。
>
> 3.空接口可保存任何类型的值。（因为每个类型都至少实现了零个方法。），类型为：interface{}

```go
package main

import(
	"fmt"
	"math"
)

func main(){
	var p Abser = nil  //接口的底层数据为nil
	v := Vertex{x:10.0,y:20.0}
	f := Myfloat(20.0)
	p = &f  //f Myfloat 实现了Abs方法
	p = &v  //v *Vertex 实现了Abser
	//p = v  //v Vertex 未实现Abser方法,此处会报错
	fmt.Println(p)
	describe(p)
}

//接口可以作为值传递
//接口值保存了一个具体底层类型的具体值。
func describe(i Abser){
	//打印:(&{10 20}, *main.Vertex)
	fmt.Printf("(%v, %T)\n", i,i)
}

//空接口
//interface{}

//定义接口
type Abser interface{
	//符合本接口的对象必须存在下面方法
	Abs() float64
}


type Vertex  struct {
	x float64
	y float64
}
//为上述结构体指针申明一个方法
func (v *Vertex) Abs() float64{
	return math.Sqrt(v.x*v.x + v.y*v.y)
}

type Myfloat float64
//为非结构体申明一个方法
func (v Myfloat) Abs() float64{
	if v<0{
		return float64(-v)
	}
	return float64(v)	
}
```



### 类型断言

```go
t := i.(T)
```

该语句断言接口值 `i` 保存了具体类型 `T`，并将其底层类型为 `T` 的值赋予变量 `t`。若 `i` 并未保存 `T` 类型的值，该语句就会触发一个恐慌。

```go
package main

import(
	"fmt"
)

func main(){
	//类型断言：
	test()
	do(10)
}

func test(){
	//类型断言：
	//判断 一个接口值是否保存了一个特定的类型，类型断言可返回两个值：其底层值以及一个报告断言是否成功的布尔值。
	var i interface{} = "hello"
	t,ok :=i.(string)   //t="hello",ok = true
	fmt.Println(t,ok)

	s1,ok :=i.(float64)  //不报错 s1=0,ok=false
	fmt.Println(s1,ok)
	
	//s :=i.(float64)  //报错(panic)
	//fmt.Println(s,ok)
}

//类型选择
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v) //没有匹配，v 与 i 的类型相同,其他情况 v类型为type
	}
}
```



### Stringer接口

本接口是一个可以用字符串描述自己的类型。`fmt` 包（还有很多包）都通过此接口来打印值。

```go
type Stringer interface {
    String() string
}

//测试：
func main(){
	f := MyInt(10)
	fmt.Println(f)
}
type MyInt int
func (f MyInt)String() string{
	return  fmt.Sprintf("MyInt type")
}
```

### error接口

```go
type error interface {
    Error() string
}
```

待深入....

# 并发

