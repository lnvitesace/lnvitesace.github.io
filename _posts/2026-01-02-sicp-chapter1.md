---
title: "SICP第一章总结"
excerpt: "It is possible that software is not like anything else, that it is meant to be discarded: that the whole point is to always see it as a soap bubble?"
---

我在大一的时候自学过CS61A，名字也是SICP，只是换成用Python授课和对内容做了一些修改，当时的题解仍然躺在我的GitHub仓库里。最近陷入了莫名其妙的迷茫，看不清自己未来的路，于是重新捡起了这本号称能阐述计算本质的书，意外读的非常享受，决定每天睡前读几页，尽量完成所有练习。这几天刚读完第一章，在复习61A的知识之外又学到了许多，想着写个总结吧。我本来不喜欢写技术博客，但这本书确实是美到我了，值得为她单独写几篇。

如果只从能这一章，甚至这本书里学到一件事，那么就是用抽象的角度思考程序，明白自己该关心什么，不该关心什么。我们把一系列动作写成函数，实际上就是将这些过程抽象出来，用一个名字来代表他。我们关心这个函数能做什么，但不关心怎么做，作为一个思考能力有限的人类来说，这就把问题简化了许多，允许我们用简单的思考来解决大的问题，甚至是以前解决不了的问题。计算机是一个非常复杂的系统，从晶体管的角度来思考如何运行一个3A游戏就是天方夜谭，如果没有抽象的思想，我们什么也做不出来。当我们设计CPU时，会直接将逻辑门拿来用，而不管他们具体是怎么实现的，这就是逻辑电路给我们的抽象。当我们编写操作系统时，会直接参考CPU架构手册，而不管CPU是如何实现他们的，这就是CPU给我们的抽象。当我们用操作系统写代码时，我们知道编译出来的程序会遵循我们的指令，而不管内存如何分配、进程如何调度、中断如何处理等一系列由操作系统实现的细节。每一层都会做好他们该做的事，将他能做什么以接口的形式向上暴露出来，上一层会只依赖这一层提供的接口而非继续往下，否则这大概率会是一个失败的实现。

Scheme的特性让我们很容易看到程序的结构。除了`cond`、`let`这些特殊的关键字之外，一个括号只做一件事情。比如

```scheme
(* (+ 1 2) (+ 3 4))
```

从开头的`*`就能看出，我们要乘两个东西，而他们自己也是一个括起来的表达式，每都个是一个加运算。当遇到括号时，我们就知道程序的构造要向下挖一层了。从这里能很容易用树画出程序的结构，以`*`为根一步一步向下，直至原始值。对于它来说，要对两个数进行乘运算，但具体是哪两个数，他不关心，交给`+`来决定。当`+`计算出结果后，`*`就会用`+`提供的值，来进行真正的运算。

括号凸显出来的程序结构对于编译器来说是完美的，但当括号越来越多，对于我们人来说也会越来也复杂。真正提供有效抽象的，只有一个关键字：`define`。

```scheme
(define pi 3.14159)
(define radius 10)
(* pi (* radius radius))
314.159
```

`define`在这里把数字抽象成了一个用于代表它的名字，我们只需要知道它能做什么，而不需要知道它是什么。`pi`是圆周率，用它我们可以求出圆的面积，但`pi`具体是多少，我们不关心，它已经替我们抽象掉了。

这一章最核心的内容是，我们不仅可以抽象数据成一个名字，也可以抽象过程成一个名字。

 ```scheme
(define (sqare x) (* x x))
 ```

这里我们知道`square`可以返回一个值的平方，但它是如何得出平方的，并不用关心。从此以后，`square`和`+`、`*`这样的原始运算没有什么实质性的区别，在我们看来，他们都是一个可以进行特定运算的过程。

无论用怎样的词语来形容这样等级抽象的能力也不为过。我们再也不需要把所有过程全部写在一个无比巨大的括号里，而是可以把大的过程拆成小过程，逐个击破，或是从最基础的过程开始，一步一步向上将复杂运算抽象成一个个简单的名字，直至抵达塔尖。

