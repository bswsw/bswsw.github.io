---
title: "[Learning Scala] Chapter8. 클래스"
author: Bae Sangwoo
date: 2020-09-23 10:53:00 +0900
categories: [Scala, Learning Scala]
tags: [scala, learningscala]
---


이 글은 [러닝 스칼라](https://jpub.tistory.com/677)를 기반으로 작성 되었습니다.

![](https://t1.daumcdn.net/cfile/tistory/27742D3D58F710DA32){:width="300px"}
*출처 : https://jpub.tistory.com/677*


이전 포스팅까지는 스칼라의 핵심적인 기능과 사용 방법에 대해 알아봤고 이제 스칼라로 객체지향 개발을 하도록 제공되는 기능들을 알아보려고 합니다.

먼저 가장 기본적인 클래스에 대해 알아보겠습니다.

> 다만 이번 포스팅은 기존에 자바 등 객체지향 언어에서 클래스를 사용해본 경험이 있다는 전제 하에 설명하려 합니다.
> 서적에는 스칼라의 클래스에 대한 다양한 내용이 있지만 기본적인 사용법과 자바 클래스와의 차이점 위주로 설명드리겠습니다.

스칼라의 클래스에서도 다른 객체지향 언어의 클래스에서 제공되는 상속(inheritance), 다형성(polymorphism), 

캡슐화(encapsulation)을 지원합니다.


# 기본 사용법

아래 예제는 간단하므로 쉽게 이해하실거라고 생각합니다.

```scala
// 클래스 정의
class Member {

  val name: String = "alan"

  def greet(): String = s"Hello, $name"

}

object Runner extends App {

  val member = new Member // 객체 생성
  println(member.greet()) // Hello, alan
  println(member) // Member@77f03bb1

}
```

스칼라의 클래스는 자바와 같이 `java.lang.Object`를 상속하는 클래스이며 스칼라의 루트 타입인 `Any`와 동등합니다.

그렇기 때문에 두번째 출력문인 `Member@77f03bb1` 이 데이터는 `toString()` 메소드를 사용하여 출력되었으며 

이를 오버라이딩(override) 할 수 있습니다.

```scala
class Member {

  val name: String = "alan"

  def greet(): String = s"Hello, $name"

  override def toString = s"Member($name)"

}

object Runner extends App {

  val member = new Member
  println(member.greet()) // Hello, alan
  println(member) // Member(alan)
}
```


# 생성자

생성자는 자바보다는 조금 간단하며 코틀린과 비슷합니다.

클래스에 매개변수로 기본 생성자를 지정할 수 있고 추가적인 생성자를 만드는 방법도 존재합니다.

그리고 메소드 매개변수와 마찬가지로 기본값을 정의할 수 있습니다.

```scala
class Member(val firstName: String, val lastName: String, val gender: String = "M") { // 기본 생성자

  // 추가 생성자
  def this(firstName: String, lastName: String) = {
    this(firstName, lastName, "M")
  }

  override def toString = s"Member($firstName, $lastName, $gender)"

}

val m1 = new Member("alan", "bae") // 기본생성자 사용
val m2 = new Member("sangwoo", "bae") // 추가 생성자 사용

println(m1) // Member(alan, bae, M)
println(m2) // Member(sangwoo, bae, M)
```


# 상속

상속도 자바와 동일한 문법으로 가능하고 당연히 자식 크래스의 객체에서 부모 클래스의 메소드 호출이 가능합니다.

또한 `isInstanceOf` 메소드를 사용하여 객체의 타입을 체크할 수 있습니다.

```scala
class Member(val memberType: String,
             val firstName: String,
             val lastName: String) {

  def print(): Unit = {
    println(this)
  }

  override def toString = s"Member($memberType, $firstName, $lastName)"

}

class OldMember(firstName: String,
                lastName: String) extends Member("OLD", firstName, lastName)

val oldMember = new OldMember("alan", "bae")

// 부모 클래스의 메소드 실행
oldMember.print() // Member(OLD, alan, bae)

println(oldMember.isInstanceOf[Member]) // true
println(oldMember.isInstanceOf[OldMember]) // true
```


# 제네릭

제네릭한 타입을 매개변수도 정의할 수 있습니다.

```scala
class MyData[T](element: T) {

  def printType(): Unit = {
    println(element.getClass.getTypeName)
  }

}

val myData1 = new MyData[String]("String")
myData1.printType() // java.lang.String

// 타입 매개변수 생략 가능
val myData2 = new MyData(1)
myData2.printType() // java.lang.Integer
```

제네릭 타입의 경계 (bounds)를 지정할 수도 있습니다.

저 복잡하게 사용할 수도 있지만 간단하게는 아래 예제로 구분할 수 있습니다.

```scala
class A
class B extends A

// <: 와 >: 사용의 차이를 아래 인스턴스 새성 구문에서 확인해보세요!
class C[T <: A]
class D[T >: A]
class E[T <: B]
class F[T >: B]

// 주석은 컴파일 결과 입니다.
new C[A] // ok
new C[B] // ok

new D[A] // ok
new D[B] // type arguments [Runner.B] do not conform to class D's type parameter bounds [T >: Runner.A]

new E[A] // type arguments [Runner.A] do not conform to class E's type parameter bounds [T <: Runner.B]
new E[B] // ok

new F[A] // ok
new F[B] // ok
```


# 추상 클래스

스스로는 인스턴스화를 할 수 없지만 다른 클래스에 의해 확장하여 사용할 수 있는 추상클래스도 정의할 수 있습니다.

필드와 메소드 모두 구현되지 않은 상태로 정의할 수 있습니다.

```scala
abstract class Profile {

  var name: String
  var email: String

  def makePrintData(): String

  def print(): Unit = {
    println(makePrintData())
  }

}

class MyProfile extends Profile {

  override var name: String = "alan"
  override var email: String = "alan@gmail.com"

  override def makePrintData(): String = {
    s"$name : $email"
  }
}

val myProfile = new MyProfile
myProfile.print() // alan : alan@gmail.com
```


# 익명 클래스

앞서 추상 클래스를 재사용이 가능하도록 상속한 자식 클래스를 정의하는 방법을 알아봤습니다.

이번엔 일회성이며 이름이 없는 익명 클래스를 알아보겠습니다.

부모 클래스를 확장하는 객체를 가지지만 일회성으로 값이나 변수에 할당할 수 있습니다.

혹은 해당 추상 클래스의 구현해야할 메소드가 한개이며 해당 타입을 파라미터로 받는 메소드가 있다면 람다식으로도 정의할 수 있습니다.

```scala
// 추상클래스
abstract class TalkListener {

  def receive(): Unit

}

class Talk {
  private val listeners = collection.mutable.Buffer.empty[TalkListener]

  // 추상클래스를 받는 메소드
  def addListener(listener: TalkListener): Unit = {
    listeners += listener
  }

  def listen(): Unit = {
    listeners.foreach(_.receive())
  }
}

val talk = new Talk

// 익명 클래스를 값에 할당
val listener = new TalkListener {
  override def receive(): Unit = {
    println("first listener")
  }
}

talk.addListener(listener)

// 구현해야할 메소드가 한개라면 람다식으로 정의할 수 있습니다.
talk.addListener(() => {
  println("second listener")
})

talk.listen()
// 출력:
// first listener
// second listener
```


# 메소드 오버로딩

자바와 마찬가지로 메소드 오버로딩(overload) 이 가능합니다.

```scala
class A {

  def print(s: String): Unit = println(s"This is String : $s")

  def print(i: Int): Unit = println(s"This is Integer : $i")

}

val a = new A

a.print("alan") // This is String : alan
a.print(100) // This is Integer : 100
```


# Apply 메소드

스칼라 클래스의 특징인 `apply` 메소드는 메소드 이름 없이 호출 될 수 있는 메소드 입니다.

이미 사용해보았는데 `List`를 선언할 때 `apply` 메소드가 사용됩니다.

```scala
// 이 두가지는 동일합니다.
val list = List(1, 2, 3)
val list2 = List.apply(1, 2, 3)
```

간단하게 아래처럼 구현해볼 수 있습니다.

```scala
class Multiplier(num: Int) {
  def apply(num2: Int): Int = num * num2
}

val number = new Multiplier(2)
number(10) // 20, 내부적으로 apply 메소드 호출
```


# 지연값 (Lazy Value)

`lazy val` 키워드를 사용하여 지연된 값을 사용할 수 있습니다.

값을 정의할 때 블록으로 표현식(expression) 을 사용할 수 있고 이는 해당 값이 최초 호출 될 때 한 번 실행되어 

결과를 값에 할당하고 이후에 값을 호출하더라도 이미 계산된 값을 반환합니다.

```scala
class MyData {

  val firstNum = 10
  val secondNum = 2

  // 지연값 정의
  lazy val resultNum: Int = {
    val result = firstNum * secondNum
    println(s"resultNum : $result")

    result
  }

}

// 객체 생성
val myData = new MyData

println(myData.resultNum)
// resultNum : 20
// 20

println(myData.resultNum)
// 20
```


# 프라이버시 접근 변경자

스칼라에도 자바와 같은 언어와 같이 값, 변수, 메소드 등의 프라이버시 제어를 할 수 있습니다.

- `public` : 붙이지 않았을 때 와 동일. 공개되어 있어 어디서든 사용 가능하며 오버라이딩 가능.
- `protected` : 동일 클래스나 서브 클래스에서 사용 가능하며 오버라이딩 불가.
- `private` : 동일 클래스에서만 사용 가능.

추가적으로 *프라이버시 접근 변경자(access modifier)* 라는 것을 사용하여 제어의 수준을 추가할 수 있습니다.

예를 들어 어떤 키워드(값, 변수, 메소드, 클래스 등) 에 `private`을 지정할 때 패키지명을 추가하여 

해당 키워드의 접근 가능한 패키지 레벨을 지정할 수 있습니다.

```scala
package dev.blog // 패키지 지정

class Blog {
  private val title = "blog title" // Blog 클래스에서만 접근 가능
  private[dev] val author = "blog author" // dev 패키지에서도 접근 가능

  // Blog 클래스에서만 접근 가능
  private def getTitle(): String = {
    title
  }

  // dev 패키지에서도 접근 가능
  private[dev] def getAuthor(): String = {
    author
  }
}
```

> `title`, `getTitle()`은 `private` 이기 때문에 다른 클래스에서 사용할 수 없습니다.
> `author`, `getAuthor()`는 `dev` 패키지 까지 사용 가능 합니다.

위 `Blog` 클래스를 아래와 같이 사용할 수 있습니다.

```scala
package dev

import dev.blog.Blog

object DevApp extends App {

  val blog = new Blog

  blog.title // value title in class Blog cannot be accessed as a member of dev.blog.Blog from object DevApp in package dev
  blog.author // 사용 가능

  blog.getTitle() // method getTitle in class Blog cannot be accessed as a member of dev.blog.Blog from object DevApp in package dev
  blog.getAuthor() // 사용 가능

}
```

이상으로 스칼라의 클래스에 대해 공부 하였습니다.

책에는 더 많은 내용이 있지만 개인적으로 자바를 경험해봤기 때문에 자바와의 차이점 위주로 작성하였습니다.

다음 포스팅에서는 클래스를 통해 생성하는 객체와 스칼라만의 구성 요소인 케이스 클래스, 트레이트에 대해 알아보겠습니다.
