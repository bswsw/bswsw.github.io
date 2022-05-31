---
title: "[Leaning Scala] Chapter9. 트레이트"
author: Bae Sangwoo
date: 2020-10-17 10:36:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


# 트레이트 (trait)

트레이트는 다중 상속을 가능하게 하는 스칼라의 유형 중 하나 입니다.

다른 클래스 유형과 마찬가지로 트레이트는 하나 이상의 클래스를 확장할 수는 없지만 동시에 여러 트레이트를 확장할 수는 있습니다.

트레이트는 직접 인스턴스화가 될 수 없고 클래스 매개변수를 가질 수는 없지만 타입 매개변수를 사용할 수 있어 재사용성이 뛰어납니다.

```scala
// 트레이트 정의
trait Article {
  def publish()
}

// 트레이트 구현
class MyArticle(title: String, content: String) extends Article {
  override def publish(): Unit = {
    println(s"title : $title, content : $content")
  }
}

val article = new MyArticle("제목", "내용")
article.publish() // title : 제목, content : 내용
```

위의 예제로 트레이트를 클래스로 확장하는 것을 볼 수 있습니다.

하나 이상의 트레이트를 확장할 때엔 `with` 키워드를 사용할 수 있습니다.

```scala
// 트레이트 정의
trait Article {
  def publish()
}

// 두번째 트레이트 정의
trait Logger {
  def log(): Unit = {
    println("Log")
  }
}

// 여러 트레이트 확장
class MyArticle(title: String, content: String) extends Article with Logger {
  override def publish(): Unit = {
    println(s"title : $title, content : $content")
  }
}

val article = new MyArticle("제목", "내용")
article.publish() // title : 제목, content : 내용
article.log() // Log
```

위의 예제를 보면 스칼라에서 트레이트를 통해서 다중 상속을 지원하는 것 처럼 보입니다.

본래 *JVM*은 하나의 부모 클래스만 확장 할 수 있는데 어떻게 가능한걸까요?


실제로는 컴파일러에서 *선형화(linearization)* 라는 상속된 클래스와 트레이트의 수평적인 리스트를 받아서 

한 클래스가 다른 클래스를 확장하는 수직적 체인으로 재구성하는 절차로 이를 가능하게 합니다.

이는 단일 상속만 지원하는 실행 환경에서 다중 상속을 지원하는 일종의 대처 방안이며 

JVM이 단일 상속만 지원한다는 사실이 모든 클래스 계층구조가 비결정적이며 

경쟁 관계의 구성원을 가진 두 트레이트로 혼란을 일으킬 가능성을 배제한다는 점을 보장합니다.

선형화를 통해 클래스와 트레이트를 재배치할 때는 가장 낮은 서브클래스부터 가장 높은 클래스까지 오른쪽 부터 왼쪽 순으로 배치합니다.

```scala
// 코드 작성
class D extends A with B with C

// 컴파일 후
class D extends C extends B extends A
```

아래 예제를 실행시켜 보면 이해하실 수 있습니다.

```scala
trait A {
  override def toString: String = "A"
}

trait B {
  override def toString: String = s"B->${super.toString}"
}

trait C {
  override def toString: String = s"C->${super.toString}"
}

class D extends A with B with C {
  override def toString: String = s"D->${super.toString}"
}

val d = new D
println(d.toString) // D->C->B->A
```

선형화의 이점으로 부모 클래스의 행위를 재정의 하고 베이스 클래스에 서브 클래스가 결합될 때 

부가적인 기능을 추가하는 트레이트를 만들 수 있습니다.

```scala
// 부모 클래스
class A(val data: String) {
  def print(): Unit = {
    println(s"A data : $data")
  }
}

// 부모 클래스를 확장한 서브 클래스 1
trait B extends A {
  override def print(): Unit = {
    println(s"B data : $data")
  }
}

// 부모 클래스를 확장한 서브 클래스 2
trait C extends A {
  override def print(): Unit = {
    println(s"C data : $data")
  }
}

// 부모 클래스와 서브 클래스 1 확장
class D(data: String) extends A(data) with B

// 부모 클래스와 서브 클래스 2 확장
class E(data: String) extends A(data) with C

new D("alan").print() // B data : alan
new E("alan").print() // C data : alan
```

위 예제를 통해 부모 클래스에 부가적인 기능을 추가하기 위해 트레이트를 사용하는 것을 볼 수 있고 

더불어 추상클래스나 자바의 인터페이스와 유사해 보이지만 전혀 다른 것을 알 수 있습니다.

