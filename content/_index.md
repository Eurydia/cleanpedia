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

---

## Functions

Functions are crucial in CLEAN.
That is, an operator is an arity-two function which can be called between its arguments and a constant value is a arity-zero function.

Everything is a function in one way or another.

In this Section, concepts regarding functions are discussed in details.

### Defining a Function

In its purest form, a function definition consists of at least one implementation.
A control implementation may be written as follows.

```
// Language: Clean
 
functionA parameter = expression
```

A function implementation has three components; 
- name (`functionA`),
- parameters (`parameter`), and
- expression or body (`expression`).

Parameters are space separated.

```
// Language: Clean

functionB paramA paramB = expression
```

#### Example Implementation

The simplest functions consist of one implementation.

```
// Language: Clean

negate x = x * (-1)
```

If a function has multiple implementations, they must be grouped together.

```
// Language: Clean

safeDivision m 0 = 0
safeDivision m n = m / n
```

Therefore, the following snippet is illegal.

```
// Language: Clean

safeDivision m 0 = 0
6 + 2 - 1
safeDivision m n = m / n
```

Notice the expression separating the implementations of `safeDivision`.

#### Implementation Signature Rule

Every implementation must have the same signature as the first.

Implementations of `safeDivision` followed this rule.
They accepts two integers (inferred) and returns an integer as a result.

To demonstrate the effect, implementations of `badSafeDivision` are written in such a way that they break this rule.

```
// Language: Clean

badSafeDivision m 0 = False
badSafeDivision m n = m / n
```

The first implementation accepts two integers as its argument and returns a Boolean value.
It dictates that subsequent implementations of `badSafeDivision` must have the same signature.

However, the second implementation does not.
Instead, it accepts two integers, but returns an integer instead of a Boolean value.

#### Implementation Selection Rule

When a function has multiple implementation, a rule exists to determine which implementation will be evaluated.

The rule states that implementations are tried in textual order, from top to bottom.
An implementation is selected if arguments of a function call matches with its parameters.

To demonstrate, `safeDivsion` is called with nine and six respectively.

```
// Language: Clean

safeDivision 9 6
```

The first implementation is tried, the first parameter (`m`) matches with everything including nine since it is a wildcard.

| Parameter | Argument | Result   |
| --------- | -------- | -------- |
| `m`       | `9`      | Matching |

The second parameter (`0`) does not match with the second argument (`6`).

| Parameter | Argument | Status       |
| --------- | -------- | ------------ |
| `m`       | `9`      | Matching     |
| `0`       | `6`      | Not matching |

The trial for the first implementation has completed.
It will not be chosen for evaluation.

Next, the second implementation is tried.
Since both parameters wildcards, they will match with both arguments.

| Parameter | Argument | Result   |
| --------- | -------- | -------- |
| `m`       | `9`      | Matching |
| `n`       | `6`      | Matching |

Thus, the second implementation is chosen for evaluation.

It should be noted that; if implementation order is changed, then the function would have unintended behaviors.

To demonstrate, implementations of `badSafeDivision` are written in such an order that they will produce unintended effects.

```
// Language: Clean

badSafeDivision m n = m / n
badSafeDivision m 0 = 0
```

It is clear from the example; the second implementation is never reached.
Even if `badSafeDivision`  is called with `0`.

```
// Language: Clean

badSafeDivAlt 9 0  // Uh oh
```

Note that, this rule is not the same as performing equality checks.

If the equality operation is not implemented on a type, this rule would still work.
This concept of user-defined types is discussed in later Sections.

### Guarded Expressions

So far, implementations has one body or expression.

Guarded expressions may be introduced to an implementation, which allow it to have multiple expression.

A control implementation with one guarded expression may be written as follows.

```
// Language: Clean

functionA parameter
| guard = expresssion
```

A guarded expression, as the name suggests, has two components:
- guard (`guard`) which evaluates to a Boolean value, and
- expression (`expression`).

An implementation with multiple guarded bodies may be written as follows.

```
// Language: Clean

functionA parameter
| guardA = expressionA
| guardB = expressionB
| guardC = expressionC
```

Additionally, guarded bodies can be nested.

```
// Language: Clean

functionA parameter
| guardA
    | guardAA = expressionAA
    | guardAB = expressionAB
```

#### Guarded Expression Signature Rule

