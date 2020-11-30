### goroutine与docker相处
* [文章](https://zhuanlan.zhihu.com/p/100165648)
* 描述：因为runtime.NumCPU()是一个syscall，所以即使docker限制一个1 cpu，但是内娱syscall来说可见的仍然是4 个cpu，因为会出现问题。
* 解决方法：https://github.com/uber-go/automaxprocs