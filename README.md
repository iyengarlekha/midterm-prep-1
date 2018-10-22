# Midterm Exam Preparation

The problems below are similar to what you can expect for the midterm,
though some are slightly harder than what I'll ask in the exam.

## Static vs. Dynamic Scoping

Consider the following program:

```scala
 1: var b = 2

 2: def f(): Unit = {
 3:   var b = 3
 4:   ()
 5: }

 6: def g(): Int = {
 7:   f()
 8:   b + 4
 9: }

10: def h(): Int = {
11:   var b = 5
12:   g()
13: }

14: g()
15: h()

```

1. Under static scoping, what value does `g()` on line 14 return?

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under dynamic scoping, what value does `g()` on line 14 return?

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under static scoping, what value does `h()` on line 15 return? 

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under dynamic scoping, what value does `h()` on line 15 return? 

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     9
     ```
     </p></details>

## Parameter Passing Modes

Consider the following program:

```scala
def f(x: Int, y: Int) {
  x = y + 1
  println(x + y)
}

var z = 1
f(z, {z = z + 1; z})
println(z)
```

What does this program print if we make the following assumptions about
the parameter passing modes for the parameters `x` and `y` of
`params`:

1. both `x` and `y` are call-by-value parameters

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     5 2
     ```
     </p>
   </details>


2. `x` is call-by-reference and `y` is call-by-value

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     5 3
     ```
     </p>
   </details>


3. `x` is call-by-value and `y` is call-by-name

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6 3
     ```
     </p>
   </details>

4. `x` is call-by-reference and `y` is call-by-name

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     7 4
     ```
     </p>
   </details>

## Deep vs. Shallow Binding

```scala
def f(x: Int, h: () => Unit): () => Unit = {
  def g(): Unit = println(x)
  
  if (x == 0) f(1, g)
  else {
    h()
    g
  }
}

var x = 2

def h(): Unit = ()

f(0, h)()
```

What does this program print:

1. assuming static scoping and deep binding

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     0 1
     ```
     </p>
   </details>

1. assuming dynamic scoping and shallow binding

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     1 2
     ```
     </p>
   </details>

## Call Stacks and Memory Management

Consider the following (faulty) implementation of a merge sort
function in C. The function `merge_sort` takes a pointer `a` to the
first element of an array of integers and the length of the array `a`
as input, and is supposed to sort the array in-place in ascending
order. A few test runs of the compiled code on a test machine produce
correct results, which seems to indicate that the implementation is
correct. However, there is a problem in the code that may cause
`merge_sort` to produce unexpected results or even crash. What is this
problem? Explain why some simple tests may not reveal this
issue. Can you suggest a fix that solves it?

Hint: you don't really need to understand merge sort to spot the
problem in the code.

```c
int* merge(int* a, int mid, int length) {
  int b[length];
  int i = 0;
  int j = 0;
  int k = mid;

  while (i < length) {
    if (k >= length || j < mid && a[j] <= a[k]) {
      b[i++] = a[j++];
    } else {
      b[i++] = a[k++]; 
    }
  }
  
  return b;
}

