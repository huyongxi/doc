# 选择题

* 1. 下面程序输出什么（）

  ```go
  func f1(a []int) {
  	a = a[:2]
  	a[0] = 6
  }
  func main() {
  	a := []int{1, 2, 3, 4, 5}
  	f1(a)
  	fmt.Println(len(a), a[0])
  }
  ```

  A. 5  1       B. 5  6      C. 2  1     D. 2  6

  答案 B

* 2. 下面程序输出什么（）

  ```go
  func f1(a int) (c int) {
  	c = 0
  	if a <= 0 {
  		return
  	}
  	defer func() { c = a + f1(a-1) }()
  	return
  }
  
  func main() {
  	fmt.Println(f1(19))
  }
  ```

  A. 189       B. 190      C. 191     D. 程序错误

  答案 B

* 3.  下面程序输出什么（）

  ```go
  func f1(c chan int) {
  	c = make(chan int)
  }
  
  func main() {
  	var c chan int
  	fmt.Println(c == nil)
  	f1(c)
  	fmt.Println(c == nil)
  }
  ```

  A.  false false     B. false true     C. true  false     D. true  true

  答案 D

* 4.  通过指针变量 p 访问其成员变量 name，下面语法正确的是（）

  A.   p.name

  B.   p->name

  C.   (&p).name

  D.   *p.name

  答案 A

* 5. 下面程序输出什么 （）

  ```go
  func f1() int {
  	a := 1
  	defer func() { a++ }()
  	defer func(b int) { fmt.Println(b) }(a)
  	a++
  	return a
  }
  func main() {
  	f1()
  }
  ```

  A. 1       B. 2      C. 3     D. 4

  答案 A

* 6. 下面赋值不正确的是（）

  A.  var x []int = nil

  B.  var x interface{} = nil

  C.  var x string = nil

  D.  var x chan<- chan int = nil

  答案 C

* 7. 下面说法错误的是 （）

  A.   init函数没有输入参数、返回值

  B.  先执行导入包的init函数，再执行本包内的init函数

  C.  任何文件都可以包含任何数目的init函数

  D.  init函数可以被其他函数调用

  答案 D

* 8. 下面说法错误的是 （）

  A.   map 中元素不能寻址

  B.   string 中字符不能寻址

  C.   向nil map中添加元素时将导致panic

  D.   访问map中不存在的元素会导致panic

  答案 D

* 9. 下面关于map说法错误的是 （）

  A.   map是协程安全的

  B.   map可能会随着元素的增多重新分配更大的内存空间

  C.   非nil的map之间能进行相等比较

  D.   可以通过map[string]int{}来创建空map

  答案 C

* 10. 下面关于go 中字符串说法错误的是（）

  A.   内置的len函数会返回字符串的所有字符数

  B.   s[i:j] 不会产生内存拷贝

  C.   字符串不会为nil

  D.   字符串是不可变的字节序列

  答案 A

* 10.  s 是 slice类型，len(s) < cap(s) 下面说法错误的是 （）

  A.   访问s[i] 且 len(s) <= i < cap(s) ，会导致panic

  B.   s[:i]， len(s) <= i <= cap(s)  会导致panic

  C.   append函数可能会申请内存

  D.   copy(dst, src)返回值 是 min(len(dst), len(src))

  答案 B

* 11. 关于struct 下面说法错误的是 （）

  A.   struct 不能同时包含两个相同的匿名字段

  B.  一个struct可以同时包含导出和未导出的变量

  C.  一个结构体S不能再包含*S类型的字段

  D.  如果struct字段是大写字母开头，那么该字段就是导出的，包外可见

  答案 C

* 12. 下面说法错误的是 （）

  A.   重复关闭 channel 会导致 panic

  B.   向关闭的 channel 发送数据会 panic

  C.   从关闭的 channel 读数据会 panic

  D.   无缓冲channel 发送和接收动作是同时发生的

  答案 C

* 13. 下面说法错误的是 （）

  A.   range语句中生成的数据的值是真实集合元素的拷贝

  B.   new()分配的变量在堆上

  C.   函数可以返回栈上变量指针

  D.   cap函数不能用于map

  答案 B

* 14. 下面程序输出什么（）

  ```go
  func f1() {
  	defer func() { fmt.Println(1) }()
  	defer func() { fmt.Println(2) }()
  	panic("error")
  	defer func() { fmt.Println(3) }()
  
  }
  func main() {
  	f1()
  }
  ```

  A.  panic: error

  B.  1  2   panic: error

  C.  panic: error 1  2

  D. 2   1  panic: error

  答案 D

