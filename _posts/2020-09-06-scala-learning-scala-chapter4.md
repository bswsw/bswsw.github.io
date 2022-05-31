---
title: "[Learning Scala] Chapter4. 함수"
author: Bae Sangwoo
date: 2020-09-06 09:38:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*

이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.


# 스칼라의 함수 (function)

스칼라에서 **함수**는 이름을 가진, 재활용 가능한 표현식입니다.

함수는 매개변수화할 수도 있고 값을 반환할 수도 있지만 이 어느 것도 필수는 아니지만 이 특징들은 재사용과 구성력을 최대화 합니다.

더 짧고, 더 가독성이 높고, 더 안정적인 어플리케이션을 작성할 수 있도록 해주며 매개변수화 된 함수를 사용하여 정규화된다면 테스트하기도 쉬워집니다.

## 함수형 프로그래밍 언어

스칼라는 **함수형 프로그래밍 언어** 이며 함수형 프로그래밍 언어는 재사용성이 높고 

구성력이 좋은 함수를 지원하여 함수를 구조화할 수 있도록 돕는데 맞춰져 있습니다.

특히 순수 함수를 구성하면 더 훌륭한 이점을 얻을 수 있습니다.

## 순수 함수의 특징

- 하나 또는 그 이상의 입력 매개변수를 가진다.
- 입력 매개변수만을 가지고 계산을 수행한다.
- 값을 반환한다.
- 동일 입력에 대해 하상 같은 값을 반환한다.
- 함수 외부의 어떤 데이터도 사용하거나 영향을 주지 않는다.
- 함수 외부 데이터에 영향받지 않는다.

## 다양한 함수의 사용법

```scala
// 입력값이 없는 함수
def hi = "hi"

// 반환 타입을 지정하여 함수 정의
def hello: String = "hello"

// 함수 정의
def greet(name: String): String = s"Hello, $name"
println(greet("alan"))

// 함수 조기종료
def safeTrim(data: String): String = {
  if (data == null) {
    return null // 특정 조건에 따라 조기종료를 해야한다면 return 사용
  }

  data.trim // return 사용하지 않고 반환
}

val trimResult = safeTrim(" This is trim... ")
println(trimResult) // This is trim...

val trimNullResult = safeTrim(null)
println(trimNullResult) // null
```


# 프로시저 (procedure)

프로시저는 반환값을 가지지 않는 함수입니다.

작성한 함수에 반환값이 없다면 `Unit` 타입으로 추론이 됩니다.

```scala
// 묵시적 반환
def logger(str: String) = println(s"log : $str")

// 명시적 반환
def logger2(str: String): Unit = println(s"log : $str")

// deprecated : 등호 없이 정의
def logger3(str: String) {
  println(s"log : $str")
}
```


# 빈 괄호 함수

함수에 매개변수가 없다면 빈 괄호를 사용하여 함수와 값을 명확하게 구분하는 것이 좋습니다.

```scala
// 빈 괄호를 사용하여 함수 정의
def hi() = "hi"
hi() // ok
hi // ok

// 괄호 없이 함수 정의
def hello = "hello"
hello // ok
hello() // error 발생
```


# 표현식 블록을 이용하여 함수 호출

함수에 표현식 블록을 사용하여 블록의 반환값을 함수의 인수로 사용할 수 있습니다.

```scala
def won(amount: Int) = s"${amount}원"

val myWon = won {
  val money = 1000 + 2000
  money * 2 // 6000. won 함수의 인수로 사용됨
}

println(myWon) // 6000원
```


# 중첩 함수

스칼라에서 표현식 블록이 중첩이 되듯 함수도 중첩이 가능합니다.

```scala
def max(a: Int, b: Int, c: Int) = {
  def max(x: Int, y: Int) = if (x > y) x else y

  max(a, max(b, c))
}

val maxNum = max(100, 200, 300)
println(maxNum) // 300
```


# 매개변수 이름을 지정하여 함수 호출

함수를 호출 할 때 매개변수 이름을 지정하여 함수를 호출하면 명시적이며 

실제 함수 정의 시의 매개변수 순서와 다르게 순서를 지정하여도 호출이 가능합니다.

