### gin.Recovery() 中间件作用

Recovery中间件用于恢复程序运行时产生的异常，如果存在异常则会返回http状态码为500，用于防止程序中存在开发者为考虑到的异常情况而导致程序退出。

当我们使用`gin.Default`方法时，默认已经启用该中间件了，如果是通过`gin.New`方法创建时则需要自己显性声明了。


### Gin如何设置接口超时控制

Gin官方提供了一个中间件timeout，用于简化我们的代码。

> [gin-contrib/timeout](https://github.com/gin-contrib/timeout) 是 Gin 框架的一个官方插件，提供了一个可靠的方式来设置接口的超时时间并在超时时返回错误信息。使用 [gin-contrib/timeout](https://github.com/gin-contrib/timeout) 可以大大简化我们的代码，使代码更加清晰易懂。
```go
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/gin-contrib/timeout"
	"github.com/gin-gonic/gin"
)

func testResponse(c *gin.Context) {
	c.JSON(http.StatusGatewayTimeout, gin.H{
		"code": http.StatusGatewayTimeout,
		"msg":  "timeout",
	})
}

func timeoutMiddleware() gin.HandlerFunc {
	return timeout.New(
		timeout.WithTimeout(3000*time.Millisecond),
		timeout.WithHandler(func(c *gin.Context) {
			c.Next()
		}),
		timeout.WithResponse(testResponse),
	)
}

func main() {
	r := gin.New()
	r.Use(timeoutMiddleware())
	r.GET("/slow", func(c *gin.Context) {
		time.Sleep(5000 * time.Millisecond)
		c.Status(http.StatusOK)
	})
	if err := r.Run(":8080"); err != nil {
		log.Fatal(err)
	}
}
```