* 15. 下面程序输出什么（）

  ```go
  func main() {
  	var data *byte
  	var in interface{}
  	fmt.Println(data, data == nil)
  	fmt.Println(in, in == nil)
  	in = data
  	fmt.Println(in, in == nil)
  }
  ```

  A.  true   true  true

  B.  true   true  false

  C.  true   false  true

  D.  true   false  false

  答案 B

* 16. 关于同步锁，下面说法不正确的是（）

  A.   当一个goroutine获得了Mutex后，其他goroutine就只能乖乖的等待，除非该goroutine释放这个Mutex

  B.   RWMutex在读锁占用的情况下，会阻止写，但不阻止读

  C.   RWMutex在写锁占用情况下，会阻止任何其他goroutine（无论读和写）进来，整个锁相当于由该goroutine独占

  D.  Lock()操作需要保证有Unlock()或RUnlock()调用与之对应

  答案 D

* 17. 下面关于接口说法不正确的是（）

  A. 如果一个类型实现了一个 interface 中所有方法，我们说类型实现了该 interface

  B. 所有类型都实现了 empty interface

  C. interface 底层是由iface来实现的

  D. 具体类型转换为带方法的接口类型是在编译过程中进行检测

  答案 C

* 18.  golang中的指针运算不包括（）

  A.  可以通过“&”取指针的地址

  B.  可以对指针进行下标运算

  C.  可以转换成unsafe.Pointer类型

  D.  可以通过“*”取指针指向的数据

  答案 B

* 19. 下面说法正确的是（）

  A.   函数执行时，如果由于panic导致了异常，则延迟函数不会执行

  B.   可以给任意类型添加相应的方法

  C.   golang不支持goto语句

  D.  同级文件的包名不允许有多个

  答案 D

* 20. 下面程序输出什么（）

  ```go
  type INT int
  
  func (r *INT) String() string {
  	return fmt.Sprintf("INT")
  }
  func main() {
  	var a INT
  	fmt.Println(a)
  }
  ```

  A.  a未初始化

  B.  0

  C.  INT

  D.  INT 0

  答案 B

* 21. 下面程序输出什么（）

  ```go
  type data struct {
  	a int
  	s string
  }
  
  func main() {
  	wg := sync.WaitGroup{}
  	wg.Add(2)
  	c := make(chan *data)
  	go func(c chan<- *data) {
  		defer wg.Done()
  		d := data{a: 1, s: "hello"}
  		select {
  		case <-time.After(time.Second):
  			fmt.Println("timeout")
  		case c <- &d:
  		}
  	}(c)
  	go func(c <-chan *data) {
  		defer wg.Done()
  		time.Sleep(2 * time.Second)
  		select {
  		case d := <-c:
  			fmt.Println(*d)
  		default:
  			fmt.Println("default")
  		}
  	}(c)
  	wg.Wait()
  }
  ```

  A.  timeout  default

  B.  default   timeout  

  C.  timeout  

  D.  default

  答案 A

* 22. 下面说法不正确的是（）

  A.  select中的case的执行顺序是随机的

  B.  Map存储的是有序的键值对集合

  C.  range 遍历map key的顺序是不确定的

  D.  闭包是由函数及其相关引用环境组合而成的实体

  答案 B

* 23. 下面程序输出什么（）

  ```go
  type people struct {
  	name string
  	age  int
  }
  
  func main() {
  	m := map[int]people{
  		1: {name: "abc", age: 10},
  		2: {name: "def", age: 20},
  	}
  	for k := range m {
  		if k%2 == 0 {
  			m[k].name = "ABC"
  			m[k].age = 1
  		} else {
  			m[k] = people{name: "DEF", age: 2}
  		}
  
  	}
  	fmt.Println(m)
  }
  ```

  A.  map[2:{ABC 1} 1:{DEF 2}]

  B.  map[1:{DEF 2} 2:{def 20}]

  C.  map[1:{abc 10} 2:{ABC 1}]

  D.  编译错误

  答案 D

* 24. 下面程序输出什么（）

  ```go
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  	for k, v := range s {
  		go func(i *int) {
  			time.Sleep(time.Second)
  			fmt.Printf("%d ", *i)
  		}(&v)
  		s[k] = 0
  	}
  	time.Sleep(2 * time.Second)
  }
  ```

  A.   运行时出错

  B.   0 0 0 0 0 

  C.   5 5 5 5 5 

  D.   1 2 3 4 5 随机排列

  答案 C

