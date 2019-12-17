---
layout: post
title:  "多年之后再次找回SICP"
date:   2019-12-17
categories: programming
---


# 历史
很久之前翻看过 `SICP` , 最早的时候大概是本科的时候，那个时候编程的基本概念也掌握
的不是很好，对 `SICP` 里面的递归思想就非常难以理解。

后来大概在研究生的时候再次翻看 `SICP` , 那个时候大概有一些 `Python` 的编程基础，
但跟起来还是有一点痛苦。就是大概能理解文彬是想要表达的意思，但是很难做出来课后的
习题。

# 现在
感觉最近比较大的改变一个是自己开始能分拆一个比较大的项目，然后把各个功能点拆开，
另外一个比较大的帮助也是最近两年在比较严肃的学习 `Haskell` , `Haskell` 的整个思
路和 `SICP` 里面的思路也都比较像，类似 `Stream` 之类的概比如。

今天突然又想到这本书，网上 `MIT` 有免费的英文版本，排版相当精美，地铁上看了一会，
回家也做了一下那个习题，大概在博客这里面记录一下。

```scheme
;;these are the code on the class

(define tolerance 0.00001)
(define (fixed-point f first-guess)
  (define (close-enough? v1 v2) (< (abs (- v1 v2))
                                   tolerance))
  (define (try guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
          next
          (try next))))

  (try first-guess))

(define (fixed-point-of-transform g transform guess)
  (fixed-point (transform g) guess))

(define (average x y) (/ (+ x y) 2))

(define (sqrt x)
  (fixed-point (lambda (y) (average y (/ x y)))
               1.0))

(define (cube x) (* x x x))

(define dx 0.00001)
(define (deriv g)
  (lambda (x) (/ (- (g (+ x dx)) (g x)) dx)))


(define (newton-transform g)
  (lambda (x) (- x (/ (g x) ((deriv g) x)))))

(define (newtons-method g guess)
  (fixed-point (newton-transform g) guess))
;; problem 1-40
(define (cubic a b c d)
  (lambda (x)
    (+ (* a x x x) (* b x x) (* c x) d)))

;; problem 1-41
(define (double f)
  (lambda (x) (f (f x))))

(((double (double double)) inc) 5)


;; problem 1-42
(define (compose f g)
  (lambda (x) (f (g x))))

;; problem 1-43
(define (square x) (* x x))
;; ((repeated square 2) 5)

(define (repeated f n)
  (if (= n  1)
      f
      (compose f (repeated f (- n 1)))))

;; problem 1-44 smooth
(define (smooth f)
  (lambda (x)
    (average (f (+ x dx))
             (f (- x dx)))))
```

其实环境用的并不是 `Scheme` 的环境，用的是 `Racket` 的环境，还好 `racket` 有个包
叫 `SICP` , 可以直接写 `Scheme` 。很赞。

编程是个修行，`SICP` 也适合不同的时间拿来再看一次，也许还会有新的收获。