Similar to the implementation signature rule discussed earlier, expressions must follow the type of the first implementation.

To demonstrate, a good definition of a `signum` function may be written as follows.

```
// Language: Clean

signum 0 =  0
signum n
| n > 0  =  1
| n < 0  = -1
```

The current definition is valid since implementations accepts one integer as argument and returns an integer.

The following definition, however, disobeys the rule of signature.

```
// Language: Clean

badSignum 0 =  0
badSignum n
| n > 0     =  True
| n < 0     = -1
```

The first implementation of `badSignum` dictates that every implementation must return an integer, but a guarded expression returns a Boolean value.

Furthermore, `badSignum` can be rewritten to have only one implementation.

```
// Language: Clean

badSignumAlt n
| n == 0 =  0
| n >  0 =  True
| n <  0 = -1
```

Unfortunately, this new definition is still illegal.
The first guarded expression dictates subsequent guarded expressions  to return an integer, but the second returns a Boolean value.

#### Guarded Expression Selection Rule

Similar to implementation selection rule discussed earlier, guarded expressions are tried in textual order, but only after their implementation is chosen.

To demonstrate, `signum` is called with zero.

```
// Language: Clean

signum 0
```

The first implementation is tried, the first parameter matches with the argument.

| Parameter | Argument | Result   |
| --------- | -------- | -------- |
| `0`       | `0`      | Matching |

The first implementation is chosen.

In this call, none of the guards was tried because the implementation that they belong to is not reached.

Instead, to demonstrate, `signum` is called with non-zero integer.

```
// Language: Clean

signum -9
```

The first implementation fails to match.

| Parameter | Argument | Result       |
| --------- | -------- | ------------ |
| `0`       | `-9`     | Not matching |

But the second implementation will match.

| Parameter | Argument | Result   |
| --------- | -------- | -------- |
| `n`       | `-9`     | Matching |

Each guard of the second implementation is tried in order.
The first guard to yield true will be chosen.

The first guard (`n > 0`) is tried.
It evaluates to false, and its expression is not selected.

| Guard   | Result |
| ------- | ------ |
| `n > 0` | False  |

The second guard is tried (`n < 0`).

| Guard   | Result |
| ------- | ------ |
| `n > 0` | False  |
| `n < 0` | True  |

It evaluates to true, and its expression is chosen.

Similarly, the same happens for nested guarded expressions.

```
// Language: Clean

functionA parameter = expression
functionA parameter
| guardA
    | guardAA       = expressionAA
```

In `functionA`, for `expresssionAA` to be selected, the second implementation must be selected, as well as, `guardA` and `guardAA` must evaluate to true.

In addition, `otherwise` keyword may used in place of a guard.
It always evaluates to true.

```
// Language: Clean

signumAlt 0 =  0
signumAlt n
| n > 0     =  1
| otherwise = -1
```

The order of the guarded bodieexpressions also matters.

```
// Language: Clean

badSignumAlt 0 =  0
badSignumAlt n
| otherwise    = -1
| n > 0        =  1
```

### Partial Functions

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

fibPartial n
| n == 1    = 1
| n == 2    = 1
| otherwise = fibPartial (n - 1) + fibPartial (n - 2)
```

By extending its domain, a partial function can be transformed into a complete function.

```
// Language: Clean

fibComplete n
| n <= 0    = 0
| n == 1    = 1
| n == 2    = 1
| otherwise = fibComplete (n - 1) + fibComplete (n - 2)
```

However, this fix might not make semantic sense to do so.
Alternatively, `abort` from `StdMisc` can be used to solve the semantic issue.

```
// Language: Clean

fibComplete n
| n <= 0    = abort "fibCompleteAlt called outside of domain"
| n == 1    = 1
| n == 2    = 1
| otherwise = fibComplete (n - 1) + fibComplete (n - 2)
```

The module `StdMisc` is discussed in more details in [StdMisc](appendix-a/stdmisc).

### Lambda Functions

A control lambda function may be written as follows:

```
// Language: Clean

\ parameter = expression
```

Parameters of a lambda function are space separated.

```
// Language: Clean

\ paramA paramB = expression
```

Alternatively, dot (`.`) and right arrow (`->`) may be used to separate  parameters from expression.
It should be noted that they carry different semantic meanings.

```
// Language: Clean