* 25. 下面程序输出什么（）

  ```go
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  
  	for k := range s {
  		if k == 2 || k == 3 {
  			s = append(s[:k], s[k+1:]...)
  		}
  	}
  	fmt.Println(s)
  }
  ```

  A.   [1 4 5]

  B.   [1 3 4 5]

  C.   [1 2 5]

  D.  [1 2 4]

  答案 D

* 26. 下面程序输出什么（）

  ```go
  func main() {
  	array := [4]int{10, 20, 30, 40}
  	slice := array[0:2]
  	newSlice := append(slice, 50)
  	newSlice[1] += 1
  	fmt.Println(slice)
  }
  ```

  A.   [10 21]

  B.   [10 20]

  C.   [10  21  50]

  D.   [10  20  50]

  答案 A

* 27. 下面程序输出什么（）

  ```go
  func main() {
  	s := []int{1, 2, 3}
  	s1 := s[1:2]
  	fmt.Println(&s[1] == &s1[0])
  	s1 = append(s1, 0)
  	fmt.Println(&s[1] == &s1[0])
  }
  ```

  A.  true  true

  B.  true  false

  C.  false  false

  D.  false  true

  答案 A

* 28. golang中GC 是采用那种算法（）

  A.  引用计数

  B.  三色标记法

  C.  节点复制

  D.  分代收集

  答案  B

* 29. 下面代码输出什么（）

  ```go
  type People struct {
  	name string `json:"Name"`
  	Age  int
  }
  
  func main() {
  	p := People{name: "one", Age: 10}
  	s, err := json.Marshal(p)
  	if err != nil {
  		fmt.Println("error")
  		return
  	}
  	fmt.Println(string(s))
  }
  ```

  A.  {"Age":10}

  B.   {"Name":"one","Age":10}

  C.   {"name":"one","Age":10}

  D.   {"name":"one"}

  答案 A

* 30. 下面代码输出什么（）

  ```go
  type People struct{}
  
  func (p *People) ShowA() {
  	fmt.Println("A")
  	p.ShowB()
  }
  func (p *People) ShowB() {
  	fmt.Println("B")
  }
  
  type Teacher struct {
  	People
  }
  
  func (t *Teacher) ShowB() {
  	fmt.Println("TB")
  }
  
  func main() {
  	t := Teacher{}
  	t.ShowA()
  	t.ShowB()
  }
  ```

  A.  A  B  B

  B.  A  TB B

  C.  A  B  TB

  D.  A  TB  TB

  答案 C

* 31. 关于main函数，下面说法不正确的是（）

  A.  main函数所在的包必须为main包

  B.  main函数不能调用自己

  C.  main函数不能带参数

  D.  main函数不能定义返回值

  答案 B

* 32. 关于函数声明，下面语法错误的是（）

  A.  func f(a, b int) (value int, err error)

  B.  func f(a int, b int) (value int, err error)

  C.  func f(a, b int) (value int, error)

  D.  func f(a int, b int) (int, int, error)

  答案 C

* 33. 下面说法错误的是（）

  A.  内置函数delete可以删除数组切片内的元素

  B.  Golang支持反射，反射最常见的使用场景是做对象的序列化

  C.  go协程调度支持抢占

  D.  go生成的可执行文件入口点不是main.main

  答案 A

* 34. 下面说法错误的是（）

  A.  Go中的常量，它们是在编译时被创建

  B.  在Go中，枚举常量使用`iota`枚举器来创建

  C.  Go中的常量只能是数字，字符，字符串或者布尔类型

  D.  Go中的常量不能在函数内部定义

  答案 D

* 35. 下面说法错误的是（）

  A.  在函数调用参数中，数组是值传递

  B.  Go语言中对象的地址不会发生变化

  C.  不同goroutine之间不满足顺序一致性内存模型

  D.  可以设置GOMAXPROCS的数量大于CPU的数量

  答案 B

* 36. 下面代码输出什么（）

  ```go
  func main() {
  	for i := 0; i < 5; i++ {
  		i := i
  		defer func() {
  			println(i)
  		}()
  	}
  }
  ```

  A. 4 3 2 1 0

  B. 0 1 2 3 4 

  C. 4 4 4 4 4

  D. 5 5 5 5 5

  答案 A

* 37. 下面程序输出什么（）

  ```go
  func main() {
  	a := []int{}
  	var b []int
  	if buf, err := json.Marshal(a); err == nil {
  		fmt.Println(string(buf))
  	}
  	if buf, err := json.Marshal(b); err == nil {
  		fmt.Println(string(buf))
  	}
  }
  ```

  A.  [ ] [ ]

  B.  [ ] null

  C.  [ ]

  D.  null null

  答案 B