> 트레이트는 어떻게 선형화가 트레이트들을 확장하는 클래스의 계층구조를 형성하는지를 이해하는 것이 중요합니다.


# 셀프 타입

셀프 타입 (self type)을 사용하여 트레이트를 확장할 때 특정 타입이 함께 사용되어야 함을 명시할 수 있습니다.

해당 트레이트에 지정된 셀프 타입에서만 함께 사용할 것을 요구하는 제약사항이라고 볼 수 있습니다.

```scala
// 기본 클래스
class BaseProfile(val name: String)

// 기본 클래스를 셀프 타입으로 가지는 트레이트
trait Printer {
  // 셀프 타입 지정
  self: BaseProfile =>

  // 셀프 타입인 기본 클래스의 필드를 메소드에서 사용함
  def print(): Unit = {
    println(s"name : ${self.name}")
  }
}
```

위 예제는 `Printer` 트레이트가 셀프 타입으로 `BaseProfile` 클래스를 가지는 것을 볼 수 있으며 

트레이트에서 셀프 타입의 필드를 사용할 수 있습니다.

이 때 특정 클래스에서 `Printer` 트레이트를 확장할 때 제약이 생겨 `BaseProfle`을 확장하지 않으면 `Printer` 트레이트를 확장할 수 없습니다.

셀프 타입을 가지는 트레이트를 단독으로 확장했을 때 위와 같은 에러를 발생시킵니다.

정상적으로 `Printer` 트레이트를 확장하기 위해서는 아래 예제와 같이 

`Printer` 트레이트의 셀프 타입인 `BaseProfile`을 확장하여야 합니다.

```scala
class Profile(name: String) extends BaseProfile(name) with Printer

val profile = new Profile("alan")
profile.print() // name : alan
```

> 셀프타입은 트레이트의 제약사항이나 요구사항을 추가할 때 특정 맥락에서만 사용되는 것을 보장하는 안전한 방식입니다.


# 트레이트를 통한 인스턴스화

지금까지 트레이트를 확장하는 방식으로 `extends` 또는 `with` 키워드를 사용하였습니다.

또 다른 방식으로는 어떠한 클래스를 인스턴스화 할 때 트레이트를 추가할 수도 있습니다.

```scala
class A

trait B

val a = new A with B
```

간단하게 보면 위 예제와 같이 `A` 클래스를 인스턴스화 할 때 `with` 키워드를 사용하여 `B` 트레이트를 추가할 수 있습니다.

이 때 주의해야 할 개념은 트레이트의 선형화를 통해 `B` 트레이트가 `A` 클래스를 확장했다는 점을 기억해야 합니다.

그렇기 때문에 확장의 개념인 `extends` 가 아니라 사용하는 클래스를 트레이트에 의해 *확장된다*는 개념으로 `with`를 사용한다고 이해하면 좋습니다.

아래와 같이 트레이트를 사용하여 인스턴스화 한다면 기존 클래스에 새로운 기능을 무한대로 추가할 수 있습니다.

```scala
class User(name: String) {
  def prefix = ""

  override def toString: String = s"$prefix $name"
}

trait Alan {
  self: User =>

  override def prefix: String = "Alan"
}

trait Sangwoo {
  self: User =>

  override def prefix: String = "Sangwoo"
}

// 트레이트를 이용한 인스턴스화
val alan = new User("Bae") with Alan
val sangwoo = new User("Bae") with Sangwoo

println(alan) // Alan Bae
println(sangwoo) // Sangwoo Bae
```

> 서적에서는 이 트레이트의 기능을 의존성 주입 (dependency injection) 의 역할로 사용된다고 서술되어 있지만 기존에 스프링 프레임워크에서 공부했던 의존성 주입과 사용 방법이 머릿 속에 박혀 있어 이 부분을 완벽히 이해하지 못하였습니다.

> 대략 느낌적으로는 클래스 정의 시점에 존재하지 않던 기능을 인스턴스화 시점에 트레이트를 추가함으로써 해당 트레이트의 기능이 클래스에 주입된다는 것일까 하는 느낌으로 어렴풋이 이해했습니다.

이렇게 트레이트에 대해 공부해 봤는데 처음 트레이트를 접할 때는 자바의 추상클래스나 인터페이스와 비교해 생각하려 했으나 

이 생각이 적절치 않다고 개인적으로 결론을 지었습니다.

트레이트는 다른 객체 지향 언어에서 볼 수 없는 (아직 보지 못한?) 특수한 기능 이라고 생각합니다.
