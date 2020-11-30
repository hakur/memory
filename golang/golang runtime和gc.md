## 1.内存逃逸分析
> 堆栈内存特性
* 栈内存会随着函数的返回，立即返还给内存，由CPU管理，不会引起golang的GC
* 堆内存会随着函数的返回，立即被golang的gc标记为待回收，由golang自己管理
* 堆的内存由golang自身管理和释放，速度比不上由CPU管理的栈内存

> 各类数据的分配去向 [文章1](https://blog.csdn.net/qq_35703848/article/details/103862437) [文章2](https://www.cnblogs.com/shijingxiang/articles/12200355.html)
* 返回值对象只会去堆，编译器认为它还会被引用
* 大对象即使不是返回值也会被分配到堆
    - 大对象make([]int,0,0,2000) 会去堆
    - 小对象make([]int,0,20)或make([]int,,0,1) 会去栈
* 对于函数外部没有引用的对象，也有可能放到堆中，比如内存过大超过栈的存储能力。
* 动态类型也会被分配到堆 如interface{}类型的数据，把一个正常的栈对象传递给 fmt.Println() 也会导致被分配到堆上
    - 如p:=make([]int,0,1)本来是栈上的，当他传入fmt.Println(p)时就会被编译器分配到堆上
* 局部变量如果被返回值闭包引用也回去堆，当然如果不是返回值类型闭包引用则不会去堆,如：
    ```go
    func Fibonacci() func() int {
        a, b := 0, 1
        return func() int {
            a, b = b, a+b
            return a
        }
    }
    ```

> 总结
* 传值还是穿指针的问题上，如果对象过大则用指针，如果对象很小就用值，以免滥用指针导致高频GC
> 附录
* 如何得知变量是分配在栈（stack）上还是堆（heap）上？

    准确地说，你并不需要知道。Golang 中的变量只要被引用就一直会存活，存储在堆上还是栈上由内部实现决定而和具体的语法没有关系。

    知道变量的存储位置确实和效率编程有关系。如果可能，Golang 编译器会将函数的局部变量分配到函数栈帧（stack frame）上。 然而，如果编译器不能确保变量在函数 return之后不再被引用，编译器就会将变量分配到堆上。而且，如果一个局部变量非常大，那么它也应该被分配到堆上而不是栈上。

    当前情况下，如果一个变量被取地址，那么它就有可能被分配到堆上。然而，还要对这些变量做逃逸分析，如果函数return之后，变量不再被引用，则将其分配到栈上。

> 命令行分析
* go build -gcflags "-m"

## 2.什么情况会触发GC？
* 自身定期GC
* runtime.
* 手动调用runtime.GC()函数
* 手动调用debug.FreeOSMemory()函数
* 手动调用debug.SetGCPercent()函数可以设置GC触发的百分比，SetGCPercent设置垃圾收集目标百分比：当新分配的数据与上一次收集后剩余的活动数据的比率达到此百分比时，将触发收集。SetGCPercent返回以前的设置。初始设置为GOGC环境变量在启动时的值，如果未设置该变量，则为100。负百分比将禁用垃圾回收。

## 3.golang会开启系统线程数量？
> 默认开启cpu核心数+1个系统线程，动态扩张数量的触发条件不明。
```
检验方法
cat /proc/${pid}/status|grep Threads
```

## 4.golang 内存扩容

## 5.编译时的操作
> ### 编译时赋值
```
go build -x "github.com/hakur/xx/pkg/version.Version=v1.0.0"

然后 运行的时候打印出来的版本就是v1.0.0
```

## 6.内存碎片化

## 7.golang的栈无限空间
> [文章](https://studygolang.com/articles/1744)