\ parameter     .  expression
\ paramA paramB -> expression
```

Guarded expressions can be introduced as well.

```
// Language: Clean

\ parameter | guardA = expressionA | guardB = expressionB | guardC = expressionC
```

To increase readability, a lambda definition maybe placed between a pair of parenthesis, which allows it to span multiple lines.

```
// Language: Clean

(\ parameter 
| guardA = expressionA 
| guardB = expressionB 
| guardC = expressionC)
```

### Case Expressions

A`case..of..` expression matches a given expression with one of its patterns, and a control expression with one pattern may be written as follows.

```
// Language: Clean

case expression of
pattern = altExpression
```

Alternatively, right double arrow (`=>`) and right arrow (`->`) may be used to separated a pattern from an alternative expression, but they are not allowed to mix.

```
// Language: Clean

case expression of
pattern => altExpression

case expression of
pattern -> altExpression
```

Multiple patterns and alternative expressions can be introduced in the same way.

```
// Language: Clean

case expression of
patternA = altExpressionA
patternB = altExpressionB
patternC = altExpressionC
```

Guarded expressions can be introduced as well, which allows a case to have multiple guarded bodies.

```
// Language: Clean

case expression of
patternA
| guardA = altExpressionAA
| guardB = altExpressionAB
| guardC = altExpressionAC
patternB = altExpressionB
```

Internally, a `case..of..` expression is compiled to a function.
That is, each case is transformed into an implementation of a function.

```
// language: Clean

__compiledExpr patternA = altExpressionA
__compiledExpr patternB = altExpressionB
__compiledExpr patternC = altExpressionC
```

This explains why guarded expressions can be introduced.

```
// language: Clean

__compiledExpr patternA
| guardAA               = altExpressionAA
| guardAB               = altExpressionAB
| guardAC               = altExpressionAC
__compiledExpr patternB = altExpressionB
```

Consequently, such an expression can result in a run-time error since it is translated into a partial function.

### Pattern-Match Expressions

A pattern-match expression checks an expression against a pattern, and a control expression may be written as follows.

```
// Language: Clean

expression =: pattern
```

If a given expression matches with the pattern, it yields `true`.

Internally, it is compiled down to a `case..of..` expression.

```
// Language: Clean

case expression of
pattern = True
_       = False
```

### Local Definitions

#### Let Expressions

A `let..in..` expression introduces a new scope within another expression.

A control expression with one local definition may be written as follows.

```
// Language: Clean

let
    localFunction x = x + 1
in expression
```

Definitions are local to `expression`.

```
// Language: Clean

[let 
    a = i * i 
in (a, a) \\ i <- [0..n]]
```

#### Where Blocks

A `where` block can be introduced to the end of a function implementation.
It introduces a new scope local to that implementation.

A control `where` block with one definition may be written as follows.

```
// Language: Clean

functionF parameter = expression
where
    localFunc x = x + 1
functionF parameter = expression
```

The second implementation does not have access to `localFunc` which is local to the first implementation.

#### With Blocks

A `with` block can be introduced to the end of a guarded.
It introduces a new scope local to that guarded expression.

A control `with` block with one definition may be written as follows.

```
// Language: Clean

functionG parameter
| guardA = expressionA
with 
    localFunc x = x + 1
| guardB = expressionB
```

However, the guard itself does not have access to definitions within its `with` block.

```
// Language: Clean

functionH n
| x == 0    = 0
with 
    x = n mod 2
| otherwise = 1
```

The definition of `functionH` is not valid since
The guard cannot reference `x`.

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

#### Operator Associativity

The associativity is important when evaluating two operators of the same precedence.
There are three types of associativity:
- `infix` for non-associative operators,
- `infixl` for left-associative operators, and
- `infixr` for right-associative operators.

The associativity of an operator is `infixl` by default.

#### Operator Precedence

The precedence determines how tightly an operator binds to its argument.
Precedence can be between zero and nine with higher number having higher precedence.

The precedence of an operator is nine by default.

#### Operator Definitions

An operator can be defined by placing its name between parentheses.
It can be implemented as if it was an ordinary function.

A control operator definition may be written as follows.

```
// Language: Clean

(operator) paramL paramR = expression
```

In global scope, right double arrow (`=>`) may be used to separated parameters from expression, much like a function.

```
// Language: Clean

// In global scope

