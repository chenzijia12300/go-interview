### 如果协程 A 发生了 `panic` ，协程 B 是否会因为协程 A 的 `panic` 而挂掉？

是的，当协程A发生panic时，协程B也会挂掉

### 如果协程 A 发生了 `panic` ，协程 B 是否能用 `recover` 捕获到协程 A 的 `panic` ？

不可以，无论那个协程发生了panic、只能在协程自身使用`recover`接住，在真实业务开发中可以通过以下方法来避免在各个协程中重复声明recover
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// 该函数的参数为多个业务逻辑函数，且函数个数为变长参数
func allTasks(tasks ...func()) {
	var wg sync.WaitGroup

	for _, t := range tasks {
		wg.Add(1) // 每启动一个协程等待组加 1

		go func(f func()) { // 匿名函数的参数为业务逻辑函数
			defer func() {
				// 在每个协程内部接收该协程自身抛出来的 panic
				if err := recover(); err != nil {
					fmt.Println("defer", err)
				}
				wg.Done() // 每个协程结束时给 等待组减 1
			}()

			f() // 业务函数调用执行

		}(t) // 将当前的业务函数名传递给协程
	}
	wg.Wait()

}

// 业务逻辑 A
func A() {
	fmt.Println("A func begin")
	panic("error A")
}

// 业务逻辑 B
func B() {
	fmt.Println("B func begin")
}

func main() {
	allTasks(A, B) // 将业务逻辑函数名 A B 传递给封装好的处理函数
	time.Sleep(1 * time.Second)
	fmt.Println("main end")
}
```