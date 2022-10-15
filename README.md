# CEK-interp

A [CEK-Style](https://en.wikipedia.org/wiki/CEK_Machine) racket interpreter

This project is inspired by Matt's article([Matt: Writing CEK-style interpreters (or semantics) in Haskell](https://matt.might.net/articles/cek-machines/))

# Implemented

- define
- lambda
- begin
- if
- quote
- set!
- call/cc

# Usage

```racket
; variable
> (interp '(begin (define a 1) a))
1

; function
> (interp '(begin
             (define fac (lambda (n)
                           (if (= n 1)
                               1
                               (* n (fac (- n 1))))))
             (fac 5)))
120

; call/cc
> (interp '(begin
             (define (for-each f lst)
               (if (null? lst)
                   (void)
                   (begin
                     (f (car lst))
                     (for-each f (cdr lst)))))

             (define (generate-one-element-at-a-time lst)
               (define (control-state return)
                 (for-each
                  (lambda (element)
                    (set! return (call-with-current-continuation
                                  (lambda (resume-here)
                                    (set! control-state resume-here)
                                    (return element)))))
                  lst)
                 (return 'you-fell-off-the-end))

               (define (generator)
                 (call-with-current-continuation control-state))

               generator)

             (define generate-digit
               (generate-one-element-at-a-time '(1 2 3)))

             (list (generate-digit) (generate-digit) (generate-digit) (generate-digit))))
(1 2 3 you-fell-off-the-end)
```