(operator) paramL paramR => expression
```

#### Operator Conflict

Operators can conflict with one another.

```
// Language: Clean

(<=>) infixl 9 :: Bool  Bool  -> Bool
(<=>)             True  True  =  True
(<=>)             False False =  True
(<=>)             _     _     =  False

(-->) infixr 9 :: Bool Bool  -> Bool
(-->)             True False =  False
(-->)             _     _    =  True
```

It is not allowed to apply operators with equal precedence in an expression in such a way that their associativity conflict.

```
// Language: Clean

True --> False <=> False
```

The [logical implication](https://en.wikipedia.org/wiki/Material_conditional) operator (`-->`)  is right-associated.
It implies that the expression should be evaluated as follows.

```
// Language: Clean

True --> (False <=> False)
```

However, the [logical equivalence](https://en.wikipedia.org/wiki/Logical_equivalence) operator (`<=>`) is left associated.
It implies that the expression should be evaluated as follows.

```
// Language: Clean

(True --> False) <=> False
```

Since both operators have the same precedence and the order of evaluation cannot be decided by their associativity, this expression will result in a compile-time error.

### Constants

There are two distinct types of constants; constant expressions and constant functions.

#### Constant Expressions

They are computed only once.
Multiple reference to the same expression will result in sharing of that expression.

A control constant expression may be written as follows

```
// Language: Clean

constExprName = expression
```

In global scope, a constant expression may be explicitly defined with `=:` symbol.

```
// Language: Clean

// In global scope

constExprName =: expression
```

#### Constant Functions

They are arity-zero functions.
Unlike constant expressions, they are re-computed every they invoked.

A control constant function may be written as follows.

```
// Language: Clean

constFnName = expression
```

In global scope, a constant function may be explicitly defined with right double arrow (`=>`).

```
// Language: Clean

// In global scope

constFnName => expression
```

### Typing Functions

Although the compiler is generally capable of inferring function types, explicit specification is highly recommended to increase readability.

When a function definition is explicitly typed, implementations must obey the type, instead of the first implementation's signature.

A control function type may be written as follows.

```
// Language: Clean

parameterType -> returnType
```

Parameter types are space separated.

```
// Language: Clean

paramAType paramBType -> returnType
```

#### Typing Ordinary Functions

A control function definition with one parameter may be explicitly typed as follows.

```
// Language: Clean

functionA :: parameterType -> expressionType
functionA    parameter     =  expression
```

Multiple parameter types are space separated.

```
// Language: Clean

functionB :: paramAType paramBType -> expressionType
functionB    paramA     paramB     =  expression
```

#### Typing Operators

A control operator definition may be explicitly typed as follows.

```
// Language: Clean

(operator) :: paramLType paramRType -> expressionType
(operator)    paramL     paramR     =  expression
```

In addition, operator fixity (`F`) and precedence (`P`) may be specified as well.

```
// Language: Clean

(operator) F P :: paramLType paramRType -> expressionType
(operator)        paramL     paramR     =  expression
```

---

## Built-In Types

Types like integers, Booleans, characters, real numbers, lists, tuples, and arrays are frequently used.
They have been predefined for reasons of efficiency and convenience.

There are four built-in primitive types:
- integers,
- real numbers,
- Booleans, and
- characters.

There are three built-in structured types:
- lists,
- arrays, and
- tuples.

### Integers

#### Constructing Integers

There are three methods to construct integer literals.

Integer literals constructed with decimal notation may be written as follows.

```
// Language: Clean

-13
 0
 13
```

Integer literals constructed with octal notation may be written by prefixing octal digits with `0` as follows:

```
// Language: Clean

-015  // decimal -13
 0
 015  // decimal  13
```

Integers literals constructed with hexadecimal notation may be written by prefixing hexadecimal digits with `0x` as follows.

```
// Language: Clean

-0xD  // decimal -13
 0
 0xd  // decimal  13
```

#### Typing Integers

An expression, which evaluates to an integer, may be explicitly typed using `Int`.

```
// Language: Clean

expr :: Int
expr =  1 + 1
```

#### Integer Operators And Functions

Integer operations and functions are discussed in more details on [Appendix A: StdInt](appendix-a/stdint).

### Real Numbers

#### Constructing Real Number

There are two methods to construct real number literals.

Real number literals constructed with decimal notation may be written as follows.


```
// Language: Clean

