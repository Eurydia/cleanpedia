---
title: "Appendix A: StdInt"
---

## Introduction

The [StdInt](https://cloogle.org/src/#base-stdenv/StdInt;icl;line=1) module contains source code for operations and functions relating to integer type.

 For integer type, this module implements:
- arithmetic operations,
- comparisons,
- bitwise operations,
- property testing, and
- conversions.

## Constants

### Zero Unit

**Implementation**

```
// Language: Clean

zero ::  Int
zero :== 0
```

**Definition**: represents the value zero for integers.

### One Unit

**Implementation**

```
// Language: Clean

one ::  Int
one :== 1
```

**Definition**: represents the value one for integers.

---

## Arithmetic Operations and Functions


### Negation

**Signature**

```
// Language: Clean

~ :: Int -> Int
~    a   => ...
```

**Behavior**: inverts the sign `a`.

**Usage**

```
// Language: Clean
 
~  0   //  0
~  1   // -1
~(-1)  //  1
```

### Addition

**Signature** 

```
// Language: Clean
 
(+) infixl 6 :: Int Int -> Int
(+)             a   b   => ...
```

**Behavior**: adds `a` and `b`.

**Usage**

```
// Language: Clean

  1  +   1   //  2
  1  + (-1)  //  0
(-1) +   1   //  0
(-1) + (-1)  // -2
```

### Subtraction

**Signature**

```
// Language: Clean
 
(-) infixl 6 :: Int Int -> Int
(-)             a   b   => ...
```

**Behavior**: subtracts `b` from `a`.

**Usage**

```
// Language: Clean

  1  -   1  //  0
  1  - (-1) //  2
(-1) -   1  // -2
(-1) - (-1) //  0
```

### Multiplication

**Signature**

```
// Language: Clean
 
(*) infixl 7 :: Int Int -> Int
(*)             a   b   => ...
```

**Behavior**: multiplies `a` and `b`.

**Usage**

```
// Language: Clean

  1  *   1  //  1
  1  * (-1) // -1
(-1) *   1  // -1
(-1) * (-1) //  1
```

### Floor Division

**Signature**

```
// Language: Clean

(/) infixl 7 :: Int Int -> Int
(/)             a   b   => ...
```

**Behavior**: divides `a` by `b`.
Silently crashes if `b` is zero.

**Usage**

```
// Language: Clean

  1  /   1   //  1
  1  / (-1)  // -1
(-1) /   1   // -1
(-1) / (-1)  //  1
  7  /   0   //  crashes
```

### Reminder Division

**Signature**

```
// Language: Clean

(rem) infix 7 :: Int Int -> Int
(rem)            a   b   => ...
```

and

```
// Language: Clean

(mod) infix 7 :: Int Int -> Int
(mod)            a   b   => ...
```

For integers, both `rem` and `mod` perform modulo division.

**Behavior**: divides `a` by `b`, and returns the reminder.

**Usage**

```
// Language: Clean

  3  mod (-2)  //  1
(-3) mod (-2)  // -1
(-3) mod   2   // -1
  3  mod   2   //  1
```

### Exponentiation

**Signature**

```
// Language: Clean

(^) infixr 8 :: Int Int -> Int
(^)             a   b   => ...
```

**Behavior**: raises `a` to `b`-th power.
Results in a run-time error if `b` is negative.

```
^ (Int) called with negative power argument
```

**Usage**

```
// Language: Clean

  1  ^   1   //  1
  1  ^ (-1)  //  NOT OK :(
(-1) ^   1   // -1
(-1) ^ (-1)  //  NOT OK :(  
```

---

## Relational Operations

### Equal To

**Signature**

```
// Language: Clean

(==) infix 4 :: Int Int -> Bool
(==)            a   b   => ...
```

**Behavior**: returns true if `a` is equal to `b`.

**Usage**

```
// Language: Clean

  5  ==   2   // False
(-5) ==   2   // False
  5  == (-2)  // False
(-5) == (-2)  // False
(-2) == (-2)  // True
```

### Not Equal To

**Signature**

```
// Language: Clean

(<>) infix 4 :: Int Int -> Bool
(<>)            a   b   => ...
```

**Behavior**: returns true if `a` is not equal to `b`.

**Usage**

```
// Language: Clean

  5  <>   2   // True
(-5) <>   2   // True
  5  <> (-2)  // True
(-5) <> (-2)  // True
(-2) <> (-2)  // False
```

### Less Than

**Signature**

```
// Language: Clean

(<) infix 4 :: Int Int -> Bool
(<)            a   b   => ...
```

**Behavior**: returns true if `a` is strictly less than `b`.

**Usage**

```
// Language: Clean

  5  <   2   // False
(-5) <   2   // True
  5  < (-2)  // False
(-5) < (-2)  // True
(-2) < (-2)  // False
```

### Less Than Or Equal To

**Signature**

```
// Language: Clean

(<=) infix 4 :: Int Int -> Bool
(<=)            a   b   => ...
```

**Behavior**: returns true if `a` is less than or equal to `b`.

**Usage**

```
// Language: Clean

  5  <=   2   // False
(-5) <=   2   // True
  5  <= (-2)  // False
(-5) <= (-2)  // True
(-2) <= (-2)  // True
```

### Greater Than

**Signature**

```
// Language: Clean

(>) infix 4 :: Int Int -> Bool
(>)            a   b   => ...
```

**Behavior**: returns true if `a` is strictly greater than `b`.

**Usage**

```
// Language: Clean

  5  >   2   // True
(-5) >   2   // False
  5  > (-2)  // True
(-5) > (-2)  // False
(-2) > (-2)  // False
```

### Greater Than Or Equal To

**Signature**

```
// Language: Clean

(>=) infix 4 :: Int Int -> Bool
(>=)            a   b   => ...
```

**Behavior**: returns true if `a` is greater than or equal to `b`.

**Usage**

```
// Language: Clean

  5  >=   2   // True
(-5) >=   2   // False
  5  >= (-2)  // True
(-5) >= (-2)  // False
(-2) >= (-2)  // True
```

---

## Bitwise Operations and Functions

Interacts with integers through their binary representation.

### Bitwise NEGATE

**Signature**

```
// Language: Clean

bitnot :: Int -> Int
bitnot    a   => ...
```

**Behavior**: returns bit-wise two complement of `a`.

**Usage**

```
// Language: Clean

bitnot (-5)  //  -4
bitnot (-2)  //   1
bitnot   2   //  -3
bitnot   5   //  -6
```

### Bitwise OR

**Signature**

```
// Language: Clean

(bitor) infixl 6 :: Int Int -> Int
(bitor)             a   b   => ...
```

**Behavior**: returns bit-wise OR of `a` and `b`.

**Usage**

```
// Language: Clean

  5  bitor   2   //  7
(-5) bitor   2   // -5
  5  bitor (-2)  // -1
(-5) bitor (-2)  // -1
```

### Bitwise AND

**Signature**


```
// Language: Clean

(bitand) infixl 6 :: Int Int -> Int
(bitand)             a   b   => ...
```

**Behavior**: returns bit-wise AND of `a` and `b`.

**Usage**

```
// Language: Clean

  5  bitand   2   //  0
(-5) bitand   2   //  2
  5  bitand (-2)  //  4
(-5) bitand (-2)  // -6
```

### Bitwise XOR

**Signature**

```
// Language: Clean

(bitxor) infixl 6 :: Int Int -> Int
(bitxor)             a   b   => ...
```

**Behavior**: returns bit-wise XOR of `a` and `b`.

**Usage**

```
// Language: Clean

  5  bitxor   2   //  7
(-5) bitxor   2   // -7
  5  bitxor (-2)  // -5
(-5) bitxor (-2)  //  5
```

### Bitwise Left Shift

**Signature**

```
// Language: Clean

(<<) infix 7 :: Int Int -> Int
(<<)            a   b   => ...
```

**Behavior**: shifts `a` to the left by `b` bits.

**Usage**

```
// Language: Clean

  5  <<   2   //  20
(-5) <<   2   // -20
  5  << (-2)  //  4611686018427387904
(-5) << (-2)  // -4611686018427387904
```

### Bitwise Right Shift

**Signature**

```
// Language: Clean

(>>) infix 7 :: Int Int -> Int
(>>)            a   b   => ...
```

**Behavior**: shifts `a` to the right by `b` bits.

**Usage**

```
// Language: Clean

  5  >>   2   //  1
(-5) >>   2   // -2
  5  >> (-2)  //  0
(-5) >> (-2)  // -1
```

---

## Basic Functions

### `sign`

**Signature**

```
// Language: Clean

sign :: Int -> Int
sign    a   => ...
```

**Behavior**: returns the sign of `a`.

**Usage**

```
// Language: Clean

sign   1   //  1
sign   0   //  0
sign (-1)  // -1
```

### `abs`

**Signature**

```
// Language: Clean

abs :: Int -> Int
abs    a   => ...
```

**Behavior**: returns the absolute value of `a`.

**Usage**

```
// Language: Clean

abs   1   // 1
abs   0   // 0
abs (-1)  // 1
```

### `gcd`

**Signature**

```
// Language: Clean

gcd :: Int Int -> Int
gcd    a   b   => ...
```

**Behavior**: returns the greatest common divisor of `a` and `b`.

**Usage**

```
// Language: Clean

gcd   3    2   // 1
gcd (-3)   2   // 1
gcd   3  (-2)  // 1
gcd (-3) (-2)  // 1
```

### `lcm`

**Signature**

```
// Language: Clean

lcm :: Int Int -> Int
lcm    a   b   => ...
```

**Behavior**: returns the least common multiple of `a` and `b`.

**Usage**

```
// Language: Clean

lcm   3    2   // 6
lcm (-3)   2   // 6
lcm   3  (-2)  // 6
lcm (-3) (-2)  // 6
```

---

## Property Functions

Tests properties of an integer.

### `isEven`

**Signature**

```
// Language: Clean

isEven :: Int -> Bool
isEven    a   => ...
```

**Behavior**: returns true if `a` is an even integer.

**Usage**

```
// Language: Clean

isEven  2  // False
isEven  1  // True
isEven  0  // True
isEven -1  // True
isEven -2  // False
```

### `isOdd`

**Signature**

```
// Language: Clean

isOdd :: Int -> Bool
isOdd    a   => ...
```

**Behavior**: returns true if `a` is an odd integer.

**Usage**

```
// Language: Clean

isOdd  2  // True
isOdd  1  // False
isOdd  0  // False
isOdd -1  // False
isOdd -2  // True
```

---

## Conversions To Integer Type

Explicitly converts values of other types to integer type.

### From Real Number Type

**Signature**

```
toInt :: Real -> Int
toInt    a    => ...
```

**Behavior**: rounds `a` to its nearest integer.

**Usage**

```
// Language: Clean

toInt   1.5   //  2
toInt   1.4   //  1
toInt   0.0   //  0
toInt (-1.4)  // -1
toInt (-1.5)  // -2
```

### From Character Type

**Signature**

```
// Language: Clean

toInt :: Char -> Int
toInt    a    => ...
```

**Behavior**: returns ASCII value of `a`.

**Usage**

```
// Language: Clean

toInt '1'  //  49
toInt '9'  //  59
toInt 'A'  //  65
toInt 'Z'  //  90
toInt 'a'  //  97
toInt 'z'  // 122
```

### From String Type

**Signature**

```
// Language: Clean

toInt :: {#Char} -> Int
toInt    a       => ...
```

**Behavior**: attempts to parse `a` to an integer.
Returns zero if unsuccessful.

**Usage**

```
// Language: Clean

toInt "1.0"   //  0	
toInt "1"     //  1
toInt "0"     //  0
toInt "-1"    // -1
toInt "-1.0"  //  0
```

---

## Conversions From Integer Type

Explicitly converts integer type to other types.
The desired type must be unambiguous.

### To Real Number Type

**Signature**

```
// Language: Clean

fromInt :: Real -> Int
fromInt    a    => ...
```

**Behavior**: converts an integer `a` into a real number.
The decimal place is set to zero.

**Usage**

```
// Language: Clean

expr :: Real
expr =  fromInt  1  // 1.0
expr =  fromInt  0  // 0.0
```

### To Character Type

**Signature**

```
// Language: Clean

fromInt :: Int -> Char
fromInt    a   => ...
```

**Behavior**: converts an integer `a` in to a character based on its ASCII value.

**Usage**

```
// Language: Clean

expr :: Char
expr =  fromInt 49   // '1'
expr =  fromInt 59   // '9'
expr =  fromInt 65   // 'A'
expr =  fromInt 90   // 'Z'
expr =  fromInt 97   // 'a'
expr =  fromInt 122  // 'z'
```

### To String Type

**Signature**

```
// Language: Clean

fromInt :: Int -> {#Char}
fromInt    a   => ...
```

**Behavior**: converts an integer `a` into a string.

**Usage**

```
// Language: Clean

expr :: {#Char}
expr =  fromInt 1  // "1"
expr =  fromInt 0  // "0"
```