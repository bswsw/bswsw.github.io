---
title: "[Leaning Scala] Chapter9. Object, 케이스 클래스"
author: Bae Sangwoo
date: 2020-10-11 16:17:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


# Object

서적에는 *객체*로 설명되어 있으나 클래스를 통햇 생성되는 *객체(instance)*와 혼동될 여지가 있어 영어인 `Object`로 명시 하겠습니다.

`Object`는 하나 이상의 인스턴스를 가질 수 없는 형태의 클래스이며 객체지향적으로 싱글턴(singleton)이라고 합니다.

자바 등 다른 객체지향 언어에서는 정적(static)이거나 전역(global)로 선언할 수 있으며 코틀린의 object와는 비슷합니다.

Object는 따로 인스턴스화를 하지 않더라도 필드나 메소드에 접근이 가능한 구조입니다.

정확하게는 최초로 접근할 때 자동으로 인스턴스화 합니다.

```scala
object Hello {
  println("Hello Object")

  def hi() = "Hi Object"
}

println(Hello.hi())
// Hello Object
// Hi Object

println(Hello.hi())
// Hi Object
```

위와 같이 `object` 키워드를 사용하여 정의하고 별도로 인스턴스를 생성하지 않고 바로 메소드를 사용할 수 있습니다.

더불어 최초 사용 시 자동으로 전역 인스턴스화가 되기 대문에 두번째 `hi()` 메소드 실행 시에는 

이미 생성된 전역 인스턴스를 사용함으로써 `Hello Object`가 출력되지 않는 것을 볼 수 있습니다.

Object는 순수 함수에 가장 잘 어울리며 저자가 좋아하는 Object의 용도입니다.

순수 함수는 주어진 매개 변수로만 계산하여 결과값을 반환하는 함수이며 참조에 투명한 함수 입니다. 

그 외의 특징은 예전 스칼라의 함수에 대해 공부할 때 작성했던 [포스팅](https://sangwoobae.github.io/posts/scala-learning-scala-chapter4/){:target="_blank"}에서 확인해보실 수 있습니다.

```scala
object Calculator {
  def plus(a: Int, b: Int): Int = {
    a + b
  }

  def minus(a: Int, b: Int): Int = {
    a - b
  }
}

println(Calculator.plus(10, 20)) // 30
println(Calculator.minus(20, 10)) // 10
```

이렇게 클래스와 상관없는 유틸리티성의 순수 함수들을 Object에 정의해놓고 사용한다면 

클래스의 비즈니스 로직과 분리할 수 있고 어느 곳에서도 재사용이 가능한 메소드가 됩니다.

## 동반 객체

동반 객체 (Companion Object)는 어떠한 클래스와 동일한 이름을 가진 Object를 말합니다.

자바에는 존재하지 않지만 코틀린에서는 `companion object` 키워드를 사용하여 정의할 수 있습니다.

동일한 이름을 가져 서로 동반 관계에 있는 클래스와 Object는 서로간의 `private`, `protected` 필드와 메소드에 접근할 수 있습니다.

```scala
class User {
  def print() = s"name : ${User.name}, email : ${User.email}"
}

object User {
  private val name = "alan"
  private val email = "alan@gmail.com"
}

val user = new User
println(user.print()) // name : alan, email : alan@gmail.com
```

객체지향 프로그래밍에서 많이 사용되는 팩토리(factory) 패턴을 구현하는데도 동반 객체를 사용할 수 있습니다.

이 전에 공부했던 클래스의 `apply` 메소드를 기억하시나요?

`apply` 메소드를 동반 객체에 적용함으로써 팩토리 패턴을 구현할 수 있습니다.

```scala
class User(name: String, email: String) {

  def printUser(): Unit = {
    println(s"name : $name, email : $email")
  }
}

object User {
  def apply(name: String, email: String): User = new User(name, email)
}

val user = User("alan", "alan@gmail.com")
println(user.printUser()) // name : alan, email : alan@gmail.com
```

## 객체를 가지는 명령줄 어플리케이션

자바 등 언어와 마찬가지로 스칼라도 명령줄 어플리케이션을 생성할 수 있습니다.

자바와 다른 점은 클래스가 아니라 object에 생성하지만 같은 점은 `main` 메소드를 사용한다는 것입니다.

사실 자바에서는 클래스에 main 메소드를 static으로 생성하기 때문에 거의 동일하다고 할 수 있습니다.

```scala
object MyApp {
  def main(args: Array[String]): Unit = {
    println("MyApp Start")
  }
}
```


# 케이스 클래스 (case class)

케이스 클래스는 자동으로 생성된 몇 가지 메소드를 포함한 클래스이며 

앞서 공부한 동반 객체도 생성되며 그 동반 객체도 자동으로 생성된 메소드가 있습니다.

케이스 클래스에 정의된 필드들에 기반으로 메소드를 생성합니다.

```scala
// 케이스 클래스 정의
case class Article(title: String,
                   content: String,
                   createdAt: LocalDateTime)

// 동반객체의 apply 메소드를 자동 생성 (Article.apply())
val article = Article(title = "제목", content = "내용", createdAt = LocalDateTime.now())
// 클래스의 toString 자동 생성
println(article.toString) // Article(제목,내용,2020-10-16T18:05:42.191)

// 사본을 반환하는 copy 메소드 자동 생성
val copiedArticle = article.copy()

// 모든 필드를 비교하는 equals 자동 생성
println(article == copiedArticle) // true
// 모든 필드들의 해시코드 반환
println(article.hashCode()) // 480956799 (랜덤)

val copiedArticle2 = article.copy(content = "내용2")
println(article == copiedArticle2) // false
```

위 예제를 통해 케이스 클래스를 정의하면 자동으로 생성된 메소드들을 확인할 수 있습니다.

코틀린의 `data class`와 비슷한 기능이라고 볼 수 있습니다.
