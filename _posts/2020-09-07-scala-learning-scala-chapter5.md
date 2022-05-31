---
title: "[Learning Scala] Chapter5. 일급 함수"
author: Bae Sangwoo
date: 2020-09-07 10:20:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*

이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.


# 일급 함수란?

- 함수가 선언되고 호출되는 것 외에 다른 데이터 타입처럼 사용될 수 있는 것.
- 식별자에 할당되지 않고도 리터럴 형태로 생성될 수 있음.
- 값, 변수 데이터 구조처럼 컨테이너에 저장될 수 있음.
- 다른 함수의 매개변수로 사용되거나 다른 함수의 반환값으로 사용될 수 있음. (고차 함수: higher-order function)


# 함수 타입과 값

함수의 타입은 입력 타입과 반환 값 타입의 단순한 그룹입니다.

일반적인 함수 정의 시 `String`, `Int`, `Unit` 등의 타입을 반환하지만 함수 자체를 반환할 수 있으며 

이 때 반환 타입을 정의할 때 아래와 같이 작성할 수 있습니다.

```
([<입력 타입>, ....]) => <반환 타입>
```

```scala
def double(x: Int): Int = {
  x * 2
}
double(10) // 일반 함수 호출

val myDouble: Int => Int = double // myDouble은 double 함수 자체를 값으로 받아 타입은 Int => Int (Int를 입력받아 Int를 반환하는 타입)
myDouble(10) // double 함수 호출과 결과가 동일

val myDoubleCopy = myDouble // 새로운 값에 할당하는 것도 일반 값과 동일하게 가능
myDoubleCopy(5) // double, myDouble과 결과 동일

def max(x: Int, y: Int): Int = {
  if (x > y) x else y
}

val maximum: (Int, Int) => Int = max // max 함수의 매개변수가 여러개일때.
maximum(10, 20)
```


# 고차 함수 (higher-order function)

입력 매개변수나 반환 값을 함수 타입의 값을 가지는 함수를 고차 함수 라고 합니다.

```scala
// 두번째 매개변수로 함수 타입을 받는다.
def myString(str: String, fun: String => String): String = {
  if (str == null) {
    return null
  }

  fun(str) // 함수타입으로 받은 매개변수를 실행시킨다.
}

def reverser(s: String) = s.reverse

myString("hello", reverser) // olleh (두번째 매개변수로 함수를 넘긴다.)
```


# 함수 리터럴 (function literal)

**함수 리터럴**은 이름없는 함수로 함숫값과 변수에 저장되거나 고차 함수 매개변수로 정의할 수 있습니다.

## 함수 리터럴의 다른 명칭

- 익명 함수 (Anonymous function)
- 람다 표현식, 람다 (Lambda expression)

```
([<식별자>: <타입>, ... ]) => <표현식>
```

```scala
/**
 * doDouble 이라는 이름이 있는 함수 정의
 * @param x 입력값
 * @return 결과값
 */
def doDouble(x: Int): Int = x * 2

/**
 * 이름없는 함수를 doubler 라는 함숫값으로 정의
 * 이 값의 타입은 Int => Int 이다.
 */
val doubler = (x: Int) => x * 2
```

> 위 예제의 차이가 느껴지시나요?

위에서 **고차 함수**의 예제로 사용했던 함수를 사용하여 함수 리터럴을 표현할 수 있습니다.

```scala
// 두번째 매개변수로 함수 타입을 받는다.
def myString(str: String, fun: String => String): String = {
  if (str == null) {
    return null
  }

  fun(str) // 함수타입으로 받은 매개변수를 실행시킨다.
}

myString("hello", (s: String) => s.replace("l", "i")) // 두번째 파라미터로 함수 리터럴을 넘김.
```


# 자리표시자 구문 (placeholder syntax)

함수 리터럴의 축약형으로 지정된 매개변수를 와일드카드 연산자(`_`)로 대체한 형태입니다.

입력 매개변수가 한 번만 사용되고 리터럴의 타입이 명시적으로 정의되어 있다면 사용할 수 있습니다.

```scala
// 리터럴의 타입이 Int => Int로 명시되어 있어 와일드카드(_)를 사용할 수 있음.
val doubler: Int => Int = _ * 2

// 두번째 매개변수로 함수 타입을 받는다.
def myString(str: String, fun: String => String): String = {
  if (str == null) {
    return null
  }

  fun(str) // 함수타입으로 받은 매개변수를 실행시킨다.
}

// 명시적으로 두번째 파라미터가 String => String 타입의 함수값을 받도록 정의되어 있으므로 와일드카드(_)를 사용할 수 있음.
myString("hello", _.reverse) // olleh
```


# 부분 적용 함수와 커링

지금까지 공부한 함수는 기본 매개변수가 있지 않다면 함수의 모든 매개변수를 지정하여야 합니다.

만약 일부 매개변수를 고정하고 싶다면 와일드카드(`_`)를 명시적인 타입과 함께 사용하여 

