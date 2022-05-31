---
title: "[Learning Scala] Chapter3. 표현식과 조건문"
author: Bae Sangwoo
date: 2020-09-04 17:02:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*

이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.


# 표현식 (Expression)

- 표현식은 실행 후 값을 반환하는 코드 단위를 말함.
- 기존 데이터를 변경하는 대신 값을 반환할 수 있기 때문에 함수형 프로그래밍의 기반이 됨.

```scala
// 누구나 할 수 있지만 이 자체가 표현식이라는 개념을 설명하기 위해...
"hello" // hello
"he" + "ll" + "o" // hello
```

## 표현식 블록 (Expression Block)

- 중괄호(`{`, `}`)를 사용하여 여러 표현식을 표현식 블록으로 만들 수 있음.
- 표현식 블록 안의 마지막 표현식이 전체 블록의 반환값이 됨.

```scala
// 1. 일반 표현식
val a = 5
val a2 = a + 10

println(a2) // 15

// 2. 표현식 블록
val b = {
  val x = 5
  x + 10
}

println(b) // 15
```
> 위 두 예제는 동일한 결과가 출력되지만 표현식 블록을 사용한 예제는 어떠한 값을 정의 하기 위한 것임을 분명히 할 수 있습니다.

```scala
val amount = {
  val a = 1000; {
    val b = a * 2; {
      val c = b - 2
      c
    }
  }
}

println(amount)
```
> 위와 같이 표현식 블록은 중첩이 가능합니다. 굳이 이런 방식을 사용할 필요는 없지만 이 구문과 구성의 본질을 이해하는 것이 필요합니다.


# if 표현식

자바 등 타 언어에서는 `if... else if... else` 블록형태를 취하지만 

스칼라는 공식적으로 `if`와 선택적인 `else` 블록만을 지원합니다.

실전에서는 `if... else if... else`블록을 작성할 수 있지만 

이는 스칼라에서는 `if... else { if... else...}` 표현식과 동일하며 논리적으로는 일치합니다.

```scala
val amount = 10
  
if (amount > 0) {
  println(true) // true 출력
} else {
  println(false)
}
```

```scala
// 아래 두 표현식은 동일하며 논리적으로도 일치함.

if (a > 0) {
  println("양수")
} else if (a < 0) {
  println("음수")
} else {
  println("0")
}

if (a > 0) {
  println("양수")
} else {
  if (a < 0) {
    println("음수")
  } else {
    println("0")
  }
}
```


# 매치 표현식 (Match)

- 자바의 `switch` 문과 유사하며 모두 잡아 내는 catch-all 패턴을 지원합니다.
- 매치 표현식은 0개 또는 단 하나의 패턴만 매칭할 수 있으므로 여러 개의 패턴을 한 번에 순서대로 매칭하는 형태의 제어 이동(fall-through)이 없습니다.
- `break`를 지원하지 않습니다.

## 기본 사용법

```scala
val x = 10
val y = 20


val maxVal = x > y match {
  case true => x
  case false => y
}

println(maxVal) // 10

// 표현식의 입력값에 따라 값을 변환하는 것 외에 추가적인 동작을 할 수 있다.
val status = 500

val message = status match {
  case 200 => "ok"
  case 400 => {
    println("Bad Request")
    "error"
  }
  case 500 => {
    println("Internal Server Error")
    "error"
  }
}

println(message)
```

## 패턴 대안 (Pattern Alternative)

하나의 `case` 블록에 파이프 기호(`|`)를 사용하여 패턴 중 하나라도 일치하면 해당 `case` 블록을 리턴할 수 있습니다.

```scala
val day = "MON"

val kind = day match {
  case "MON" | "TUE" | "WED" | "THU" | "FRI" => "weekday"
  case "SAT" | "SUN" => "weekend"
}

println(kind) // weekday
```

## 값 바인딩 패턴

