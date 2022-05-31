---
title: "[Learning Scala] Chapter6. 보편적인 컬렉션 (1)"
author: Bae Sangwoo
date: 2020-09-07 17:00:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*

스칼라는 JVM 기반 언어이므로 기본적으로 자바 컬렉션이 사용이 가능합니다. 

하지만 추가적으로 스칼라만의 다양한 고차 연산도 있있습니다.

대부분 `Iterable`의 서브타입이고 컬렉션 데이터를 처리하는 메소드들을 제공합니다.


# List

`List`는 불변의 단방향 연결 리스트입니다.

`List`를 가볍게 아래와 같이 사용해 볼 수 입습니다.

```scala
val list: List[Int] = List(11, 30, 10, 21) // List.apply(11, 30, 10, 21)

println(s"list size : ${list.size}, list : $list") // list size : 4, list : List(11, 30, 10, 21)

println(list(1)) // 30
println(list.head) // 11
println(list.last) // 21
println(list.tail) // List(30, 10, 21)
```


# List를 루프(loop)로 사용하기

앞에서 공부한 `for` 문을 사용하여 `List`를 루핑할 수 있습니다.

```scala
val list: List[Int] = List(11, 30, 10, 21) // List.apply(11, 30, 10, 21)

var total = 0
for (i <- list) {
  total += i
}

println(total) // 72
```


# 고차함수 사용하기

`List` 외에 다른 컬렉션에서도 사용 가능한 고차 함수를 아래 예제처럼 적용할 수 있습니다.

```scala
val list = List("hello", "world", "scala")

// foreach: 프로시저를 받아서 리스트의 모든 요소에서 호출 시킨다.
list.foreach((s: String) => print(s"$s,")) // hello,world,scala,
list.foreach { s => print(s"$s,") } // hello,world,scala,

// map: 리스트의 모든 요소을 다른 값이나 타입으로 전환하는 함수를 받는다.
list.map((s: String) => s.length) // List(5, 5, 5)
list.map { s => s.length } // List(5, 5, 5)

// reduce: 리스트 요소들를 단일 항목으로 결합한다.
val data = list.reduce((s1: String, s2: String) => {
  println(s"($s1, $s2)")
  s1 + s2
})
println(data) // helloworldscala

// 위 reduce 동작 순서
// (hello, world)
// (helloworld, scala)
```


# Set

`Set`은 각 요소들이 유일하고 순서가 없는 컬렉션이며 동작은 `List`와 유사합니다.

```scala
val data = Set(10, 20, 30, 20, 10)

println(data) // Set(10, 20, 30)=
println(data.reduce((a: Int, b: Int) => a + b)) // 60 (sum과 동일하게 동작)
```


# Map

`Map`은 불변의 키-값 저장소이며 키-값 쌍을 `Tuple`로 기술하면 되며 관계 연산자(`->`)를 사용할 수 있습니다.

`Map` 또한 `Iterable`의 서브 타입 이므로 `List`와 동일한 연산을 지원합니다.

```scala
val rgbMap = Map(
  "red" -> 0xff0000,
  "green" -> 0xff00,
  "blue" -> 0xff
)

rgbMap("red") // 16711680
rgbMap("green") | rgbMap("blue") // 65535
rgbMap.contains("black") // false

rgbMap.foreach(pair => println(pair))
// (red,16711680)
// (green,65280)
// (blue,255)
```

지금까지 이렇게 기본적인 컬렉션인 `List`, `Set`, `Map`에 대해 간단히 알아보았습니다.

이런 컬렉션들로 어떤 작업을 할 수 있는지는 다음 포스팅에서 알아보겠습니다.