```scala
def printProfile(name: String, age: Int, email: String): Unit = {
  println(s"name: $name")
  println(s"age: $age")
  println(s"email: $email")
}

printProfile(
  name = "alan",
  email = "alan@gmail.com", // 실제 함수에 선언된 매개변수 순서와 호출 순서가 다르더라도 가능
  age = 10
)
// 출력:
// name: alan
// age: 10
// email: alan@gmail.com
```


# 기본값을 갖는 매개변수

함수의 매개변수의 기본값을 지정할 수 있으며 기본값을 지정한 매개변수를 

매개변수 순서를 뒤로 가도록 하면 구조화에 더 좋습니다.

```scala
def greet(name: String, prefix: String = "Hello") = {
  println(s"$prefix, $name")
}

greet(name = "alan") // Hello, alan
greet(prefix = "hi", name = "alan") // hi, alan
greet("alan", "bye") // bye, alan
```


# 가변 매개변수

스칼라는 코틀린 처럼 가변 매개변수 (`vararg`)를 받을 수 있습니다.

`*`(asterisk, 별표) 를 사용하여 가변 매개변수를 정의할 수 있고 해당 매개변수는 컬렉션 타입입니다.

```scala
def sum(nums: Int*): Int = {
  var total = 0
  for (num <- nums) total += num

  total
}

println(sum(1, 2, 3, 4)) // 10
```


# 매개변수 그룹

스카라는 매개변수를 그룹으로 분리를 할 수 있다. 괄호 (`(`, `)`)를 기준으로 그룹을 묶을 수 있습니다.

```scala
def min(x: Int)(y: Int): Int = {
  if (x < y) x else y
}

println(min(1)(5)) // 1
```
   
> 위 예제처럼 간단한 코드에서는 특별하게 사용해야할 이유가 없을 수도 있지만 추후에 고차 함수를 사용하게 되어 함수 리터럴과 함게 사용할 때 이점이 있을 수 있습니다.


# 타입 매개변수

자바 등에서 부르는 generic 과 비슷한 기능입니다.

대괄호(`[`,`]`)를 사용하여 타입을 지정할 수 있으며 지정한 타입으로 해당 함수 호출 범위에서 사용할 수 있습니다.

더불어 타입 추론을 함께 사용하면 더 간결한 코드를 작성할 수 있습니다.

```scala
// 결과값의 타입을 지정해서 받을 수 없다. 무조건 Any로 리턴
def identity(a: Any): Any = a

val s1: String = identity("string") // error: type missmatch
val i1: Int = identity(1) // error: type missmatch

// 아래부터 타입 매개변수 사용

// 리턴 타입을 지정해서 받을 수 있음.
def identity2[A](a: A): A = a

// 함수에 대괄호로 타입 지정
val s2: String = identity2[String]("string")
val i2: Int = identity2[Int](1)

// 매개변수 자체로 리턴타입 추론 가능.
val s3 = identity2("string")
val i3 = identity2(2)
```


# 메소드

스칼라에서 객체의 메소드를 호출하는 방식은 기존의 자바와 동일합니다.

단지 스칼라의 특성에 따라 빈 괄호를 생략하거나 띄어 쓰기로 매개변수를 입력할 수 있습니다.

```scala
val image = "image.jpg"

image.endsWith(".jpg") // true (기본적인 방식)
image endsWith ".jpg" // true (위와 동일. 코틀린의 infix function 처럼... 스칼라에서는 연산자 표기법이라고 함.)

image.last // g (매개변수가 없는 함수)
```


# 연산자 표기법 (operator notation)

위 예제에서 보여진 것처럼 함수의 매개변수가 한 개 일때 점과 괄호를 제거하고 함수를 호출하는 방식을 **연산자 표기법**이라고 합니다.

실제로 스칼라에서는 덧셈 (`+`) 등 어떤 산술 연산자도 없고 모두 객체의 메소드로써 호출을 합니다.

```scala
val num = 1 + 2 // 그동안 산술연산자 처럼 사용하였다.
val num2 = 1.+(2) // 실제로는 이렇게 메소드 호출로 동작한다.

num compare 4 // -1 (num보다 작으면 1, 같으면 0, 크면 -1)

val str = "mystr"
str substring (2, 4) // st (매개변수가 두개 이상일때는 비효율적일수도...)
```

![](/assets/img/posts/2020-09-06-scala-learning-scala-chapter4/operator-notation.png)
*이와 같이 연산자는 모두 객체의 메소드 이다.*
