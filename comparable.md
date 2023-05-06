#### 变量类型

##### 基本类型

- 整型：例如int，uint，int8，uint8，int16，uint16，int32，uint32，int64，uint64，byte，rune，uintptr等
- 浮点型：例如float32，float64
- 复数类型：例如complex64，complex128
- 字符串类型
- 布尔类型

##### 复合类型

- 数组
- struct

##### 引用类型

- slice
- map
- channel

#### 可比较类型

布尔类型、数字类型、字符串类型、指针、通道等

#### 不可比较类型

slice、map、function，但他们值为nil时可以与nil进行比较



#### 注意事项

在Go中基本类型的比较中两个变量类型必须完全相同、在Go中没有隐形类型转换，例如`uint8`和`uint`是无法之间比较的。

同时复合类型是逐一字段比较，因此array和struct中的元素都需要是可比较类型，array中长度也是类型的一部分、长度不相同的数组变量无法进行比较。



#### 参考资料

- [这可能是最全的golang的"=="比较规则了吧 - 简书 (jianshu.com)](https://www.jianshu.com/p/a982807819fa)