특정 리터럴을 매칭 하고 그 외의 경우에는 값 바인딩을 이용하여 가능한 모든 값이 매칭 되도록 할 수 있습니다.

```scala
val message = "ERROR"

val status = message match {
  case "OK" => 200
  case other =>
    println(other) // ERROR
    -1
}

println(status) // -1
```

> 값 `other`은 `case` 블록이 유지되는 동안 정의 되며 매치 표현식의 입력값인 `message` 값이 할당됩니다.

## 와일드카드 연산자 패턴

와일드카드 연산자로는 언더바(`_`) 기호가 사용됩니다.

```scala
val message = "ERROR"

val status = message match {
  case "OK" => 200
  case _ => -1
}

println(status) // -1
```

## 패턴 가드 (Pattern guard)

`case` 블록에서 `if` 표현식을 추가하여 `match` 표현식에 조건부 로직을 섞어 쓸 수 있습니다.

```scala
val data = "hello"

data match {
  case str if data == "hi" => println(str)
  case str => println(s"this is not hi! : $str") // this is not hi! : hello
}
```

## 타입 매칭

매치 표현식으로 각 `case` 블록에서 타입을 매칭하여 처리를 분리할 수 있습니다.

```scala
val x: Int = 1123
val y: Any = x // 최상위 타입인 Any에 x값을 할당

y match {
  case x: String => println(s"String : $x")
  case x: Int => println(s"Int : $x") // Int : 1123
  case x: Long => println(s"Long : $x")
  case x: Double => println(f"Double : $x%.2f")
  case x: Float => println(f"Float : $x%.2f")
}
```


# 루프 (Loop)

## 기본 `for` 루프 반복

```scala
for (<식별자> <- <반복자>) [yield] [<표현식>]
```

> `yield`는 호출된 모든 표현식의 반환 값을 컬렉션으로 반환합니다.

아래는 가장 간단한 `for` 루프 예제입니다.

```scala
for (x <- 1 to 5) {
  println(x)
}

// 출력
// 1
// 2
// 3
// 4
// 5
```

아래는 `yield` 키워드를 사용하여 컬렉션을 리턴하는 예제입니다.

```scala
val result = for (x <- 1 to 5) yield {
  s"data: $x"
}

println(result) // Vector(data: 1, data: 2, data: 3, data: 4, data: 5)
```

> `yield` 키워드를 사용하여 리턴된 컬렉션은 Vector 입니다.

## 반복자 가드

매치 표현식의 패턴 가드와 마찬가지로 반복자 가드 (or 필터) 를 추가할 수 있습니다.

```scala
val threes = for (i <- 1 to 20 if i % 3 == 0) yield i
println(threes) // Vector(3, 6, 9, 12, 15, 18)

// 라인을 구분하여 반복자와 반복자 가드를 구분할 수 있고 반복자 가드를 여러개 사용할 수 있음.
val quotes = "a,b,c,,d"
for {
  t <- quotes.split(",")
  if t != null
  if t.length > 0
} println(t)
```

## 중첩된 반복자 (Nested Iterator)

반복자를 중첩하여 사용하면 전체 반복 횟수는 모든 반복자 횟수의 곱입니다. 

```scala
for {
  x <- 1 to 2
  y <- 1 to 3
} print(s"($x, $y), ") // (1, 1), (1, 2), (1, 3), (2, 1), (2, 2), (2, 3), 
```

## 값 바인딩

`for` 루프 표현식 블록 내에서 사용할 임시 변수를 바인딩 할 수 있습니다.

```scala
val result = for (i <- 1 to 5; data = s"data:$i") yield data // data가 임시 변수 
println(result) // Vector(data:1, data:2, data:3, data:4, data:5)
```

`while`과 `do... while` 루프

```scala
var x = 3
while (x > 0) {
  print(s"x:$x,")
  x -= 1
}
// 출력 : x:3,x:2,x:1,

val y = 0
do println(s"y:$y")
while (y > 0)
// 출력 : y:0
```
