# 第一章 构造过程抽象
## 黑盒模式
...

## 组合式


## 代换模型
并不是解释器的实际工作方式

## 线性迭代和线性递归
线性迭代：时间复杂度O(X) 空间复杂度O(1)
线性递归：时间复杂度O(X) 空间复杂度O(X)
相同的计算结果，但消耗的时间，占用的内存递归计算比迭代计算多
``` scheme
  (define (+ x y)
    if (= x 0)
      y
      (+ (- 1 x) (+ 1 y))
  )

  (define (+ x y)
    if (= x 0)
      y
      (+ 1 (+ (- 1 x) y))
  )
```

## 高阶过程抽象
公共模式：
``` scheme
  (define (<name> a b)
    (if (> a b)
      0
      (+ (<term> a)
        (<name> (<next> a) b))))
```
将过程作为参数，可以将复杂的过程分解成一个个小模块

求平方根方法
``` scheme
  (define (fixed-point f start)
    (define (iter old new)
      (if (close-enuf? old new)
        new
        (iter new (f new))
      )
    )
    (iter start (f start))
  )
```
方法实现
``` scheme
  (define (fixed-point f start)
    (define tolerance 0.00001)
    (define (close-enuf? u v)
      (< (abs (- u v)) tolerance)
    )
    (define (iter old new)
      (if (close-enuf? old new)
        new
        (iter new (f new))
      )
    )
    (iter start (f start))
  )
```

最终实现sqrt过程，average-damp过程的参数是一个过程，返回值也是一个过程 (λ是匿名过程，括号里是形参，P3 38:00)
``` scheme
  (define (sqrt x)
    (fixed-point 
      (average-damp (λ(y)(/ x y)))
      1
    )
  )
  (define average-damp
    (λ(f)
      (λ(x) (average (f x) x))
    )
  )
```
非匿名过程写法
``` scheme
  (define (average-demp f)
    (define (foo x)
      (average (f x) x)
    )
    foo
  )
```

在数学猜想的基础上，按愿望思维写出大致实现思路，再逐步通过一个个过程实现。愿望思维在工程中很重要
将函数作为第一元素，函数(过程)可以以变量命名，函数可以是另一个过程的参数，也可以是过程的返回结果，这样设计可以带来强大的抽象能力，复杂的计算可以清晰的编写出来