void merge_sort(int* a, int length) {
  int mid = length / 2;

  if (mid == 0) return;

  merge_sort(a, mid);
  merge_sort(a + mid, length - mid);

  int* b = merge(a, mid, length); 

  for (int i = 0; i < length; i++) a[i] = b[i];
}
```

<details><summary>Solution</summary>
<p>
     
The problem is that the function `merge` returns a pointer to an array
`b` that is allocated on the stack within the activation record of
`merge`. Hence, when `merge` returns, this pointer will be dangling.
When `merge_sort` executes the `for` loop after `merge_sort` returns
and dereferences the returned pointer `b`, then this will have
undefined behavior. In particular, the code may crash with a
segmentation fault. However, the error may go undetected because the
old contents of the array `b` from the call to `merge` may still
reside in memory and so the `for` loop may actually copy the correct
values back into the array `a`.

The problem can be solved by moving the `for` loop from `merge_sort`
to the end of `merge`. In this case, `merge` does not need
a return value and its return type can be changed to `void`.

</p>
</details>

## Lambda Calculus

1. For each of the following lambda calculus terms, compute the set of
   free variables and show an alpha-renaming of the term such that no
   variable is bound more than once.
   
   1. *(λ x. x y) z*
   
   1. *(λ x. (λ x. x) x) (λ x. x)*
   
   1. *(λ x. (λ x. x) x) (λ x. x) y*

1. Show the reductions to normal form of the following lambda calculus
   terms, assuming the definitions provided in the class notes:

   1. *`or` `false` `1`*

   1. *`iszero` `1` `2` `3`*
   
   1. *`pred` `1`*
   
   Show each beta-reduction step.

1. Define a lambda term `minus` that takes two Church numerals `m` and
   `n` and returns a Church numeral that encodes the number `max(0,
   m - n)`.
   
## Scheme Programming

1. Write a Scheme function `find-nth` that returns the `n`-th element of
   a list. You may assume that `n` is never greater than the length of
   the list. Examples:
   
   ```scheme
   > (find-nth 1 '(a b c d))
   a
   > (find-nth 2 '(a b c d))
   b
   > (find-nth 4 '(a b c d))
   d
   ```
   
   <details><summary>Solution</summary>
   <p>
     
   ```scheme
   ; Tail-recursive solution (preferable in this case)
   (define (find-nth n xs)
     (if (eq? n 1) (car xs) (find-nth (- n 1) (cdr xs))))
     
   ; Solution with foldl (slightly convoluted)
   (define (find-nth n xs)
     (let ((helper 
            (lambda (hd res) 
              (match res
                [(cons _ 1) (cons hd 0)]
                [(cons x n) (cons x (- n 1))]))))
                
       (car (foldl helper (cons 'whatever n) xs)))) 
   ```
   </p>
   </details>

   
   
1. Write a Scheme function `pack` that packs consecutive elements of a
   list into sublists:
   
   ```scheme
   > (pack '(a a a b c c c c d d))
   ((a a a) (b) (c c c c) (d d))
   > (pack '())
   ()
   ```
   
   <details><summary>Solution</summary>
   <p>
     
   ```scheme
   ; Tail-recursive solution
   (define (pack-helper xs curr-pack packed-xs)
     (match xs 
       [(cons hd tl) (if (equal? hd (car curr-pack))
                         (pack-helper tl (cons hd curr-pack) packed-xs)
                         (pack-helper tl (list hd) (cons curr-pack packed-xs)))]
       ['() (reverse (cons curr-pack packed-xs))]))
       
   (define (pack xs)
     (match xs
       [(cons hd tl) (pack-helper tl (list hd) '())]
       ['() '()]))
       
   ; Solution with foldl
   (define (helper hd res)
     (if (equal? hd (caar res))
         (cons (cons hd (car res)) (cdr res))
         (cons (list hd) res)))

   (define (pack xs)
     (match xs
       [(cons hd tl) (reverse (foldl helper (list (list hd)) tl))]
       ['() '()]))
   ```
   </p>
   </details>
   
1. Write a Scheme function `encode` that computes the length encoding
   of a list (you can use the `pack` function as a subroutine).
   
   ```scheme
   > (encode '(a a a b c c c c d d))
   ((a . 3) (b . 1) (c . 4) (d . 2))
   ```
   
   <details><summary>Solution</summary>
   <p>
     
   ```scheme
   (define (encode xs)
     (let ((packed-xs (pack xs)))
       (map (lambda (pack) (cons (car pack) (length pack))) packed-xs)))
   ```
   </p>
   </details>
   
1. Write a Scheme function `drop` that drops every n-th element from
   a list
   
   ```scheme
   > (drop 3 '(1 2 3 4 5 6 7 8 9 10))
   (1 2 4 5 7 8 10)
   > (drop 3 '(a b c d e))
   (a b d e)
   ```

   <details><summary>Solution</summary>
   <p>
     
   ```scheme
   ; Tail-recursive solution
   (define (drop-helper n k res xs)
     (match xs
       [(cons hd tl)
        (if (eq? k 1)
          (drop-helper n n res tl)
          (drop-helper n (- k 1) (cons hd res) tl))]
       ['() (reverse res)]))

   (define (drop n xs) (drop-helper n n '() xs)) 
   ```
   </p>
   </details>
   
Challenge yourself and try to implement these functions as efficiently
as possible both with respect to run-time/space complexity as well as
code complexity (e.g. use tail-recursion, use implementations based on
`foldl`/`foldr` etc.)

