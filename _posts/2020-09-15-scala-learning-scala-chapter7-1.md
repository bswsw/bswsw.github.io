---
title: "[Learning Scala] Chapter7. 그 외의 컬렉션 (가변, 배열, 시퀀스)"
author: Bae Sangwoo
date: 2020-09-15 21:05:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


이전 포스팅에서 보편적인 컬렉션인 `List`, `Set`, `Map`에 대해 알아봤습니다.

이 세 컬렉션은 모두 불변의 타입이며 `List`는 순서가 있는 컬렉션이고 `Set`과 `Map`은 순서가 없는 컬렉션 입니다.

위 세 컬렉션을 제외한 다른 컬렉션에 대해 알아보겠습니다.


# 가변적인 컬렉션

불변의 타입을 가진 컬렉션은 요소를 추가, 변경, 삭제가 불가능하지만 가변적인 컬렉션을 사용하여 처리할 수 있습니다.

불변 타입의 컬렉션과 대응되는 가변 컬렉션은 아래와 같습니다.

|불변의 타입	|가변적인 대응 타입|
|:----------|:--------------|
|`collection.immutable.List`	|`collection.mutable.Buffer`|
|`collection.immutable.Set`	|`collection.mutable.Set`|
|`collection.immutable.Map`	|`collection.mutable.Map`|

## 새로운 가변 컬렉션 생성하기

가변 컬렉션을 생성할때는 풀패키지명을 포함해서 생성해야하며 필요시에 다시 불변의 리스트로 전환할 수 있습니다.

```scala
val mutableList = collection.mutable.Buffer("a")

mutableList.addOne("b")
mutableList.append("c") // 내부적으로는 addOne 호출
mutableList += "d" // 내부적으로는 addOne 호출
println(mutableList) // ArrayBuffer(a, b, c, d)

val immutableList = mutableList.toList // 불변의 List로 변환
println(immutableList) // remove
```

## 불변의 컬렉션으로부터 가변적인 컬렉션 만들기

위 예제에서 본 `가변 컬렉션 -> 불변 컬렉션` 이 아니라 `불변 컬렉션 -> 가변 컬렉션` 으로 변환도 가능합니다.

```scala
val map = Map("alan" -> 1130, "song" -> 1021) // Map 선언

val mutableList = map.toBuffer // Map -> Buffer
println(mutableList) // ArrayBuffer((alan,1130), (song,1021))

mutableList trimStart 1 // 가변의 컬렉션이므로 수정 가능
println(mutableList) // ArrayBuffer((song,1021))

mutableList += ("bae" -> 1) // 가변의 컬렉션이므로 수정 가능
println(mutableList) // ArrayBuffer((song,1021), (bae,1))

val map2 = mutableList.toMap // Buffer -> Map
println(map) // Map(alan -> 1130, song -> 1021)
```

`List`와 `Set`은 비슷하지만 `Set`은 모든 요소가 유일하므로 중복되는 요소는 삭제됩니다.

```scala
val list = List("a", "b", "c")

val buffer = list.toBuffer // List -> Buffer
buffer += "a" // 요소 추가
println(buffer) // ArrayBuffer(a, b, c, a)

val set = buffer.toSet // buffer -> Set (중복요소 삭제됨)
println(set) // Set(a, b, c)
```

## 컬렉션 빌더 사용하기

`Builder`를 사용하면 추가(append) 연산만 가능하게 됩니다. 필요한 요소를 추가한 후 

`result()` 메소드를 호출하면 이를 최종 컬렉션 타입으로 반환해 줍니다.

```scala
val setBuilder = collection.mutable.Set.newBuilder[String]

setBuilder.addOne("a")
setBuilder += "b"
setBuilder ++= List("a", "b", "c", "d", "e") // List 추가. 내부적으로 addAll 호출 
println(setBuilder) // scala.collection.mutable.HashSet$$anon$4@2aaf7cc2

val mutableSet = setBuilder.result()
println(mutableSet) // HashSet(a, b, c, d, e)
```


# 배열

`Array`는 고정된 크기를 가지며, 내용 변경이 가능하고, 인덱스를 가지고 있는 타입이고 자바의 배열을 감싼 래퍼 타입 입니다.

공식적으로는 컬렉션이 아니지만 `Iterable` 연산을 지원합니다.

```scala
val arr = Array("a", "b", "c")

arr(0) = "d" // 0번째 인덱스 값 변경
println(arr) // [Ljava.lang.String;@77f03bb1
println(arr.mkString("Array(", ", ", ")")) // Array(d, b, c)

val mappedArr = arr.map(_.concat("z")) // Iterable 연산 지원
println(mappedArr.mkString("Array(", ", ", ")")) // Array(dz, bz, cz)

val filteredArr = arr.filter(item => item == "b") // Iterable 연산 지원
println(filteredArr.mkString("Array(", ", ", ")"))
```


# Seq와 시퀀스

`Seq`는 모든 시퀀스의 루트 타입이며 `List`같은 연결리스트와 `Vector` 같은 색인리스트를 포함합니다.

`List`를 생성하는 다른 방법으로 `Seq`를 호출할 수 있습니다.

```scala
val seq = Seq('C', 'M', 'Y', 'K')
println(seq) // List(C, M, Y, K)

val list = List('C', 'M', 'Y', 'K')
println(list) // List(C, M, Y, K)

println(seq == list) // true
```

![](/assets/img/posts/2020-09-15-scala-learning-scala-chapter7-1/scala-seq-tree.jpg)
*시퀀스 컬렉션의 계층구조 (출처 : https://www.oreilly.com/library/view/learning-scala/9781449368814/ch07.html)*

# 시퀀스 타입

|이름	|설명|
|:---|:---|
|`Seq`	|모든 시퀀스의 루트, `List()`의 간단한 방법|
|`IndexedSeq`	|색인 시퀀스의 루트, `Vector`의 손쉬운 방법|
|`Vector`	|색인된 접근을 위해 `Array` 인스턴스에 의해 지원받는 리스트|
|`Range`	|정수의 범위, 데이터를 즉시 생성함|
|`LinearSeq`	|선형(연결 리스트) 시퀀스의 루트|
|`List`	|구성요소들의 단방향 연결 리스트|
|`Queue`	|선입선출 리스트|
|`Stack`	|후입선출 리스트|
|`Stream`	|지연 리스트, 항목들은 그 항목에 접근할 때 추가됨|
|`String`	|문자(Character)의 컬렉션|
