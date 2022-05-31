---
title: "[Learning Scala] Chapter7. 그 외의 컬렉션 (모나딕 컬렉션)"
author: Bae Sangwoo
date: 2020-09-16 10:16:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


모나딕 컬렉션(monadic collection) 이란 `Iterable` 연산과 비슷한 변형 연산은 지원하지만 

하나 이상의 요소를 포함할 수 없는 컬렉션을 말합니다.


# Option 컬렉션

크기가 1인 컬렉션이며 값의 존재 여부를 나타내며 `null`의 안전한 처리를 하는데 사용되는 경우가 많습니다.

또한 자바의 `Optional`과 비슷하다고 볼 수 있습니다.

하나의 값에 대한 여러 연산 체인이 있을 때 이 연산들을 안전하게 처리하는데 좋은 방법이 될 수 있습니다.

```scala
val name = Option("alan")

name match {
  case Some(n) => println(s"name : $n") // name : alan
  case None => println("name is null")
}
```

위 예제와 같이 `Some`과 `None` 타입으로 값의 존재를 구분할 수 있습니다.

혹은 아래와 같이 `isDefined`, `isEmpty`를 사용하여 판단할 수도 있습니다.

```scala
val name = Option("alan")

if (name.isDefined) {
  println(s"name is defined : $name") // name is defined : Some(alan)
} else {
  println("name is empty")
}
```

일반적인 컬렉션에서도 요소를 추출할 때 안정성을 위해 `Option`을 리턴하는 메소드들이 있습니다.

```scala
val words = List("alan", "bae", "sangwoo")

println(words.headOption) // Some(alan)
println(words.find(_.endsWith("song"))) // None
```

`Option`에서 값을 꺼낼 때 다양한 방법이 있지만 `get()` 메소드를 사용하는 것은 좋지 않습니다.

만약 해당 옵션이 `None`인스턴스였을때 아래와 같은 런타임 익셉션이 발생하므로 안전하지 않습니다.

![](/assets/img/posts/2020-09-15-scala-learning-scala-chapter7-2/monad-option.png)
*Option의 get() 메소드는 안전하지 않습니다!*

안전하게 `Option`의 값을 꺼내는 방법은 아래 예제와 같이 몇가지가 있습니다.

```scala
val mySome = Option("alan")
val myNone = Option(null)

// None일 경우 기본값 sangwoo 리턴
mySome.getOrElse("sangwoo") // alan
myNone.getOrElse("sangwoo") // sangwoo

// None일 경우 sangwoo 리턴, Some일 경우 고차함수 실행
// foldRight, foldLeft, recudeXXX 등도 사용할 수 있습니다.
mySome.fold("sangwoo")(x => s"$x.bae") // alan.bae
myNone.fold("sangwoo")(x => s"$x.bae") // sangwoo

// None일 경우 주어진 파라미터의 Option을 리턴
mySome orElse Option("sangwoo") // Some(alan)
myNone orElse Option("sangwoo") // Some(sangwoo)

// 매치표현식으로도 표현할 수 있습니다.
mySome match {
  case Some(m) => println(s"This is Some $m") // This is Some alan
  case None => println("This is None")
}
```

# Try 컬렉션

기존의 자바 등에서 에러 처리를 위해 `try... catch...` 블록을 주로 사용하지만 

`util.Try` 컬렉션을 사용하면 스칼라만의 방식으로 에러를 처리할 수 있습니다. 

저자의 간단한 설명을 그대로 적자면 아래와 같습니다.

> 스칼라는 `try.. catch...` 블록을 지원하며, 이 경우 `catch` 블록에는 발생한 에러를 매칭하기 위한 일련의 `case`문을 포함하고 있다.
> 하지만 나는 `util.Try`만 사용할 것을 추천하는데, 이는 에러를 처리하기에 더 안전하고, 더 표현력 있으며, 완전한 모나딕 접근법을 제공하기 때문이다.

`Try`를 구현한 두 개의 서브 타입이 있으며 표현식의 반환 값을 포함한 `Success`와 발생한 익셉션을 포함한 `Failure`가 있습니다.

아래 예제를 보시면 이해가 되실 것 같아요!

```scala
val emptyList = List()
val list = List("alan")

Try { emptyList.head } // Failure(java.util.NoSuchElementException: head of empty list)
Try { list.head } // Success(alan)
```

