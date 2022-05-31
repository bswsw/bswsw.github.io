---
title: "[Learning Scala]  Chapter2. 데이터로 작업하기: 리터럴, 값, 변수, 타입"
author: Bae Sangwoo
date: 2020-09-04 13:38:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*

이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.


# 스칼라 데이터의 정의

- 값
    - 불변의 타입
    - 재할당 불가

- 리터럴 (리터럴 데이터)
    - 숫자, 문자, 텍스트 등 데이터

- 변수
    - 가변 타입
    - 재할당 가능

- 타입
    - 데이터의 종류, 데이터의 정의 또는 분류
    - 스칼라의 모든 데이터는 특정 타입에 대응
    - 모든 스칼라 타입은 데이터와 그 데이터를 처리하는 메소드를 갖는 클래스로 정의


# 값 (Value)

`val` 키워드를 사용하여 불변한(immutable) 값을 정의할 수 있습니다.

```scala
// val <식별자>[: <타입>] = <데이터>

val x: Int = 20
val greeting: String = "Hello, Scala!"
val at: Char = '@'

x = 1000 // error: 재할당 불가
```


# 변수 (Variable)

`var` 키워드를 사용하여 변경이 가능한(mutable) 변수를 정의할 수 있습니다.

```scala
// var <식별자>[: <타입>] = <데이터>

var myStr: String = "Hello"
var myInt: Int = 100

myStr = "Hi" // 재할당 가능
myInt = "Scala" // error : Type missmatch
```


# 타입 (Type)

- 스칼라는 숫자 타입(`Int`, `Double` 등) 과 숫자가 아닌 타입 (`String` 등)이 있음.
- 스칼라는 원시 데이터 타입 (primitive type)이 없음.

## 타입 추론

값이나 변수를 선언 시 타입을 명시하지 않고 할당된 데이터를 기반으로 타입을 추론할 수 있습니다.

```scala
val myVal = 10 // Int 값으로 추론됨

var myVar = "Hello" // String 변수로 추론됨
```

## 숫자형 데이터 타입 (Number Data Type)

|순위 |이름 |설명 |크기 (Byte)  |최소 |최대|
|:---|:---|:---|:---|:---|:---|
|1	|`Byte`	|부호 있는 정수	|1	|-128	|127|
|2	|`Short`	|부호 있는 정수	|2	|-32768	|32767|
|3	|`Int`	|부호 있는 정수	|4	|-2^31	|2^31 - 1|
|4	|`Long`	|부호 있는 정수	|8	|-2^63	|2^63 - 1|
|5	|`Float`	|부호 있는 부동 소수점	|4	|n/a	|n/a|
|6	|`Double`	|부호 있는 부동 소수점	|8	|n/a	|n/a|

### 숫자 타입 간 자동 변환

```scala
// 위의 표에서 낮은 순위에 있는 타입은 자신보다 높은 타입으로 자동 변환이 가능하다. 
val b: Byte = 10
val s: Short = b 
val d: Double = s 

val i: Int = d // Double은 Int보다 높은 순위의 타입으로 변환 불가
```

### 메소드를 사용한 숫자 타입 간 변환

```scala
val l: Long = 10
val i: Int = l.toInt // Int 타입으로 변환
```

### 숫자 리터럴(Literal) 표현

```scala
val myInt = 10 // Int
val myHex = 0xffff00 // 접두사 0x : 16진수
val myLong = 200L // 접미사 L(l) : Long
val myDouble = 2.0 // Double
val myFloat = 10f // 접미사 f : Float
val myDouble2 = 2d // 접미사 d : Double
```


## 문자열 (`String`)

스칼라의 `String` 은 자바의 `String` 을 기반으로 했습니다. [침고!](https://github.com/scala/scala/blob/2.13.x/src/library/scala/Predef.scala#L147){:target="_blank"}

### 기본 사용법

```scala
# REPL

scala> val hello = "hello"
val hello: String = hello

scala> val helloScala = "hello \nscala"
val helloScala: String =
hello
scala
```

### 여러줄 리터럴

- 큰 따옴표 세 개를 사용하여 여러줄 리터럴리 가능함.
- 특수문자의 시작인 역슬래시를 인식하지 못함.

```scala
# REPL

scala> val multipleLine = """scala
     | java
     | kotlin
     | jvm
     | \t
     | \n"""
val multipleLine: String =
scala
java
kotlin
jvm
\t
\n
```

### 문자열 보간 (String Interpolation)

```scala
val alan = "alan"

println("Hello, " + alan) // Hello, alan
println(s"Hello, $alan") // Hello, alan

val num = 10

println(s"num : $num") // num : 10
println(s"num * 3 : ${num * 3}") // num * 3 : 30

// printf
val item = "apple"

println(f"I wrote a new $item%.3s today") // I wrote a new app today
println(f"$item count : ${355 / 113.0}%.5f") // apple count : 3.14159
```

## 튜플

- 둘 이상의 값을 가지는 순서가 있는 컨테이너.
- 각각의 값은 다른 타입을 가질 수 있음.
- 값을 논리적으로 분류할 필요가 있으며 분류한 값들을 하나의 응집된 단위로 표현하는데 유용함.
- 리스트, 배열과는 다르게 요소들을 반복할 수는 없고 단지 하나 이상의 값을 담기 위한 컨테이너.

```scala
val infos = ("alan", "bae", 8, 31)
println(infos._1) // alan

val red = "red" -> 0xff0000
println(red) // (red,16711680)

val reversedRed = red._2 -> red._1
println(reversedRed) // (16711680,red)
```