抽象思想的另一个应用就是递归。在了解数学归纳法对递归函数正确性的严格证明之后，递归就是一个非常优美的表达方式，我们只需要了解问题和一个或多个子问题的关系，与合适的base case，递归就会像魔法一样自动帮我们算出答案。快速幂就是分治递归的一个典型例子。我们只用知道递归关系：
$$
\begin{align*}
b^n = (b^{n/2})^2 && \text{if n is even} \\
b^n = b \cdot b^{n - 1} && \text{if n is odd}
\end{align*}
$$
再加上 $n=0$ 时幂的值是1这个base case整个函数就是一个正确且优美的递归：

```scheme
(define (fast-expt b n)
    (cond ((= n 0) 1)
          ((evne? n ) (square (fast-expt b (/ n 2))))
          (else (* b (fast-expt b (- n 1))))))
```

我们有自信递归部分的调用可以得出正确结果，但不管是怎么得出的。如果我们写的`fast-expt`是正确的，那么递归调用的`fast-expt`也能得出正确的结果。

换硬币的问题是另一个动态规划递归的典型。要将大小为`a`的面值换成`n`个固定面值的硬币，有多少种换法？这种问题刚一拿到，几乎是无从下手。但只要我们知道和子问题之间的关系，递归就会帮我们解决。解决这个问题有两种情况：

1. 用第一种硬币
2. 不用第一种硬币

问题的解必定是这两种情况对应解的和。对于第一种情况，既然已经用了第一种硬币，那么解就是用所有种类硬币换`a - d`的换法，即`(cc (- amount (first-denomination kinds-of-coins)) kinds-of-coins)`其中`d`是第一种硬币的面值。对于第二种情况，既然不用第一种硬币，那么解就是除了第一种硬币以外剩下种类硬币换`a`的换法，即`(cc amount (- kinds-of-coins 1))`。

我们有自信他们能算出正确的值，但不关心怎么算的。设置合适的base case之后，函数就会魔法般的算出正确值。

以现在的眼光来看，动态规划的解法能更高效的完成任务，这是另一种自底向上的抽象，同样也是优美的，但在我看来程度不及纯递归。

当函数能作为参数时，抽象能力又上了一个台阶，开始可以把一些相关的过程抽象成一个一般性的过程。`sum-integers`、`sum-cubes`、`pi-sum`三个函数的共同点都是要对一个有限数列的每一个值做运算后求和。从这里就可以抽象出四个参数：

1. 数列起始点是什么？
2. 数列终点是什么？
3. 数列之间有什么关系？
4. 要对每一个值做什么运算？

将第一个参数建模为`a`，第二个参数建模为`b`，第三个参数建模为`next`，第四个参数建模为`term`，就可以得到以下一般的求和过程：

```scheme
(define (sum term a next b)
		(if (> a b)
        0
        (+ (term a)
           (sum term (next a) next b))))
```

有了这个一般的过程，上面的三个函数都可以写成这个一般过程的特殊情况：

```scheme
(define (sum-integers a b)
		(sum identity a inc b))
```

```scheme
(define (sum-cubes a b)
  	(sum cube a inc b))
```

```scheme
(define (pi-sum a b)
		(define (pi-term x)
     		(/ 1.0 (* x (+ x 2))))
  	(define (pi-next x)
      	(+ x 4))
 		(sum pi-term a pi-next b))
```



从这个一般性的过程覆盖了更广阔的算法，我们甚至可以`sum`来近似积分：
$$
\int_a^b f = \left[f(a + \frac{dx}{2}) + f(a + dx + \frac{dx}{2}) + f(a + 2dx + \frac{dx}{2}) +\ ...\right] dx
$$
这个积分实际上也就是数列值运算后的和，其中数列起点是 $a + \frac{dx}{2}$ ，终点是 $b$ ，数列之间的差是 $dx$  ,对每个值做的运算是 $f$ ，再将结果乘以 $dx$ 就是我们想要的：

```scheme
(define (integral f a b dx)
		(define dx 0.00001)
  	(define (add-dx x)
      	(+ x dx)
      	(* (sum f (+ a (/ dx 2.0)) add-dx b)
           dx))
```

