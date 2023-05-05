### 题目简介
使用两个 goroutine 交替打印序列，一个 goroutine 打印数字， 另外一个 goroutine 打印字母， 最终效果如下:
```
1A2B3C4D5E6F7G8H9I10J11K12L13M14N15O16P17Q18R19S20T21U22V23W24X25Y26Z
```

### 整体思路
通过建立两个channel来实现,协程之间的协调打印,需要注意执行完毕后让协程退出，避免内存泄露。

```go
func alternateNumberAndWord() {
	letter, number := make(chan struct{}), make(chan struct{})
	wait := sync.WaitGroup{}

	go func() {
		i := 1
		for {
			select {
			case _, ok := <-number:
				if !ok {
					return
				}
				fmt.Print(i)
				i++
				letter <- struct{}{}
			}
		}
	}()
	wait.Add(1)
	go func() {
		i := 'A'
		defer func() {
			close(number)
			wait.Done()
		}()
		for {
			select {
			case <-letter:
				fmt.Printf("%c", i)
				i++
				if i > 'Z' {
					return
				}
				number <- struct{}{}
			}
		}
	}()
	number <- struct{}{}
	wait.Wait()
}

```