* 38. 下面说法正确的是（）

  A.  在函数体代码中可以有未使用的变量

  B.  字符串零值为nil

  C.  全局变量声明但不使用是可以的

  D.  `:=` 左侧必须是新变量

  答案 C

* 39. 下面程序输出什么（）

  ```go
  func main() {
  	a := [...]int{1: 2, 3: 4}
  	b := a[:]
  	fmt.Println(a, reflect.TypeOf(b) == reflect.TypeOf(a), &a[0] == &b[0])
  }
  ```

  A.   [1 2 3 4] true  false

  B.   [1 2 3 4] false  true

  C.   [0 2 0 4] true  false

  D.   [0 2 0 4] false  true

  答案 D

* 40. 关于go内存分配说法不正确的是（）

  A.  在go中，每个P都会被分配一个mcache，是私有的，从这里分配内存不需要加锁

  B.  当mcache不够时候，会向mheap申请内存

  C.  mheap是真实拥有虚拟地址的

  D.  在栈上分配的代价要远小于在堆上进行分配

  答案 B  从mcentral申请内存

* 41. 关于Channel说法不正确的是（）

  A.  对一个元素的send操作Happens Before对应的receive操作

  B.  对channel的close操作Happens Before receive端的收到关闭通知操作

  C.  对一个元素的receive 操作Happens Before对应的send完成操作

  D.  对于带缓存的Channel，假设Channel 的buffer 大小为C，那么对第k个元素的receive操作，Happens Before第k+C个send完成操作

  答案 C  对于无缓存的Channel

* 42. 关于go协程调度说法不正确的是（）

  A.  当G被阻塞在某个系统调用上时，执行该G的M会与P解绑

  B.  对M来说，P提供了相关的执行环境，如内存分配状态，任务队列

  C.   Go中的抢占实际上是为G设置抢占标记g.stackguard0

  D. M只会从P的Local队列中取出G，切换到G的堆栈并执行

  答案 D

* 43. 下面关于反射说法不正确的是（）

  A.  反射可以从接口类型到反射类型对象

  B.  可以修改反射类型变量的内部值

  C.  反射可以从反射类型对象到接口类型

  D.  reflect包使用Kind类型来表示类型所属的分类

  答案 B

* 44. 下面程序输出什么（）

  ```go
  type myType struct {
  	a int
  }
  
  func (m *myType) foo(p int) {
  	m.a = p
  }
  func main() {
  	var f func(int)
  	ins := myType{a: 1}
  	f = ins.foo
  	f(100)
  	fmt.Println(ins.a)
  }
  ```

  A.  100

  B.   1

  C.   编译错误

  D.  运行错误

  答案 A

* 45. 下面程序输出什么（）

  ```go
  type myType struct {
  	a int
  }
  
  func (m *myType) foo() {
  	if m.a%2 == 0 {
  		m.a++
  		return
  	}
  	m.a = 10
  }
  
  func main() {
  	var p interface{} = &myType{a: 1}
  	p.(interface {
  		foo()
  	}).foo()
  	fmt.Println(p.(*myType).a)
  }
  ```

  A.   1

  B.   10

  C.   11

  D.   编译错误

  答案 B

* 46. 下面程序输出什么（）

  ```go
  type obj struct{}
  
  func main() {
  	a := &obj{}
  	c := &obj{}
  	d := &obj{}
  	e := &obj{}
  	_ = fmt.Sprintf("%p\n", a)
  	_ = fmt.Sprintf("%p\n", c)
  	fmt.Println(a == c, a == d, d == e)
  }
  ```

  A.   true true true

  B.   false false true

  C.   true false false

  D.   true false true

  答案 C  a,c都逃逸到堆内存上

* 47. 

* 48. 

* 49. 

* 50. 

* 

* 

* 

  

  

  

  

  



# 编程题

* 1. 启动3个协程打印递增的数字, 协程1先打印1,2,3,4,5, 然后是协程2打印6,7,8,9,10, 然后是协程3打印11,12,13,14,15。接着再由协程1打印16,17,18,19,20....以此类推, 直到打印到2019. 程序的输出结果应该为:

     协程1  1
     协程1  2
     协程1  3
     协程1  4
     协程1  5
     协程2  6
     协程2  7
     协程2  8
     协程2  9
     协程2  10
     协程3  11
     协程3  12
     协程3  13
     协程3  14
     协程3  15

     .

     .

     .

     协程3  2006
     协程3  2007
     协程3  2008
     协程3  2009
     协程3  2010
     协程1  2011
     协程1  2012
     协程1  2013
     协程1  2014
     协程1  2015
     协程2  2016
     协程2  2017
     协程2  2018
     协程2  2019