부분적으로 함수를 적용할 수 있고 이 기법을 **커링(currying)**이라고 합니다.

```scala
def factorOf(x: Int, y: Int) = y % x == 0

val multipleOf3 = factorOf(3, _: Int) // factorOf 함수의 첫번째 파라미터를 3으로 고정한 함수값
val isEven = factorOf(2, _: Int) // factorOf 함수의 첫번째 파라미터를 2로 고정한 함수값

multipleOf3(30) // true
isEven(111) // false
```


# 이름에 의한 호출 매개변수

함수의 매개변수를 값, 함수 중 아무거나 호출할 수 있도록 지원하는 **이름에 의한(by-name) 호출 매개변수** 입니다.

값으로 매개변수를 넘긴다면 일반적인 함수와 동일하게 파라미터에 접근할 수 있습니다. (by-value parameter)

만약 함수를 매개변수를 넘긴다면 호출한 함수 내에서 매개변수가 사용할때마다 매개변수로 넘긴 함수가 실행됩니다.

```
이름에 의한 호출 매개변수 지정하기
<식별자>: => <타입>
```

```scala
// 일반적인 함수
def myPrinter1(data: Int): Unit = {
  for (i <- Range(0, 2)) {
    println(s"myPrinter1 call data($i) : $data")
  }
}

// 이름에 의한 호출 매개변수를 가진 함수
def myPrinter2(data: => Int): Unit = {
  for (i <- Range(0, 2)) {
    println(s"myPrinter2 call data($i) : $data")
  }
}

// 예시 함수
def f(num: Int) = {
  println(s"f($num)")
  num
}

// 1. 값을 매개변수를 넘겼을 때 차이

myPrinter1(1)
//myPrinter1 call data(0) : 1
//myPrinter1 call data(1) : 1

myPrinter2(1) // myPrinter1(1)과 동작 동일
//myPrinter2 call data(0) : 1
//myPrinter2 call data(1) : 1

// 함수를 매개변수로 넘겼을 때 차이

myPrinter1(f(1))
//f(1)
//myPrinter1 call data(0) : 1
//myPrinter1 call data(1) : 1

myPrinter2(f(1)) // myPrinter1(f(1))과 동작이 다름. 매개변수를 참조할 때 마다 f(1) 함수 실행
//f(1)
//myPrinter2 call data(0) : 1
//f(1)
//myPrinter2 call data(1) : 1
//f(1)
```

> `myPrinter1` 과 `myPrinter2` 함수의 파라미터를 비교해보면 차이점을 알 수 있습니다.


# 부분 함수

부분 함수(partial function)는 앞에서 공부한 부분 적용 함수와는 다릅니다.

부분 함수는 일반적인 함수인 완전 함수와 반대되는 개념으로 가능한 입력값 중 일부만 받을 수 있는 함수입니다.

부분 적용 함수는 부분적으로 호출되는 일반 함수입니다.

부분 함수는 매치 표현식과 비슷하게 사용할 수 있습니다.

```scala
def call: Int => String = {
  case 1 => "ONE"
  case 2 => "TWO"
}

println(call(1)) // ONE
println(call(2)) // TWO
println(call(3)) // 에러 발생 scala.MatchError
```


# 함수 리터럴 블록으로 고차 함수 호출하기

위에서 **고차 함수**를 공부할 때는 표현식 블록을 직접 정의하여 사용할 함수에서 매개변수로 호출 하였습니다.

만약 표현식 블록을 재사용할 가능성이 크다면 그렇게 사용하는 것도 좋지만 

만약 한 번 사용되고 말 표현식이라면 **함수 리터럴 블록**으로 매개변수를 넘길 수 있습니다.

```scala
// 두번째 매개변수로 함수 타입을 받는다.
def myString(str: String, fun: String => String): String = {
  if (str == null) {
    return null
  }

  fun(str) // 함수타입으로 받은 매개변수를 실행시킨다.
}

// 위에서 공부한 고차 함수 호출 법
def myConcat(s: String): String = {
  val str = s.concat(",scala")
  str.replace(",", " ")
}
myString("hello", myConcat)

// 함수 리터럴로 고차 함수 호출하기
myString("hello", { s =>
  val str = s.concat(",scala")
  str.replace(",", " ")
})
```

위와 같이 함수 리터럴로 고차 함수를 호출 할 수 있습니다.

다만 한 괄호 안에 포함 시키는 것이 다루기 불편할 수 있기 때문에 

매개변수 그룹을 분리하여 함수 리터럴 블록을 사용할 수 있습니다.

```scala
// 매개변수 그룹을 분리
def myString2(str: String)(fun: String => String): String = {
  if (str == null) {
    return null
  }

  fun(str) // 함수타입으로 받은 매개변수를 실행시킨다.
}

myString2("hello") { s =>
  val str = s.concat(",scala")
  str.replace(",", " ")
}
```
