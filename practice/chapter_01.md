# 第一章 LISP——函数型语言

## 习题

### 1.1 用前缀表达式写出：

$$
\dfrac{3^2 - 3 * 4 + 4^2}{3 + 4} \qquad |5 - 10|^2 + |5 - 1|^2
$$

```lisp
(/ (+ (- (square 3) (* 3 4)) (square 4)) (+ 3 4))
(+ (square (abs (- 5 10))) (square (abs (- 5 1))))
```

### 1.2 写出以下函数的定义：

1. $add1(x) = x + 1;$

   ```lisp
   (de add1 (x) (+ x 1))
   ```

2. $sub1(x) = x - 1;$

   ```lisp
   (de sub1 (x) (- x 1))
   ```

3. $remainder(x, y) = 用y除x所得的余数；$

   ```lisp
   (de remainder (x, y) (mod y x))
   ```

4. $zerop(x), 恒为0的函数$

   ```lisp
   (de zerop (x) 0)
   ```

### 1.3 用条件表达式写出：

$$
x \lor y, \qquad x \land y, \qquad \lnot x
$$

```lisp
(de or (x, y)
    (cond (x T)
          (y T)
          (T nil)))

(de not (x)
    (cond (x nil)
          (T T)))

(de and (x, y)
    (cond ((not x) nil)
          ((not y) nil)
          (T T)))
```

### 1.4 写出以下谓词的定义：

$$
\begin{alignat}{0}
positive(x) & 当x为正数时值为T \\
one\textrm{-}digit(x) & 当x为一位数时值为T \\
divided\textrm{-}by(x, y) &当x能被y整除时值为T \\
odd(x) & 当x是奇数时值为T
\end{alignat}
$$

```lisp
(de positive (x)
	(cond ((greaterp x 0) T)))

(de one-digit (x)
	(cond ((greaterp x 9) nil)
	      ((lessp x -9) nil)
	      (T T)))

(de divided-by (x, y)
    (cond ((zerop (mod x y)) T)))

(de odd (x)
    (cond ((zerop (mod x 2)) nil)
          (T T)))
```

### 1.5 写出以下函数的递归定义：

1. $p2(n) = 2^n;$

   ```lisp
   (de p2 (n)
       (cond ((zerop n) 1)
             (T (* n (p2 (- n 1))))))
   ```

2. $s(n) = 1 * 2 + 2 * 3 + \cdots + n * (n + 1);$

   ```lisp
   (de s (n)
       (cond ((zerop 0) 0)
             (T (+ (* n (+ n 1)) (s (- n 1))))))
   ```

3. $gcd(x, y) = x 与 y 的最大公约数$（用辗转相减法）。

   ```lisp
   (de gcd (x, y)
       (cond ((eqn x y) x)
             ((greaterp x y) (gcd (- x y) y))
             ((lessp x y) (gcd x (- y x)))))
   ```

### 1.6 对上题的（3）给出计算`(GCD 10 14)`的调用关系图

```mermaid
graph LR
A["(gcd 10 14)"] --> B["(gcd 10 4)"]
B --> C["(gcd 6 4)"]
C --> D["(gcd 2 4)"]
D --> E["(gcd 2 2)"]
E --> F["2"]
```

### 1.7 写出 $g(x, y) = x^y$ 尾递归计算的办法

提示：利用辅助函数$x^y * z$，每次递归时使y的值减半（分奇、偶两种情况）。

```lisp
(de f (x y z)
    (cond ((zerop y) z)
          ((eqn y 1) (* x z))
          ((zerop (mod y 2)) (f (* x x) (/ y 2) z))
          (T (f x (- y 1) (* z x)))))
(de g (x y) (f x y 1))
```

### 1.8 写出`square-root`函数用尾递归计算的定义

```lisp
(de good-enough (guess x)
    (lessp (abs (- (* guess guess) x)) 0.001))

(de guess-next (guess x)
    (/ (+ guess (/ x guess)) 2))

(de sqrt-iter (guess x)
    (cond ((good-enough guess x) guess)
          (T (sqrt-iter (guess-next guess x) x))))

(de square-root (x) (sqrt-iter 1.0 x))
```

### 1.9 `Fibonacci`数列的通项公式是

$$
a_n = \begin{cases}
n, & 当 n = 0, 1 \\
a_{n-1} + a_{n-2}, & 当 n > 1
\end{cases}
$$

试写出计算$a_n$的递归定义的函数。此外，利用辅助函数
$$
h(n, m, k) = m * a_n + k * a_{n-1}
$$
设法写出用尾递归计算$a_n$的办法，对于$a_5$的计算比较两种不同的计算过程。

```lisp
;; 递归
(de f (n)
    (cond ((lessp n 2) n)
          (T (+ (f (- n 1)) (f (- n 2))))))

;; 尾递归
(de h (n m k)
    (cond ((zerop n) m)
          (T (h (- n 1) k (+ m k)))))

(de f (n) (h n 0 1))
```

递归版本：
$$
\begin{alignat}{}
f(5) &= f(4) + f(3) \\
&= f(3) + f(2) + f(2) + f(1) \\
&= f(2) + f(1) + f(1) + f(0) + f(1) + f(0) + 1 \\
&= f(1) + f(0) + 1 + 1 + 0 + 1 + 0 + 1 \\
&= 1 + 0 + 1 + 1 + 0 + 1 + 0 + 1 \\
&= 5
\end{alignat}
$$
尾递归版本：
$$
\begin{alignat}{}
f(5) &= h(5, 0, 1) \\
&= h(4, 1, 1) \\
&= h(3, 1, 2) \\
&= h(2, 2, 3) \\
&= h(1, 3, 5) \\
&= h(0, 5, 8) \\
&= 5
\end{alignat}
$$

### 1.10 写出`Ackermann`函数的定义，画出`Ack(2, 1)`的调用关系图

$$
Ack(n, m) = \begin{cases}
m + 1, & 当 n = 0 \\
Ack(n - 1, 1), & 当 n > 0, m = 0 \\
Ack(n - 1, Ack(n, m - 1)), & 其它情况
\end{cases}
$$

```lisp
(de ack (n m)
    (cond ((zerop n) (+ m 1))
          ((zerop m) (ack (- n 1) 1))
          (T (ack (- n 1) (ack n (- m 1))))))
```

$$
\begin{alignat}{}
ack(2, 1) &= ack(1, ack(2, 0)) \\
&= ack(1, ack(1, 1)) \\
&= ack(1, ack(0, ack(1, 0))) \\
&= ack(1, ack(0, ack(0, 1))) \\
&= ack(1, ack(0, 2)) \\
&= ack(1, 3) \\
&= ack(0, ack(1, 2)) \\
&= ack(0, ack(0, ack(1, 1))) \\
&= ack(0, ack(0, ack(0, ack(1, 0)))) \\
&= ack(0, ack(0, ack(0, ack(0, 1)))) \\
&= ack(0, ack(0, ack(0, 2))) \\
&= ack(0, ack(0, 3)) \\
&= ack(0, 4) \\
&= 5
\end{alignat}
$$