위 예제에서 나온 리턴 값을 가지고 에러를 처리하는 여러가지 방법을 알아보겠습니다.

```scala
def divide(a: Int, b: Int) = a / b

// 매치표현식을 사용할 수 있습니다.
Try { divide(10, 0) } match {
  case Success(value) => println(s"success : $value")
  case Failure(exception) => println(s"exception : ${exception.getMessage}") // exception : / by zero
}

// map : Success인 경우 새로운 값에 내장된 값을 매핑하는 함수를 호출함
Try { divide(10, 2) }.map { divide(_, 2) } // Success(2)

// flatMap : Success인 경우 Try를 반환하는 함수를 호출함 (위 map과 아래 결과물은 동일합니다. 문법적인 차이점을 찾아보세요!)
Try { divide(10, 2) }.flatMap { result => Try { divide(result, 2) } } // Success(2)

// toOption : Option으로 전환하고 Success일 경우 결과를 Some에 넣어서 반환
// Success -> Some
Try { divide(10, 2) }.toOption // Some(5)
// Failure -> None
Try { divide(10, 0) }.toOption // None

// Success에 내장된 값을 가져오거나 Falure일 경우 ERROR를 리턴
Try { divide(10, 2) }.getOrElse("ERROR") // 5
Try { divide(10, 0) }.getOrElse("ERROR") // ERROR
```


# Future 컬렉션

`Future` 컬렉션은 이름에서 유추할 수 있듯 백그라운드 작업을 개시하는 컬렉션 입니다.

기본적으로 스칼라 코드는 JVM의 메인 스레드에서 동작하지만 `Future`를 함수로 호출하면 

별도의 스레드에서 함수를 실행하여 백그라운드 작업이 가능하게 합니다.

주의해야할 점은 백그라운드 작업이 진행 중일 수 있기 때문에 `Future`의 값을 즉시 사용하지 못할 수 있다는 것과 

함수를 동시에 실행하기 위한 `context`를 지정해야한다는 점 입니다.

## 기본 사용법

```scala
import scala.concurrent.ExecutionContext.global // 컨텍스트 import
import scala.concurrent.Future

object ScalaFuture extends App {

  println("1")

  Future {
    println("2")
  }(global) // 컨텍스트 지정

  println("3")

}
```

위 예제를 실행하면 실행 순서가 어떻게 될까요?

퓨처는 백그라운드에서 실행되기 때문에 `1 -> 3 -> 2` 순으로 출력되는 것을 보실 수 있습니다.

또한 위 예제처럼 명시적으로 `context` 를 지정할 수도 있지만 `묵시적` 으로도 지정이 가능합니다.

```scala
import scala.concurrent.ExecutionContext.Implicits.global // 묵시적 context 지정 (Implicit)
import scala.concurrent.Future

object ScalaFuture extends App {

  println("1")

  Future {
    println("2")
  } // 묵시적으로 context가 지정되어 파라미터가 생략됨

  println("3")

}
```

> 서적 기준으로 `묵시적` 이라고 표현했지만 암묵적, 암시적 등으로도 표현됩니다.

## 비동기식으로 퓨처 처리하기

위 예제들이 기본적으로 비동기적으로 퓨처를 실행하는 방법입니다. 

추가적으로 퓨처의 반환값이나 예외를 포함한 `Try` 객체를 받아서 처리를 하거나 콜백 함수를 실행하여 처리할 수도 있습니다.

아래 표는 비동기식 퓨처를 처리하기 위한 연산입니다.

