---
title: "[Learning Scala] Chapter6. 보편적인 컬렉션 (2)"
author: Bae Sangwoo
date: 2020-09-08 09:58:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


이전 포스팅에서 기본적인 컬렉션에 대해 알아보았습니다.

이번 포스팅에서는 앞서 공부한 컬렉션들에 대해 조금 더 디테일한 기능에 대해 알아보겠습니다.

(`List` 컬렉션을 중점으로 두고 작성하겠습니다.)


# List에는 무엇이 있는가?

## List 정의하기

리스트를 정의하는 법은 다양하게 있지만 간단하게 몇가지를 알아보겠습니다.

```scala
// 빈 리스트 선언 (리스트 타입을 지정해줘야합니다.)
val list = List[String]()
  
// String List 정의 (값의 타입을 추론합니다.)
val colors = List("red", "green", "blue")

// List 안에 List 선언
val listInList = List(List(1, 3, 5), List(2, 4, 6))

// List 인에 Tuple 선언 (Map과 유사해보이지만 Map 만의 api를 제공하진 않습니다.)
val tuples = List(('A', 1), 'B' -> 2, 'C' -> 3)
```

## 요소 참조하기

`List`의 요소를 참조할 때는 가장 기본적으로 인덱스 번호로 참조할 수 있고

첫번째, 마지막 요소를 참조하는 메소드도 따로 존재하고 스칼라만의 특이한 `tail` 메소드가 있습니다.

```scala
val colors = List("red", "green", "blue")

colors(1) // 인덱스 번호로 요소를 호출하기.
colors.head // 리스트의 첫번째 요소 가져오기.
colors.last // 리스트의 마지막 요소 가져오기.
colors.tail // head 요소를 제외한 나머지 요소들 가져오기.
```

## List 확인하기

`List`에 요소가 존재하는지, 비어있는지 등을 확인하는 메소드도 당연히 있습니다.

다만 단순히 `List`의 요소 존재여부만 판단할 때 `size` 메소드 보다는 `isEmpty`를 사용하는 것이 조금 더 효율적입니다.

```scala
val colors = List("red", "green", "blue")

// 아래 두 메소드는 결과는 동일하지만 size 메소드는 매번 리스트를 모두 순회하므로 이 경우에는 isEmpty가 더 효율적입니다.
colors.size < 1 // false
colors.isEmpty // false

// isEmpty 를 반대로 구현한 메소드 입니다. !isEmpty
println(colors.nonEmpty) // true
```

## 생성 연산자

스칼라에는 `Nil` 이라는 타입이 있습니다. 아무 것도 없는 리스트를 표현하는 타입이며 모든 리스트의 종점엔 `Nil` 인스턴스가 있습니다.

또한 지금까지 공부한 대부분의 연산자들은 왼쪽 결합형 (left-associative)이었으나 지금 공부할 리스트 생성 연산자 (`::`)는 오른쪽 결합형(right-associative) 입니다.

`Nil`과 생성 연산자(`::`)를 사용하여 리스트를 생성할 수 있습니다.

```scala
val list = 1 :: 2 :: 3 :: Nil
println(list) // List(1, 2, 3)
```

> `::`는 단순히 `List`에서 제공하는 메소드이며 1, 2, 3을 `Nil` 앞에 요소로 추가한다고 볼 수 있습니다.


# List의 산술 연산

`Int`나 `String` 타입의 값이나 변수에 사용되는 산술 연산이 있듯 `List`에서도 사용할 수 있는 산술 연산이 다양하게 존재합니다.

바로 위에서 공부했던 생성 연산자 `::`도 마찬가지 입니다.