-1.3
 0.0
 1.3
```

Real number literals constructed with scientific notation may be written as follows.

```
// Language: Clean

-13E-2  // -0.13
 0E0    //  0
 13E-2  //  0.13
```

When constructing a real number literal with scientific notation, the expression may result in an integer instead of a real number.

```
// Language: Clean

13E2  // 1300
```

#### Typing Real Numbers

An expression, which evaluates to a real number, may be explicitly typed using `Real`.

```
// Language: Clean

expr :: Real
expr =  1.0 + 1.0
```

#### Real Number Operators And Functions

Real number operations and functions are discussed in more details on [Appendix A: StdReal](appendix-a/stdreal).

### Booleans

#### Constructing Booleans

There are two methods to construct Boolean literals.

Each constructor represent a Boolean value true and false.

```
// Language: Clean

True
False
```

#### Typing Booleans

An expression, which evaluates to a Boolean value, may be explicitly typed using `Bool`.

```
// Language: Clean

expr :: Bool
expr =  1 == 1
```

#### Boolean Operations and Functions

Boolean operations and functions are discussed in more details on [Appendix A: StdBool](appendix-a/stdbool).

### Characters

#### Constructing Characters

A character may be constructed by placing one character inside a pair of single quotation marks (`'..'`).

Characters constructed in the same way.

```
// Language: Clean

'a'
'9'
'Z'
'+'
```

#### Typing Characters

An expression, which evaluates to a character, may be explicitly typed using `Char`.

```
// Language: Clean

expr :: Char
expr =  'a'
```

#### Character Operations And Functions

Character operations and functions are discussed in more details on [Appendix A: StdChar](appendix-a/stdchar).

### Lists

**Type annotation**:
`[Int]`,
`[Char]`,
`[T]`,
et cetera.

#### Constructing Lists

A list can be constructed in many ways, but there are three primary methods.
The simplest way to construct a list is to explicitly write elements between a pair of square brackets (`[..]`).

```
// Language: Clean

[1]
```

Elements are comma (`,`) separated.

```
// Language: Clean

[1, 2, 3]
```

A list may be appended to the end of another list using colon (`:`).

```
// Language: Clean

[1 : [2, 3]]
```

The outer list (`[1 : ..]`) is constructed by appending the inner list (`[2, 3]`) to the end.

As a result, this method of list construction has a wide varieties which can be written.

```
// Language: Clean

[1 : [2 : [3 : []]]]
[1, 2 : [3]]
[1, 2, 3 : []]
```

Secondly, a list may be implicitly constructed with `dot-dot` expression, and a control expression may be written as follows.

```
// Language: Clean

[initial..]
```

An infinite list is generated from `initial`, which represents the starting value, subsequent elements are incremented by one.

```
// Language: Clean

[1..]  // [1, 2, 3, 4, ...]
```

To create a finite list, an `end` value may be specified.

```
// Language: Clean

[initial..end]
```

By adding `end`, elements are generated only up to `end` it self.

```
// Language: Clean

[1..5]  // [1, 2, 3, 4, 5]
```

In addition, `next` value may be specified to change how subsequent elements are generated.

```
// Language: Clean

[initial, next..end]
```

Each step is computed from `next - initial`.
If the next element is strictly greater than `end`, it will not be included.
It is possible to generate a list which is in descending order.

```
// Language: Clean

[1, 3..9]  // [1, 3, 5, 7, 9]
[5, 4..1]  // [5, 4, 3, 2, 1]
```

By omitting `end`, an infinite list with step may be generated.

```
// Language: Clean

[initial, next..]
```

The expression results in an infinite list.

```
// Language: Clean

[1, 3..]  // [1, 3, 5, 7, 9, ...]
[5, 4..]  // [5, 4, 3, 2, 1, ...]
```

It should be noted that `dot-dot` expressions requires `StdEnum` module.

And lastly, a list may be constructed with comprehension.

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

A list may contain an infinite number of elements, but elements must have the same type.

More information about built-in operations and functions on lists can be found on:

- [Appendix A: StdCharList](appendix-a/stdcharlist),
- [Appendix A: StdList](appendix-a/stdlist), and
- [Appendix A: StdOrdList](appendix-a/stdordlist).

**List patterns**

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

The Standard Environment is discussed in detail [here](appendix-a/introduction.md).