甚至可以更近一步，数列之间的运算不一定是加法，也可以是乘法、除法或者我们定义的任何运算。这个更一般性的过程可以抽象出六个参数：

1. 数列起始点是什么？
2. 数列终点是什么？
3. 数列之间有什么关系？
4. 要对每一个值做什么运算？
5. 对于这些结果，我们要做什么运算？
6. 这个运算的单位元是什么？

```scheme
(define (accumulate combiner null-value term a next b)
    (if (> a b)
        null-value
        (combiner (term a) (accumulate combiner null-value term (next a) next b))))
```

那么`sum`就是这个过程的特殊情况：

```scheme
(define (sum term a next b)
    (accumulate + 0 term a next b))
```

将`+`改成`*`，可以得到范围内函数值的积：

```scheme
(define (product term a next b)
    (accumulate * 1 term a next b))
```

它和`sum`就只有运算符和单位元的不同。

甚至更近一步，即使是`accumulate`，也只是更高层抽象的特殊情况。我们可以给它加上一个`filter`，只对`filter`为真的项进行运算：

```scheme
(define (filtered-accumulate combiner null-value term a next b filter)
  	(if (> a b)
      	null-value
      	(if (filter a)
          	(combiner (term a)
                    	(filtered-accumulate combiner null-value term (next a) next b filter))
          	(filtered-accumulate combiner null-value term (next a) next b filter))))
```

`accumulate`就是`filtered-accumulate`的一个子集：

```scheme
(define (accumulate combiner null-value term a next b)
  	(define (always-true x) #t)
  	(filtered-accumulate combiner null-value term a next b always-true))
```

有了`filter`，就可以对特定满足条件的项进行运算。比如区间内所有素数的平方和：

```scheme
(filtered-accumulate + 0 square a inc b prime?)
```

又比如与 n 互素的数的乘积：

```scheme
(filtered-accumulate * 1 identity 1 inc n (lambda (x) (= (gcd x n) 1)))
```

最后几节用几个例子让我们对过程抽象的印象更加深刻。从`sqrt`可以抽象出`fixed-point`，而`fixed-point`可以用来实现`newtons-method`，而`newtons-method`又可以反过来高效的实现`sqrt`。这两个函数都是求一个函数经过某种变换之后的固定点，于是从它们之上又可以抽象出`fixed-point-of-transform`。除此之外，这一章大部分数值方法都是一种非常一般行方法的实例：`iterate-improve`

```scheme 
(define (iterative-improve good-enough? improve)
    (define (try guess)
        (let ((next (improve guess)))
            (if (good-enough? guess)
                next
                (try next))))
    try)
```

它接受两个函数，一个表示两值是否接近，一个表示下一步迭代的动作。返回一个函数，用于接收第一次猜测的值。`sqrt`和`fixed-point`都可以用它来实现：

```scheme
(define (sqrt x)
    (define (improve t)
        (avg t (/ x t)))
    (define (good-enough? guess)
        (< (abs (- (square guess) x)) 0.00001))
    ((iterative-improve good-enough? improve) 1.0))
```

```scheme
(define (fixed-point f first-guess)
    (define (good-enough? guess)
        (< (abs (- (f guess) guess)) 0.00001))
    ((iterative-improve good-enough? f) first-guess))
```

这就是全章最后一个习题。

MIT在2007年停止开设了SICP，现在6.001的课程是[Introduction to Computer Science and Programming in Python](https://ocw.mit.edu/courses/6-0001-introduction-to-computer-science-and-programming-in-python-fall-2016/)，在我看来并不是一个非常值得批判的决定。时代在进步，当前工程界的实践并不需要我们从头构建一个复杂系统，很多模块其他人已经替我们做好了，我们需要做的只是读文档，然后play around。[Programming by poking: why MIT stopped teaching SICP](http://lambda-the-ultimate.org/node/5335)中节选的Gerry Sussman的观点也是如此。不过工程是一回事，学术是另一回事，如果你的目标是尽量理解这个世界运行的规则，那么SICP绝对是一本非常值得研究的书，它让你知道多么复杂的系统，多么身经百战成熟的库，也是从最小的模块开始，一步一步抽象到这一层的。

