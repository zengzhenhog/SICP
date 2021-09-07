# 第二章 构造数据抽象

## 复合数据
scheme使用序对实现数据抽象，如下计算有理数的过程
``` scheme
  (define (add-rat x y)
    (make-rat (+ (* (numer x) (denom y))
                 (* (numer y) (denom x)))
              (* (denom x) (denom y))
    )
  )
  (define (make-rat n d) (cons n d))
  (define (numer x) (car x))           
  (define (denom x) (cdr x))           
```

上面的例子使用cons存储数据，看似有了数据，但是数据和过程的边界很模糊，数据其实也是用一个过程来表示，每一个过程都是一个独立的对象，如下cons的实现(`cons`返回过程`dispatch`，`(car z)`z应用为0，即`dispatch`的参数`m`为0)
``` scheme
  (define (cons x y)
    (define (dispatch m)
      (cond ((= m 0) x)
            ((= m 1) y)
            (else (error "argument not 0 or 1" m))
      )
    )
    dispatch
  )
  (define (car z) (z 0))
  (define (cdr z) (z 1))
```

[闭包](https://zhuanlan.zhihu.com/p/98298167)：引用了自由变量的函数

通过嵌套`cons`可以构造出序对链，表
``` scheme
  (cons 1 cons(2 cons(3 nil)))
  (list 1 2 3)
```
递归表，MAP将某个过程应用于表的每个元素，并返回相应结构构成的表，`null`判断表是否为空
``` scheme
  (define (map p l)
    (if (null? l)
        nil
        (cons (p (car l))
          (map p (cdr l))
        )
    )
  )
```

迭代表，for-each给定一个列表和操作，依次将其应用于表的每个元素，只完成操作，不构建新表
``` scheme
  (define (for-each proc list)
    (cond ((null? list) "done")
          (else (proc (car list))
                (for-each proc (cdr list))
          )
    )  
  )
```

语言的三要素：基本元素，组合方法，抽象方式（本课重点）

Escher例子，一个绘图语言
基本元素：picture图像
组合式：旋转