|이름	|예제	|설명|
|:---|:---|:---|
|`fallbackTo`	|`nextFtr(1) fallbackTo nextFtr(2)`	|두번째 퓨처를 첫번째에 연결하고 새로운 종합적인 퓨처를 반환함.<br/>첫번째 퓨처가 성공적이지 않다면 두번째 퓨처가 호출됨.|
|`flatMap`	|`nextFtr(1).flatMap(int => nextFtr())`	|두번째 퓨처를 첫번째 퓨처에 연결하고 새로운 종합적인 퓨처를 반환함.<br/>첫번째가 성공적이라면 그 반환값이 두번째를 호출하는데 사용됨.|
|`map`	|`nextFtr(1) map (_ * 2)`	|주어진 함수를 퓨처에 연결하고 새로운 종합저긴 퓨처를 반환함.<br/>퓨처가 성공적이라면 그 반환값이 해당 함수를 호출할 때 사용됨.|
|`onComplete`	|`nextFtr() onComplete { _ getOrElse 0 }`	|퓨처의 작업이 완료된 후 주어진 함수가 값 또는 예외를 포함한<br/>`util.Try`를 이용하여 호출함|
|`onFailure`	|`nextFtr() onFailure { case _ => "error!" }`	|퓨처의 작업이 예외를 발생시키면 주어진 함수는 그 예외를 가지고 호출됨|
|`Future.sequence`	|`Future sequence List(nextFtr(1), nextFtr(5))`	|주어진 시퀀스에서 퓨처를 병행으로 실행하여 새로운 퓨처를 반환함.<br/>시퀀스 내의 모든 퓨처가 성공하면 이들의 리스트가 반환됨. 그렇지 않으면 그 시퀀스 내에서 처음으로 발생한 예외가 반환됨.|

다양한 연산이 존재하지만 간단히 `flatMap`과 `map`의 차이를 알아보겠습니다.

이 둘은 퓨처가 중첩으로 있을때 차이가 있습니다.

`flatMap`은 이름 처럼 평평하게 `map`을 한다고 생각하면 됩니다.

이론적으로보단 직접 코드를 작성해보시면 실제로 실행되는 프로세스는 동일하지만 결과값이 다른 것을 알 수 있습니다.

```scala
// future 안에 future가 있기 때문에 결과 값은 Future[Future(..)]
val f3: Future[Future[Unit]] = f1().map(_ => f2())

// future 안에 future가 있지만 하나의 종합적인 future로 반환됨 
val f4: Future[Unit] = f1().flatMap(_ => f2())
```

위 표에 작성되어 있지는 않지만 개인적으로 유용해보이는 연산인 `isCompleted`를 소개해드립니다.

```scala
val f = Future {
  println("f")
}

while (!f.isCompleted) {
  println("wait......")
}
```

위 프로그램을 실행시키면 `Future`가 완료될 때 까지 `wait......` 가 여러번 출력되는 것을 알 수 있습니다.

이 외 표에 작성되어 있는 연산들을 직접 작성하고 확인해보시면 좋습니다. :)


## 동기식으로 퓨처 처리하기

동기식으로 퓨처를 처리하게 되면 백그라운드 스레드가 완료될 때 까지 스레드를 차단하기 때문에 

자원 소모가 더 클 수 있기 때문에 트래픽이 많다면 비동기적으로 처리하는 것이 좋습니다.

단, 스레드를 차단하고 백그라운 작업이 성공적이든 아니든 완료되길 기다려야할 때 사용할 수 있습니다.

추가적으로 동기적으로 퓨처를 처리하기 위해서는 `Duration`을 지정해야하고 

그 기간 내에 완료되지 않았을 때 `java.util.concurrent.TimeoutException`이 발생한다는 점을 숙지해야합니다.

```scala
import scala.concurrent.ExecutionContext.Implicits.global
import scala.concurrent.duration.{Duration, SECONDS}
import scala.concurrent.{Await, Future}

object ScalaFuture extends App {
  def f() = Future {
    println("1")
  }

  val duration = Duration(10, SECONDS)
  Await.result(f(), duration)

  println("2")
}
```

위 예제는 이전 비동식 예제와 다르게 `1 -> 2` 순서대로 정확하게 동작하는 것을 볼 수 있습니다.

단 `duration` 값으로 지정된 기간 안에 퓨처가 완료되지 않는다면 예외를 발생시킵니다.

이상으로 다양한 컬렉션에 대해 알아봤습니다.

스칼라에서 모나딕 컬렉션은 함수형 프로그래밍을 하는데 핵심적인 역할을 한다고 생각하기 때문에 잘 학습해야할 것 같습니다.

더불어 컬렉션 타입 별 동일한 네이밍의 연산들이 존재하는데 (`map`, `flatMap` 등) 처리되는 방식은 

컬렉션에 따라 조금씩 다르기 때문에 헷갈리지 말아야할 것 같습니다.

다음 포스팅부터는 객체지향 스칼라에 대해 공부해 보겠습니다.

감사합니다.