|이름	|예제	|설명|
|:---|:---|:---|
|`::`	|`1 :: 2 :: Nil`	|리스트에 개발 요소를 덧붙임, 오른쪽-결합형 연산자|
|`:::`	|`List(1, 2) ::: List(2, 3)`	|이 리스트 앞에 다른 리스트를 추가함, 오른쪽-결합형 연산자|
|`++`	|`List(1, 2) ++ Set(3, 4, 3)`	|이 리스트에 다른 컬렉션을 덧붙임|
|`==`	|`List(1, 2) == List(1, 2)`	|두 컬렉션의 타입과 내용이 동일하다면 참을 반환|
|`distinct`	|`List(3, 5, 4, 3, 4).distinct`	|중복 요소가 없는 리스트를 반환|
|`drop`	|`List('a', 'b', 'c', 'd') drop 2`	|리스트의 첫 번째 n개의 요소를 제거|
|`filter`	|`List(23, 8, 14, 21) filter (_ > 18)`	|`Boolean` 함수를 통과한 요소들을 반환|
|`flatten`	|`List(List(1, 2), List(3, 4)).flatten`	|다중 리스트를 단일 리스트로 변환|
|`partition`	|`List(1, 2, 3, 4, 5) partition (_ < 3)`	 |리스트의 요소들을 참/거짓 함수 결과에 따라 분류하여 두 개의 리스트를 포함하는 튜플로 생성|
|`reverse`	|`List(1, 2, 3).reverse`	|리스트 요소들의 순서를 거꾸로 함|
|`slice`	|`List("apple", "to) slice (1, 3)`	|리스트 중 첫 번째 인덱스부터 두 번째 인덱스 - 1까지에 해당하는 부분을 반환|
|`sortBy`	|`List("apple", "to") sortBy (_.length)`	|주어진 함수로부터 반환된 값으로 리스트 순서를 정렬|
|`sorted`	|`List("apple", "to").sorted`	|핵심 스칼라 타입의 리스트를 자연값 기준으로 정렬함|
|`splitAt`	|`List(2, 3, 5, 7) splitAt 2`	|`List` 요소들을 주어진 인덱스의 앞에 위치하는지 뒤에 위치하는지에 따라 두 리스트의 튜플로 분류함|
|`take`	|`List(2, 3, 5, 7, 11, 13) take 3`	|리스트에서 첫 번째 n개의 요소들을 추출|
|`zip`	|`List(1 , 2) zip List("a", "b")`	|두 리스트를 각 인덱스에 해당하는 요소들끼리 구성된 튜플의 리스트로 결합|

> `List`에는 이렇게 다양한 산술 연산이 존재합니다.
> 더불어 `List`는 연결 리스트이기 때문에 리스트를 순회하는 연산을 할 때는 주의해야 합니다. 일반적으로는 앞에서 연산하는 것이 가장 좋습니다.


# 고차함수 사용해보기

위 표에서 고차함수는 `filter`, `partition`, `sortBy` 가 있는데 가볍게 살펴보겠습니다.

```scala
// 20보다 큰 요소만 추출
List(23, 8, 14, 21).filter(_ > 20) // List(23, 21)

// 3보다 작은 것과 그렇지 않은 것들로 나눠서 튜플로 반환
List(1, 2, 3, 4, 5).partition(_ < 3) // (List(1, 2),List(3, 4, 5))

// 문자열의 길이가 짧은 것부터 정렬
List("apple", "to").sortBy(_.length) // List(to, apple)
```


# List 매핑

`map` 메소드를 사용하여 어떠한 함수를 `List`의 모든 요소에 적용하고 새로운 리스트로 결과를 반환할 수 있습니다. 

책에서 엄선한 map 관련 메소드를 살펴보겠습니다.

```scala
// 주어진 함수를 이용하여 각 요소를 반환
List("alan", "sangwoo").map(s => s"$s bae") // List(alan bae, sangwoo bae)

// 주어진 함수를 이용하여 각 요소를 변환하고 그 결과 리스트를 이 리스트에 평면화(flatten) 함
List("milk,tee").flatMap(_.split(",")) // List(milk, tee)

// 각 요소를 부분 함수를 사용하여 변환하고, 해당 함수를 적용할 수 있는 요소를 유지함.
List(0, 1, 2) collect { case 1 => "ok" } // List(ok)
List(0, 1, 2) collect {
  case 1 => "ok"
  case 2 => "fail"
} // List(ok, fail)
```


# List 축소하기

지금까지 `List`의 사이즈와 구조를 변경하거나 완전히 다른 값과 타입으로 전환하는 방법을 알아보았습니다.

이번에는 리스트를 단일 값으로 축소하는 방법을 알아보겠습니다.

## 수학적인 축소 연산

|이름	|예제	|설명|
|:---|:---|:---|
|`max`	|`List(41, 59, 26).max`	|리스트의 최대값 구하기|
|`min`	|`List(10.9, 32.5, 4.23, 5.67).min`	|리스트의 최소값 구하기|
|`product`	|`List(5, 6, 7).product`	|리스트의 숫자들을 곱하기|
|`sum`	|`List(11.3, 23.5, 7.2).sum`	|리스트의 숫자들을 합하기|

## 부울(bool) 축소 연산

|이름	|예제	|설명|
|:---|:---|:---|
|`contains`	|`List(34, 29, 18) contains 29`	|리스트에 이 요소를 포함하고 있는지름 검사함|
|`endsWith`	|`List(0, 4, 3) endsWith List(4, 3)`	|리스트가 주어진 리스트로 끝나는지를 검사함|
|`exists`	|`List(24, 17, 32) exists (_ < 18)`	|리스트에서 최소 하나의 요소에 대해 조건자가 성립하지는지를 검사함|
|`forall`	|`List(24, 17, 32) forall (_ < 18)`	|리스트의 모든 요소에 대해 조건자가 성립하는지를 검사함|
|`startsWith`	|`List(0, 4, 3) startsWith List(0)`	|리스트가 주어진 리스트로 시작하는지를 테스트함|

## 일반적인 리스트 축소 연산

