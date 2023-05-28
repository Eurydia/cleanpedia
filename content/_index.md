---
title: Cleanpedia
---

## Introduction

The motivation of this reference is to create an accessible, concise, and clear documentation for those who are seeking to learn CLEAN.

The same information can be found on:

- [Cloogle](https://cloogle.org/) which is the language’s search engine, and
- [language report](https://cloogle.org/doc/) which describes the syntax and BNF of Clean.

However, this documentation is not official.
Terminologies and explanations may not be entirely true to the references used.

In addition, the language report, which was the primary source of this documentation, was written for the version 2.0 of CLEAN.

If you notice any mistake or have suggestions for improvements, please feel free to contact me through the following channels:

- Email: [b9xp3x@inf.elte.hu](mailto:b9xp3x@inf.elte.hu)
- Instagram: [@\_kornthana](https://www.instagram.com/_kornthana/)
- Telegram channel: [https://t.me/+El6CtwOD8KxhYmU9](https://t.me/+El6CtwOD8KxhYmU9)

## Functions

### Defining A Function

A function definition consists of, at least, one implementation, and a control implementation may be written as follows:

```
// Language: Clean

functionA parameter = expression
```

Multiple parameters are space separated.

```
// Language: Clean

functionB paramA paramB = expression
```

In global scope, right arrow (`=>`) may be used to separate parameters from expression.

```
// Langauge: Clean

// In global scope

functionA parameter     => expression
functionB paramA paramB => expression
```

The simplest functions consist of one implementation.

```
// Language: Clean

negate x = x * (-1)
```

If a function has multiple implementations, they must be grouped together.

```
// Language: Clean

safeDiv m 0 = 0
safeDiv m n = m / n
```

Therefore, the following snippet is illegal.

```
// Language: Clean

safeDiv m 0 = 0
6 + 2 - 1
safeDiv m n = m / n
```

**Implementation signature**

Every implementation must have the same signature as the first.

```
// Language: Clean

badSafeDiv m 0 =  False
badSafeDiv m n =  m / n
```

The second implementation of `badSafeDiv` disobeyed the signature of the first implementation by returning an integer, instead of a Boolean value.

**Implementation selection order**

Implementations are tried in textual order.
An implementation is selected if arguments of a function call matches with its parameters.

```
// Language: Clean

safeDiv 9 6  // False
```

In the first implementation, parameter $m$ matches with argument $9$, but parameter $0$ does not match with argument $6$.
Thus, the first implementation is not selected.

In the second implementation, both parameters match with both arguments.
Thus, the second implementation is selected for evaluation.

It should be noted that; if implementation order is changed, then the function would have unintended behaviors.

```
// Language: Clean

safeDivAlt m n = m / n
safeDivAlt m 0 = 0
```

The second implementation is never reached.
Even if it is invoked with $0$ as the second argument.

```
// Language: Clean

safeDivAlt 9 0  // Uh oh
```

With the current implementation of `safeDivAlt`, it is clear that the second implementation is never reached.

It is important to recognize that, this parameter-argument matching is not the same as performing equality checks.

### Guarded expressions

One or more guarded expressions can be introduced to an implementation.
Guarded expressions allow an implementation to have multiple bodies, instead of one.

A control implementation with one guarded body may be written as follows:

```
// Language: Clean

functionC parameter
| guard = expresssion
```

An implementation with multiple guarded bodies may be written as follows:

```
// Language: Clean

functionD parameter
| guardA = expressionA
| guardB = expressionB
| guardC = expressionC
```

Additionally, guarded bodies can be nested.

```
// Language: Clean

functionE parameter
| guardA
    | guardAA    = expressionAA
    | guardAB    = expressionAB
```

**Guarded expression signature**

A guard evaluates to a Boolean value.

```
// Language: Clean

signum 0 =  0
signum n
| n > 0  =  1
| n < 0  = -1
```

The definition of `signum` has two implementations, and its second implementation has two guarded expressions.

The guarded expressions must obey the same signature rule as its implementation.

```
// Language: Clean

badSignum 0 =  0
badSignum n
| n > 0     =  True
| n < 0     = -1
```

According to first implementation of `badSignum`, every implementation must return an integer.
However, one of the guarded expression returns a Boolean value, which is not allowed.

`badSignum` can be rewritten to have only one implementation.

```
// Language: Clean

badSignumAlt n
| n == 0    =  0
| n >  0    =  True
| n <  0    = -1
```

Unfortunately, this new definition is still illegal.
The first guarded expression forces others to return an integer, but the second returns a Boolean value.

**Guarded expression selection order**

Guarded expressions are tried in textual order, but only after their implementation is chosen.

That is, if `signum` is called with $0$, then parameter $0$ of the first implementation matches with argument $0$.
Thus, the first implementation is selected.

```
// Language: Clean

signum 0
```

In this call, none of the guards were tried because the implementation that they belong to is not selected.

Instead, if `signum` is called with a non-zero integer, the first implementation fails to match.
The second implementation is then selected.

```
// Language: Clean

signum -9
```

The first guard ($n \gt 0$) is tried.
It evaluates to false, and its expression is not selected.

The second guard is tried ($n \lt 0$).
It evaluates to true, and its expression is selected.

Similarly, the same happens for nested guarded expressions.

```
// Language: Clean

someFuncA parameter = expression
someFuncA parameter
| guardA
    | guardAA       = expressionAA
```

In `someFuncA` defined above, for `expresssionAA` to be selected, the second implementation must be selected, as well as, `guardA` and `guardAA` must evaluate to true.

In addition, `otherwise` keyword may used in place of a guard.
It always evaluates to true.

```
// Language: Clean

signumAlt 0 =  0
signumAlt n
| n > 0     =  1
| otherwise = -1
```

The order of the guarded bodies also matters.

```
// Language: Clean

badSignumAlt 0 =  0
badSignumAlt n
| otherwise    = -1
| n > 0        =  1
```

### Partial functions

Partial functions result in a run-time error when called with values outside of its domain.

```
// Language: Clean

fibPartial 1 = 1
fibPartial 2 = 1
fibPartial n = fibPartial (n - 1) + fibPartial (n - 2)
```

The definition of `fibPartial` is partial.
It results in a run-time error when called any integer less than one.

Implementations with guarded expressions can be partial as well.

```
// Language: Clean

fibPartialAlt n
| n == 1    = 1
| n == 2    = 1
| otherwise = fibPartialAlt (n - 1) + fibPartialAlt (n - 2)
```

By extending its domain, a partial function can be transformed into a complete function.

```
// Language: Clean

fibComplete n
| n <  0    = 0
| n == 1    = 1
| n == 2    = 1
| otherwise = fibComplete (n - 1) + fibComplete (n - 2)
```

However, this solution might not make semantic sense to do so.
Alternatively, `abort` from `StdMisc` can be used to solve the semantic issue.

```
// Language: Clean
fibCompleteAlt n
| n <= 0    = abort "fibCompleteAlt called outside of domain"
| n == 1    = 1
| n == 2    = 1
| otherwise = fibCompleteAlt (n - 1) + fibCompleteAlt (n - 2)
```

`abort` terminates the execution of the program with a custom error message.
`fibComplete` still works as intended, but it will stops the program and throws an error

```
fibCompleteAlt called outside of domain
```

when it is called with $n\le{0}$.

The module `StdMisc` is discussed in more details [here](/cleanpedia/appendix-a/stdmisc).

### Operators

Operators are arity-two functions.
They can be applied in infix position or invoked like ordinary functions.

```
// Language: Clean

1 + 1  // applied as an operator
```

To invoke an operator as an ordinary function, the operator name must be placed inside parentheses, and in front of its arguments.

```
// Language: Clean

(+) 1 1  // invoked as an ordinary function
```

When applied in infix position, both arguments must be given.
Operators can be curried, but only when they are invoked as ordinary functions.

**Operator precedence**

The precedence determines how tightly an operator binds to its argument.
Precedence can be between $0$ and $9$ with higher number having higher precedence.

The precedence of an operator is $9$ by default.

**Operator fixity**

The fixity is important when evaluating two operators of the same precedence.
There are two relevant fixities:

- `infixl` for left-associated operators, and
- `infixr` for right-associated operators.

The fixity of an operator is `infixl` by default.

**Defining an operator**

An operator can be defined by placing its name between parentheses.
It can be implemented as if it was an ordinary function.

```
// Language: Clean

(operatorA) paramL paramR =  expression
```

In global scope, right arrow (`=>`) may be used to separated parameters from expression, much like a function.

```
// Language: Clean

// In global scope

(operatorB) paramL paramR => expression
```

**Operator conflict**

Operators can conflict with one another.

```
// Language: Clean

(<=>) infixl 9 :: Bool  Bool  -> Bool
(<=>)          :: True  True  =  True
(<=>)          :: False False =  True
(<=>)          :: _     _     =  False

(-->) infixr 9 :: Bool  Bool  -> Bool
(-->)          :: False True  =  False
(-->)          :: _     _     =  True
```

It is not allowed to apply operators with equal precedence in an expression in such a way that their fixity conflict.

```
// Language: Clean

True --> False <=> False
```

`-->` operator is a right-associated.
It implies that the expression should be evaluated as follows.

```
// language: Clean

True --> (False <=> False)
```

However, `<=>` operator is left associated.
It implies that the expression should be evaluated as follows.

```
// language: Clean

(True -> False) <=> False
```

Since both operators have the same precedence and the order of evaluation cannot be decided by their fixity, this expression will result in a compile-time error.

### Lambda Functions

A lambda function is defined "on the spot" as an expression.
However, it cannot have local definitions or guards.

A lambda function has following syntax.

```
// Language: Clean

\[fnParam] =  [fnBody]
\[fnParam] -> [fnBody]
```

Parameters of a lambda function are separated by spaces.

```
// Language: Clean

\paramA        = ...
\paramA paramB = ...
```

### Case Expressions

In a $\textbf{case..in}$ expression, cases are tried in textual order.
Each case contains a pattern and a case body.

```
// Language: Clean

case [expr] of
[casePatternA] =  [caseBodyA]
[casePatternB] =  [caseBodyB]
[casePatternC] =  [caseBodyC]

case [expr] of
[casePatternX] -> [caseBodyX]
[casePatternY] -> [caseBodyY]
[casePatternZ] -> [caseBodyZ]
```

Guards can be introduce to a case which allows a case to have multiple guarded bodies.

```
// Language: Clean

case [expr] of
[patternA]
| [guardA] = [bodyAA]
| [guardB] = [bodyAB]
| [guardC] = [bodyAC]
[patternB] = [bodyB]
```

Internally, a $\textbf{case..in}$ expression is translated to a function definition.
The cases are transformed into an implementation.

```
// language: Clean

_caseFn [casePatternA] = [caseBodyA]
_caseFn [casePatternB] = [caseBodyB]
_caseFn [casePatternC] = [caseBodyC]
```

Consequently, this can result in a run-time error when none of pattern matches since the expression is translated into a partial function.

### Pattern-Match Expressions

A pattern-match expression checks an expression against a pattern or not.

```
// Language: Clean

[expr] := [pattern]
```

Internally, it compiles down to a $\textbf{case..of}$ expression.
It yields $\textbf{True}$ or $\textbf{False}$ similar to an equality check.

```
// Language: Clean

case [expr] of
[pattern] = True
_         = False

```

It is useful when dealing with algebraic types.

```
// Language: Clean

:: Tree a = Node a (Tree a) (Tree a)
          | Leaf

isLeaf :: (Tree a) -> Bool
isLeaf    t        =  t := (Leaf)

isNode :: (Tree a) -> Bool
isNode    t        =  t := (Tree x l r)
```

### Local Definitions

#### Let Expressions

A $\textbf{let}..\textbf{in}$ expression introduces a new scope inside an expression.

```
// Language: Clean

let
    localX  = ...
    localY = ...
in expr
```

Anything defined inside a $\textbf{let}..\textbf{in}$ expression only has meaning in $\text{expr}$.

```
// Language: Clean

[let 
    a = i * i 
in (a, a) \\ i <- [0..n]]
```

#### Where Blocks

A $\textbf{where}$ block can be added at the end of a function implementation which introduces a new scope.

```
// Language: Clean

[fnName] [fnParam]
| [guardA] = [bodyA]
| [guardB] = [bodyB]
| [guardC] = [bodyC]
where
    localX = [expr]
```

For example:

```
// Language: Clean

absolute :: Int -> Int
absolute    0   =  z  // 1st impl
where
    z :: Int
    z =  0
absolute    n         // 2nd impl
| n <  0        =  negN
| otherwise     =  n
where
    negN :: Int
    negN =  n * (-1)
```

The second implementation does not have access to $\text{z}$ which is defined in the first implementation.

#### With Blocks

A $\textbf{with}$ block can be added at the end of a guarded body which introduces a new scope.

```
// language: Clean

[fnName] [fnParam]
| [guardA] = [bodyA]
with
    localX = [expr]
| [guardB] = [bodyB]
with
    localY = [expr]
| [guardC] = [bodyC]
```

For example:

```
// Language: Clean

absolute :: Int -> Int
absolute    n
| n == 0        =  0
| n <  0        =  negN
with
    negN :: Int
    negN =  n * (-1)
| otherwise     =  n
```

The third guarded body does not have access to $\text{negN}$, which is local to the second guarded body.

---

## Built-In Types

Certain types like integers, Booleans, characters, real numbers, lists, tuples and arrays are frequently used that they have been predefined for reasons of efficiency and convenience.

### Primitive Types

#### Integers

**Type annotation**: $\textbf{Int}$

**Constructors**

From decimal notation:

```
// Language: Clean

n :: Int
n = -13
n =  0
n =  13
```

From octal notation by prefixing octal digits with $\textbf{0}$:

```
// Language: Clean

n :: Int
n = -015  // dec -13
n =  0
n =  015  // dec  13
```

From hexadecimal notation by prefixing hexadecimal digits with $\textbf{0x}$:

```
// Language: Clean

n :: Int
n = -0xD  // dec -13
n =  0
n =  0xd  // dec  13
```

More information about built-in operations and functions on integers can be found on [Appendix A: StdInt](appendix-a/stdint).

#### Real Numbers

**Type annotation**: $\textbf{Real}$

**Constructors**

From decimal notation:

```
// Language: Clean

n :: Real
n = -1.3
n =  0.0
n =  1.3
```

From scientific notation:

```
// Language: Clean

n :: Real
n = -13E-2  // -0.13
n =  0E0    //  0
n =  13E-2  //  0.13
```

More information about built-in operations and functions on real numbers can be found on [Appendix A: StdReal](appendix-a/stdreal).

#### Booleans

**Type annotation**: $\textbf{Bool}$

**Constructors**

```
// Language: Clean

b :: Bool
b =  True
b =  False
```

More information about built-in operations and functions on Booleans can be found on [Appendix A: StdBool](appendix-a/stdbool).

#### Characters

**Type annotation**: $\textbf{Char}$

**Constructors**

```
// Language: Clean

c :: Char
b =  'a'
b =  '9'
b =  'Z'
b =  '+'
```

More information about built-in operations and functions on characters can be found on [Appendix A: StdChar](appendix-a/stdchar).

#### Parameter-Matching Primitive Types

Constants of primitive types can be specified as pattern.

Using integer constants as pattern:

```
// Language: Clean

fib :: Int -> Int
fib    1   =  1
fib    2   =  1
fib    n   =  fib (n - 1) + fib (n - 2)
```

Using character constants as pattern:

```
// Language: Clean

isLetterA :: Char -> Bool
isLetterA    'a'  =  True
isLetterA    'A'  =  True
isLetterA     _   =  False
```

### Lists

#### Defining A List

**Type annotation**:
$[\textbf{Int}]$,
$[\textbf{Char}]$,
$[\textbf{T}]$,
et cetera.

A list can contain an infinite number of elements.
All elements must be of the same type.

**Constructors**

From explicit enumeration of the elements:

```
// Language: Clean

[1, 3, 5, 7, 9]
[1 : [3, 5, 7, 9]]
[1, 3, 5 : [7, 9]]
[1 : [3 : [5 : [7 : [9 : []]]]]]
[1 : 3 : 5 : 7 : 9 : []]
```

From implicit enumeration with $\textbf{dot-dot}$ expression:

```
// Language: Clean

[1..]       // infitite list [1, 2, 3, ...]
[1, 3..]    // infinite list [1, 3, 5, ...]
[1..5]      // [1, 2, 3, 4, 5]
[1, 3..10]  // [1, 3, 5, 7, 9]
```

It should be noted that $\textbf{dot-dot}$ expressions requires $\text{StdEnum}$ module.

From list comprehension:

```
// Language: Clean

// extract from a list
[el \\ el <- list]

// extract from an array
[el \\ el <-: array]

// cartesian product
[(x, y) \\ x <- xs , y <- ys]

// pair-wise zip
[(x, y) \\ x <- xs & y <- ys]

// same as filter
[x \\ x <- xs | P x]

// nested
[(x, y) \\ x <- xs, y <- [1..x]]
```

A special notation for constructing a list of characters is also provided:

```
// Language: Clean

['a', 'b', 'c']
['abc']
['ab','c']
```

More information about built-in operations and functions on lists can be found on:

- [Appendix A: StdCharList](appendix-a/stdcharlist),
- [Appendix A: StdList](appendix-a/stdlist), and
- [Appendix A: StdOrdList](appendix-a/stdordlist).

#### List Patterns

Lists can be specified as patterns as follow:

```
// Language: Clean

getFst :: [T]       -> T
getFst    [x, y, z] =  x

getSnd :: [T]       -> T
getSnd    [x, y, z] =  y

getThd :: [T]       -> T
getThd    [x, y, z] =  z
```

The results of these function calls are as expected:

```
// Language: Clean

getFst [1, 2, 3]  // 1
getSnd [1, 2, 3]  // 2
getThd [1, 2, 3]  // 3
```

However, they will result in a run-time error if it is invoked with a list which does not have exactly three elements.

```
// Language: Clean

getFst [1]          // NOT OK :(
getSnd [1, 2]       // NOT OK :(
getThd [4, 3, 2, 1] // NOT OK :(
```

To remedy this issue, an addition element should be introduce.

```
// Language: Clean

getFstAny :: [T]     -> T
getFstAny    [x : r] =  x

getSndAny :: [T]        -> T
getSndAny    [x, y : r] =  y

getThdAny :: [T]           -> T
getThdAny    [x, y, z : r] =  z
```

The right-hand side of colon ($:$) matches with any number of elements, including zero.
It is worth noting that $r$ is always a list.

```
// Language: Clean

getFstAny [1]           // x = 1
                        // r = []

getFstAny [1, 2]        // x = 1
                        // r = [2]

getFstAny [4, 3, 2, 1]  // x = 4
                        // r = [3, 2, 1]
```

However, the second function still requires the list to have at least two elements.

```
// Language: Clean

getSndAny [1]           // NOT OK :(

getSndAny [1, 2]        // x = 1
                        // y = 2
                        // r = []

getSndAny [4, 3 ,2 ,1]  // x = 4
                        // y = 3
                        // r = [2, 1]
```

Similarly, the third function requires a list with at least three elements.

```
// Language: Clean

getThdAny [1]           // NOT OK :(

getThdAny [1, 2]        // NOT OK :(

getThdAny [4, 3, 2, 1]  // x = 4
                        // y = 3
                        // z = 2
                        // r = [1]
```

### Tuples

#### Defining A Tuple

**Type annotation**:
$(\textbf{T},\ \textbf{K})$,
$(\textbf{T},\ \textbf{K},\ \textbf{V})$,
$(\textbf{T},\ \textbf{K},\ \textbf{V},\ \textbf{E})$
et cetera.

A tuple contains finite number of elements.
Elements do not have to be the same type.
Every type appears in a tuple must be specified, and singleton tuples are not allowed.

**Constructors**

```
// Language: Clean

A :: (Int, Char)
A =  (49, '1')

B :: (Real, Bool, String)
B =  (0.2, False, "Hi")

C :: (Int)
C =  (2, 6)  // NOT OK should be (Int, Int)

D :: (Int)
D =  (2)     // NOT OK
```

More information about built-in operations and functions on lists can be found on:

- [Appendix A: StdTuple](appendix-a/stdtuple).

#### Tuple Patterns

Tuples can be used as patterns in a similar way to lists.
However, colon ($:$) is not allowed in tuple patterns.

```
// Language: Clean

getFst :: (T, K, V) -> T
getFst    (x, y, z) =  x

getSnd :: (T, K, V) -> K
getSnd    (x, y, z) =  y

getThd :: (T, K, V) -> V
getThd    (x, y, z) =  z
```

The results of these function calls are as expected:

```
// Language: Clean

getFst (1, 'a', 1.0)  // 1
getSnd (1, 'a', 1.0)  // 'a'
getThd (1, 'a', 1.0)  // 1.0
```

### Arrays

#### Defining An Array

**Type annotation**:
$\{\textbf{Int}\}$,
$\{\textbf{Char}\}$,
$\{\textbf{T}\}$,
et cetera.

An array contains a finite number of elements.
Elements of an array have to be of the same type.

**Constructors**

```
// Language: Clean

{1, 2, 3, 4}
{1.0, 2.0, 3.0, 4.0}

"abc"
// equivalent to
// {'a', 'b', 'c'}
```

An array can be constructed from comprehension by surrounding a comprehension with $\{\ldots\}$.

More information about built-in operations and functions on lists can be found on:

- [Appendix A: StdArray](appendix-a/stdarray), and
- [Appendix A: StdString](appendix-a/stdstring).

---

## Defining New Types

As a strongly typed language, every object and function in CLEAN has a type.
The basic can be extended with algebraic types, record types, abstract types and synonym types.

New types can only be defined on the global level.

### Algebraic Data Types

#### Defining An Algebraic Data Type

An algebraic data type introduces a new data structure and a constructor.

It has the following syntax.

```
// Language: Clean

:: [tName] = [tConstructor]
```

An algebraic data type can have multiple constructors.
Constructors defined in the same global scope must have names.
Constructors must be separated by a vertical bar ($|$).

```
// Language: Clean

:: [tName] = [tConstructorA] | [tConstructorB] | [tConstructorC]
```

For readability, constructors may be placed on different lines.

```
// Language: Clean

:: [tName] = [tConstructorA]
           | [tConstructorB]
           | [tConstructorC]
```

For example, a basic algebraic type can be defined as follows.

```
// Language: Clean

:: Mood = Happy | Sad
```

Constructors also accept types as arguments, but they must be declared on the left-hand side.
Once declared, these generic types can be referred to by any of its constructors.

```
// language: Clean

:: [tName] T K = [tConstructorA] T
               | [tConstructorB] K
               | [tConstructorC] T K
```

For example, a binary tree, which is has a recursive structure can be defined as follows.

```
// language: Clean

:: Tree = Branch Tree Tree
        | Leaf
```

An infix constructor is defined by surrounding its name with parentheses.
They must have an arity of two.
The precedence and fixity of an such constructor follows that of an operator, which is discussed in Chapter I.

```
// Language: Clean

:: [tName] T K =  [tConstructorA]  T
               |  [tConstructorB]  K
               | ([tConstructorC]) T K

:: [tName] T K =  [tConstructorA]  T
               |  [tConstructorB]  K
               | ([tConstructorC]) [fixity] [precedence] T K
```

For example, the $\text{Branch}$ constructor can be rewritten as an infix constructor.

```
// language: Clean

:: TreeInfix = (/\) infixr 0 Tree Tree
             | Leaf
```

The precedence and fixity of an infix constructor can be omitted.
The default precedence is $9$, and the default fixity is $\textbf{infixl}$.

```
// language: Clean

:: TreeInfix = (/\) Tree Tree
             | Leaf
```

#### Creating Objects Of Algebraic Data Types

Objects of simple algebraic data types are created by invoking one of its constructors.

```
// Language: Clean

:: Mood = Sad | Happy

A :: Mood
A =  Happy

B :: Mood
B =  Sad
```

If a constructor accepts arguments, it should be invoked with appropriate arguments.

```
// Language: Clean

:: Tree = Branch Tree Tree
        | Leaf

A :: Tree
A =  Leaf

B :: Tree
B =  Branch Leaf Leaf

C :: Tree
C =  Branch Leaf (Branch Leaf Leaf)
```

Infix constructors behave in a similar way.

```
// language: Clean

:: TreeInfix = (/\) TreeInfix TreeInfix
               | Leaf

A :: TreeInfix
A =  Leaf

B :: TreeInfix
B =  Leaf /\ Leaf

C :: TreeInfix
C =  (/\) Leaf Leaf

D :: TreeInfix
D =  Leaf /\ (Leaf /\ Leaf)
```

#### Algebraic Data Type Patterns

To use a algebraic data type as a pattern, use its constructors.

```
// Language: Clean

:: Mood = Sad | Happy

isHappy :: Mood    -> Bool
isHappy    (Happy) =  True
isHappy    _       =  False
```

With the same process, complex algebraic data types can be used as patterns as well.

```
// Language: Clean

:: Tree = Branch Tree Tree
        | Leaf

isBranch :: Tree           -> Bool
isBranch    (Branch l r)   =  True
isBranch    _              =  False

:: TreeInfix = (/\) TreeInfix TreeInfix
               | Leaf

isBranchInfix :: TreeInfix -> Bool
isBranchInfix   (l/\r)     =  True
isBranchInfix   _          =  False
```

### Record Types

A record type is an algebraic data type in which exactly one constructor is defined.
A field name is attached to each of the arguments of the constructor.

#### Defining A Record Type

A record type is a tuple-like algebraic data structure that has the advantage that its elements can be selected by field name rather than by position.

A simple record type can be defined as follows.

```
// Language: Clean

:: [tName] = { [fieldName] :: [fieldType] }
```

Fields must be separated commas ($,$).

```
// Language: Clean

:: [tName] = { [fNameA] :: [fTypeA] , [fNameB] :: [fTypeB] , [fNameC] :: [fTypeC]  }
```

To increase readability, fields can be placed on different lines.

```
// Language: Clean

:: [tName] = { [fNameA] :: [fTypeA]
             , [fNameB] :: [fTypeB]
             , [fNameC] :: [fTypeC]
             }
```

For example, a record type for representing complex numbers can be defined as follows.

```
// Language: Clean

:: Complex = { re :: Real
             , im :: Real
             }
```

#### Creating Objects Of Record Types

The field names are case-sensitive, but the field order does not matter.
Every field of a record type must be given.

```
// Language

:: Complex = { re :: Real
             , im :: Real
             }

A :: Complex
A =  { re = 1.0 , im = 0.0 }

B :: Complex
B =  { im = 1.0 , re = 0.0}
```

The name of a record type can be added to the constructor.
In such a case, the type annotation can be omitted.

```
// Language: Clean

:: Point = { x :: Int
           , y :: Int
           }

:: Position = { x :: Int
              , y :: Int
              }

// A :: Point
// A =  { x = 0 , y = 0 }
A =  { Point | x = 0 , y = 0 }

// B :: Position
// B =  { x = 0 , y = 0 }
B =  { Position | x = 0 , y = 0 }
```

**Record update**

A new record object can be constructed from an existing one.

```
// Language: Clean

{ [oldObject] & [fName] = [newValue] }
```

Multiple fields can be updated all at once.

```
// Language: Clean

{ [oldObject] & [fNameA] = [newValueA] , [fNameB] = [newValueB] }
```

For example:

```
// Language: Clean

:: PosThree = { x :: Int
              , y :: Int
              , z :: Int
              }

A :: PosThree
A =  { x = 0 , y = 0 , z = 0 }  // (PosThree 0 0 0)

B :: PosThree
B =  { A & x = 1 , y = 1 }      // (PosThree 1 1 0)
```

#### Record Type Patterns

An object of type record can be specified as pattern.
Not every field must be used.

```
// Language: Clean

:: PosThree = { x :: Int
              , y :: Int
              , z :: Int
              }

isZeroX :: PosThree -> Bool
isZeroX    { x=0 }  =  True
isZeroX    _        =  False

A :: PosThree
A =  { x = 0 , y = 0 , z = 0 }

isZeroX A  // True

B :: PosThree
B =  { A & x = 1 , y = 1 }

isZeroX B  // False
```

Alternatively, record fields can be extracted without patterns.

```
// Language: Clean

:: PosThree = { x :: Int
              , y :: Int
              , z :: Int
              }

isZeroY :: PosThree -> Bool
isZeroY    { y=r }    =  r == 0
```

In the implementation above, the record field $y$ is extracted to $r$.
It can be used referred by the body.

Record fields can be extracted to an identifier of the same name.

```
:: PosThree = { x :: Int
              , y :: Int
              , z :: Int
              }

isZeroZ :: PosThree -> Bool
isZeroZ    { z }    =  z == 0
```

The implementation above is equivalent to the following:

```
// Language: Clean

isZeroZ :: PosThree -> Bool
isZeroZ    { z=z }  =  z == 0
```

**Record field selection**

The value of a record field can be selected using dot operator ($.$).

```
// Language: Clean

[recordObj].[fName]
```

For example, a simple getter function on $\textbf{Point}$ can be defined as follows.

```
:: Point = { x :: Int
           , y :: Int
           }

getX :: Point -> Int
getX    p     =  p.x

A :: Point
A =  { x = 1 , y = 0 }

getX a  // 1
```

### Synonym Types

Synonym types allow for an introduction of a new type name for an existing type.

#### Defining A Synonym Type

A simple synonym type has the follow syntax:

```
// Language: Clean

:: [tName] :== [existingType]
```

For example, an array of characters can be viewed as a string.

```
// Language: Clean

:: String :== {Char}
```

Synonym types also accept generic types as arguments.

```
// Language: Clean

:: [tName] T K :== [existingType] T K
```

For example, an arity-two operator can be shortened with a synonym type.

```
// Language: Clean

:: Op T :== T T -> T

// add :: Int Int -> Int
add :: (Op Int)
```

---

## Overloading

### Overloaded Functions

Functions and operators are defined on built-in types.
However, when interacting with custom data types, they do not have any defined function or operator.

That is, adding two objects of $\textbf{Complex}$ type is not allowed.

```
// Language: Clean

:: Complex = { re :: Real
             , im :: Real
             }

a :: Complex
a =  { re = 0.0 , im = 1.0 }

b :: Complex
b =  { re = 0.0 , im = 1.0 }

a + b  // NOT OK :(
```

This happens because addition is not defined on $\textbf{Complex}$ type.

In this case, addition can be overloaded on $\textbf{Complex}$ type.

```
// Language: Clean

instance + Complex
where
    (+) x y = { Complex | re=(x.re + y.re) , im=(x.im + y.im)}

a + b  // (Complex 0.0 2.0)
```

Only after overloading the addition operation on $\textbf{Complex}$ that the addition is allowed.
In other word, the complier now knows the meaning of complex number addition.

A list of functions and operations which can be overloaded can be found on [Appendix A: StdOverloaded](appendix-a/stdoverloaded).

#### Defining An Overloaded Function Or Operator

To overload the built-in functions and operations, the general syntax is as follows.

```
// Language: Clean

instance [clsName] [typeVars]
where
    [fnImplementation]
    [opImplementation]
```

Functions and operators can be defined in terms of existing overloaded functions.
This is especially useful when dealing with generic types.

```
// Language: Clean

increment :: T -> T
increment    x =  x + 1
```

It is adding an object of type $\textbf{T}$ and $1$ which is an integer.
Unless the addition is instantiated for mentioned types, this implementation would not work as intended.

One way to ensure that it works for all type is to introduce class context.

```
increment :: T -> T | + T & one T
increment    x =  x + one
```

The new implementation requires that the addition operation, as well as, $\textbf{one}$ unit is defined on a generic type $\textbf{T}$.

In this case, the function is defined in terms of an overloaded function and a unit.

Such declaration has the following syntax.

```
// Language: Clean

[fnName] :: [typeVars] -> [typeVar] | [clsConstraints]
```

Class constraints are separated by ampersands ($\&$).

### Classes

A class gives name to a group of overloaded functions and operators.
These functions and operators are members of the class.

```
// Language: Clean

class Equal T
where
    (==) infixl 4 :: T T -> Bool
    (<>) infixl 4 :: T T -> Bool
```

The class $\text{Eq}$ has two members.
They are equality and inequality operators.

To instantiate a class, all of its members must be implemented.

```
// Language: Clean

instance Equal Complex
where
    (==) x y = (x.re == y.re) && (x.im == y.im)
    (<>) x y = (x.re <> y.re) || (x.im <> y.im))
```

A list of classes can be found on [Appendix A: StdClass](appendix-a/stdclass).

#### Defining A Class

As shown above, a class simple provides a name to collection of logically-linked functions and operations.

It can be defined with the following syntax.

```
// Language: Clean

class [clsName] [typeVars]
where
    [fnDeclaration]
    [opDeclaration]
```

Type variables declared can be referred to by all of its member, but they must be given as arguments when the class is instantiated.

```
// Language: Clean

class MinMaxC T K
where
    minC :: T T -> K
    maxC :: T T -> K

instance MinMax Real Int
where
    minC x y = toInt (min x y)
    maxC x y = toInt (max x y)
```

---

## Appendix A: Standard Environment

More information about Standard Environment can be found [here](appendix-a/intro.md)