|이름	|예제	|설명|
|:---|:---|:---|
|`fold`	|`List(4, 5, 6).fold(0)(_ + _)`	|주어진 시작값과 축소 함수로 리스트를 축소|
|`reduce`	|`List(4, 5, 6).reduce(_ + _)`	|리스트의 첫번째 요소를 시작으로 주어진 축소 함수로 리스트를 축소|
|`scan`	|`List(4, 5, 6).scan(0)(_ + _)`	|시작값과 축소 함수를 취하여 각각의 누곗값의 리스트를 반환함|

> 위 세 가지 연산은 `Left`, `Right` 연산도 함께 제공 됩니다. (ex: `foldLeft`, `foldRight`)


# 컬렉션 전환하기

`List`를 기준으로 이번 포스팅을 진행하지만 필요에 따라 

`Map`이나 `Set` 등 다른 컬렉션 타입으로 변환이 필요할 수 있을때 사용하는 연산이 있습니다.

## 컬렉션 전환 연산

|이름	|예제	|설명|
|:---|:---|:---|
|`mkString`	|`List(24, 99, 104).mkString(", ")`	|주어진 구분자를 사용하여 컬렉션을 `String`으로 변환|
|`toBuffer`	|`List('f', 't').toBuffer`	|불변의 컬렉션을 가변적인 컬렉션으로 전환|
|`toList`	|`Map("a" -> 1, "b" -> 2).toList`	|컬렉션을 `List`로 전환|
|`toMap`	|`Set(1 -> true, 3 -> true).toSet`	|두 요소(길이)로 구성된 튜플의 컬렉션을 `Map`으로 전환|
|`toSet`	|`List(2, 5, 5, 3, 2).toSet`	|컬렉션을 `Set`으로 전환|
|`toString`	|`List(2, 5, 5, 3, 2).toString`	|컬렉션을 String으로 컬렉션의 타입을 포함하여 만듦|

## 자바와 스칼라 컬렉션 호환성

스칼라는 JVM 으로 컴파일하고 그 위에서 동작하기 때문에 JDK와 상호작용하고 어떤 자바 라이브러리도 추가할 수 있어야 합니다.

기본적으로 자바 컬렉션과 스칼라 컬렉션은 호환되지는 않지만 아래의 `import` 구문을 추가하면 서로 변환이 가능한 연산이 추가 됩니다.

```scala
import scala.jdk.CollectionConverters._

object ScalaToJava extends App {
  
  val scalaList = List(12, 29)
  val list = scalaList.asJava // java.util.List
  
  val javaList = new java.util.ArrayList[String](5)
  val d = javaList.asScala // mutable.Buffer 
}
```

> 서적에는 `collecton.JavaConverters._`를 `import` 하라고 기술되어 있지만 deprecated 되었고
> 2.13.0버전 부터는 `scala.jdk.CollectionConverters._`를 사용하도록 되어 있습니다.


# 컬렉션으로 패턴 매칭하기

컬렉션을 이용하여 매치 표현식을 사용할 수 있습니다.

패턴 매칭은 스칼라의 평범한 연산일 뿐 아니라 스칼라의 핵심 특징이며 스칼라 데이터 구조에 광범위하게 적용됩니다. 

잘 사용한다면 다른 언어에서 비슷한 작업을 할 때보다 로직을 간결하게 작성할 수 있습니다.

```scala
val statuses = List(500, 404)

// 리스트의 head 값을 기준으로 500 미만 여부 체크하여 분기
val msg1 = statuses.head match {
  case x if x < 500 => "okay"
  case _ => "error"
}

println(msg1) // error

// 리스트에 500 포함 여부 체크하여 분기
val msg2 = statuses match {
  case x if x contains 500 => "has error"
  case _ => "okay"
}

println(msg2) // has error

// 패턴 가드를 사용하여 분기
val msg3 = statuses match {
  case List(500, x) => s"error by $x"
  case List(e, x) => s"$e by $x"
}

println(msg3) // error by 403

// 리스트의 head와 tail을 분해
val result = List('r', 'g', 'b') match {
  case head :: tail => s"$head :: $tail"
  case Nil => ' '
}

println(result) // r :: List(g, b)

// 튜플도 패턴 매칭을 사용할 수 있음
val result2 = ('h', 204, true) match {
case (_, _, false) => 501
case ('c', _, true) => 302
case ('h', x, true) => x // 여길 통과합니다.
case (c, x, true) =>
  println(s"did not expect code $c")
  x
}

println(result2) // 204
```

이상으로 기본적인 컬렉션인 `List`에 대해 알아보고 `Map`과 `Set`에 대해서도 가볍게 알아봤습니다.

이번 포스팅으로 알게된 `List`, `Map`, `Set` 뿐만 아니라 다른 컬렉션들이 많이 존재하니 

각 컬렉션들의 용도를 잘 구분하여 사용할 수 있어야할 것 같습니다.
