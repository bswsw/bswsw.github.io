---
title: "[Live Study] 6주차 과제: 상속"
author: Bae Sangwoo
date: 2020-12-26 10:22:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, inheritance]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 상속에 대해 학습하세요.


# 자바 상속의 특징

- 자식 클래스는 부모 클래스의 내용을 상속할 수 있습니다.
- 서브 클래스에서 별도의 내용을 가질 수 있습니다.
- 자식 클래스는 부모 클래스를 알 수 있지만 부모 클래스는 자식 클래스를 알 수 없습니다.
- 자바의 상속은 다중 상속을 지원하지 않습니다.
- 자바의 모든 클래스는 기본적으로 `java.lang.Object` 클래스를 상속합니다.

## 자바의 상속 사용법

작성하는 클래스에서 다른 클래스를 상속하기 위해서는 `extends` 키워드를 사용할 수 있습니다.

```java
/**
 * 부모 클래스
 */
public class Person {
    // code..
}

/**
 * 자식 클래스 (Person 클래스 상속)
 */
public class Sangwoo extends Person {
    // code..
}

/**
 * 자식 클래스 (Person 클래스 상속)
 */
public class Alan extends Person {
    // code..
}
```

자식 클래스로 객체를 생성할 때 부모 클래스 타입으로 할당할 수 있습니다.

```java
Sangwoo sangwoo = new Sangwoo();
Person person = new Sangwoo(); // 자식 클래스 객체를 부모 클래스 타입으로 할당
```


# super 키워드

먼저 자식 키워드에서 부모 클래스의 필드나 메소드를 참조하는 방법을 알아보겠습니다.

```java
/**
 * 부모 클래스
 */
public class Person {

    public String name = "person";

    public String getKorean() {
        return "사람";
    }

}

/**
 * 자식 클래스 (Person 클래스 상속)
 */
public class Sangwoo extends Person {

    public void print() {
        System.out.println(name);  // 자식 클래스에서 부모 클래스의 필드 참조 
        System.out.println(getKorean()); // 자식 클래스에서 부모 클래스의 메소드 참조
    }

}
```

위 예제 처럼 자식 클래스에서 부모 클래스에 선언되어 있는 필드나 메소드에 접근할 수 있습니다.

그런데 만약 부모 클래스에 선언되어 있는 필드나 메소드가 자식 클래스에 동일하게 있다면 어떻게 해야할까요?

```java
/**
 * 자식 클래스 (Person 클래스 상속)
 */
public class Alan extends Person {

    public String name = "alan";

    public String getKorean() {
        return "앨런";
    }

    public void print() {
        System.out.println(name);
        System.out.println(getKorean());
        System.out.println(super.name); // 부모 클래스의 name 필드 접근
        System.out.println(super.getKorean()); // 부모 클래스의 getKorean() 메소드 접근
    }
}
```

위 예제 처럼 `Alan` 클래스에 선언된 필드나 메소드가 부모 클래스의 것과 이름이 동일하다면 

`super` 키워드를 사용하여 부모 클래스의 필드나 메소드로 호출이 가능합니다.

```java
/**
 * 부모 클래스
 */
public class Person {

    public String name;
    
    public int age;

    public Person() {
        this.name = "person";
        this.age = 10;
    }

    public Person(String name) {
        this.name = name;
        this.age = 10;
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

}
```

또한 위와 같은 `Person` 클래스를 상속한다면 자식 클래스에서 `Person` 클래스의 생성자를 호출할 수 있습니다.

```java
/**
 * 자식 클래스 (Person 클래스 상속)
 */
public class Sangwoo extends Person {

    public Sangwoo(String name, int age) {
        super(name, age); // super(), super(name) 도 가능.. 필요에 따라 잘 사용하기..
    }

    public void printName() {
        System.out.println("name : " + name);
    }

}
```


# 메소드 오버라이딩 (Method Overriding)

자식 클래스가 부모 클래스의 메소드와 동일한 이름, 파라미터, 리턴 타입을 사용하여 메소드를 재정의할 수 있습니다.

`super` 예제에서 `Alan` 클래스에 작성된 `getKorean()`가 `Person` 클래스의 메소드를 오버라이딩한 것 입니다.

```java
public class A {

    public int m1(int x, int y) {
        return x + y;
    }

    public int m2(int x, int y) {
        return x * y;
    }
}

public class B extends A {

    // A 클래스의 a 메소드를 오버라이딩(재정의)
    @Override
    public int m1(int x, int y) {
        return x - y;
    }
}
```

`A` 클래스에 덧셈 로직으로 작성된 `m1` 메소드를 `B` 클래스에서 빼기 로직으로 오버라이딩 했습니다.

`A` 클래스와 `B` 클래스 각각 객체를 생성하고 `m1`, `m2` 메소드를 실행 시킨다면 아래와 같습니다.

```java
A a = new A();
B b = new B();

a.m1(10, 2); // 12
b.m1(10, 2); // 8

a.m2(3, 2); // 6
b.m2(3, 2); // 6
```

`m1` 메소드는 `B` 클래스에서 오버라이딩 되었기 때문에 결과가 다르고 

`m2` 메소드는 `B` 클래스에서 오버라이딩 되지 않았기 때문에 `b` 객체에서도 `A` 클래스의 메소드를 호출합니다.


# 메소드 디스패치 (Method Dispatch)

메소드 디스패치란 어떤 메소드를 호출할지 결정하여 실제로 호출 시키는 과정을 말합니다.

메소드 디스패치의 종류는 아래와 같습니다.

- 스태틱 메소드 디스패치
- 다이나믹 메소드 디스패치
- 더블 디스패치

이 포스팅에서는 **스태틱 메소드 디스패치**와 **다이나믹 메소드 디스패치**만 알아보겠습니다.

사용할 예시 클래스는 아래와 같습니다.

```java
public class Parent {

    public void print() {
        System.out.println("This is parent.");
    }

}

public class Child extends Parent {

    @Override
    public void print() {
        System.out.println("This is child.");
    }

}
```

## 스태틱 메소드 디스패치 (Static Method Dispatch)

스태틱이라는 말 답게 컴파일 시점에 어떤 메소드가 호출될지 명확하게 알고 있는 경우를 말합니다.

컴파일 시점에 알 수 있기 때문에 바이트 코드에도 정보가 남겨집니다.

```java
Parent p = new Parent();
Child c = new Child();

p.print(); // This is parent.
c.print(); // This is child.
```

## 다이나믹 메소드 디스패치 (Dynamic Method Dispatch)

다이나믹 메소드 디스패치는 런타임 시점에 메소드 호출을 결정하는 경우를 말합니다.

자바가 런타임 다형성을 구현하는 방법이며 재정의된 메소드가 호출된다면 참조하는 객체 타입에 따라 실제 호출될 메소드를 결정 합니다.

다른 말로는 런타임 다형성(Runtime Polymorphism)이라고도 합니다.

자식 클래스를 부모 클래스로 업캐스팅(upcasting) 발생합니다.

```java
Parent p1 = new Child(); // 업캐스팅
p1.print();
```

결과는 당연히 `This is child.` 겠지만 이 경우에 `p1`이 어떤 객체인지 컴파일 시점에 알기 어렵습니다.

`왜?` 라고 생각하시나요? 조금 다른 예제를 보겠습니다.

```java
public void printParent(Parent p) {
    p.print();
}

Parent p1 = new Child();
Parent p2 = new Parent();

printParant(p1); // This is child.
printParent(p2); // This is parent.
```

위 결과도 당연히 이해는 되실거라고 생각합니다. 그런데 왜 런타임 시점에 호출될 메소드가 결정된다고 할까요?

`printParent` 메소드 입장에서 보면 편합니다.

`printParent` 메소드 입장에서 파라미터로 받는 `p`가 어떤 객체인지 컴파일 시점에 알 수 없기 때문입니다.


# 추상 클래스

추상 클래스는 메소드 정의만 있고 구현이 되어 있지 않은 메소드가 한 개 이상 존재하는 클래스를 추상 클래스라고 합니다.

즉 구체적이지 않고 추상적인 클래스를 의미 하며 추상 클래스 안에 구현되어 있지 않은 메소드를 추상 클래스라고 합니다.

클래스 앞에 `abstract` 키워드를 붙임으로써 추상 클래스를 생성할 수 있습니다.

추상 메소드도 앞에 `abstract` 키워드를 붙여서 정의할 수 있습니다.

```java
abstract class AbstractService {
    
    abstract String getName();

    // 물론 이런 일반적인 메소드도 넣을 수 있습니다.
    public int plus(int x, int y) {
        return x + y;
    }

}
```

추상 클래스는 다음과 같은 특징을 가지고 있습니다.

- 추상 메소드를 가지고 있는 클래스는 자동으로 자체적으로 추상화되며 그렇게 선언되어야 합니다. 그렇지 않으면 컴파일 에러가 납니다. (추상 메소드를 가진다면 추상 클래스이어야 한다!)

```java
class AbstractService {

    public int plus(int x, int y) {
        return x + y;
    }

    abstract String getName(); // 추상 메소드를 가지고 있으나 추상 클래스가 아니라 컴파일 에러 발생!!!

}
```

![](/assets/img/posts/2020-12-26-java-livestudy-6week/abstract-compile-error.png)

- 추상 클래스는 인스턴스화 할 수 없습니다. (객체 생성 불가)

```java
// 컴파일 에러
AbstractService service = new AbstractService();
```

![](/assets/img/posts/2020-12-26-java-livestudy-6week/abstract-instantiated-error.png)

- 추상 클래스를 상속한 클래스는 모든 추상 메소드를 재정의(오버라이딩) 해야 합니다. 이러한 클래스들을 구체적인 하위 클래스(concrete subclass)라고도 합니다.

- 만약 추상 메소드를 재정의하지 않고 추상 클래스를 상속한다면 그 자체도 추상 클래스가 되어야 합니다.

- `static`, `private`, `final` 메소드는 하위 클래스에 의해 오버라이딩 될 수 없기 때문에 추상 메소드가 될 수 없습니다.

- 추상 메소드가 없더라도 추상 클래스를 정의할 수 있습니다.


# final 키워드

`final` 키워드는 클래스, 변수, 메소드에서 사용할 수 있는 키워드로 더 이상 변할 수 없는 최종 상태 입니다.

## final 클래스

final 클래스는 상속할 수 없습니다. 

```java
final class FinalClass {}

class SubClass extends FinalClass {} // 컴파일 에러
```

## final 변수

final 변수의 값을 재할당할 수 없습니다.

```java
final String field = "final field";
field = "reassign data"; // 컴파일 에러
```

## final 메소드

final 메소드는 오버라이딩할 수 없습니다.

더불어 `private` 키워드와 중첩해서 사용하는건 권장하지 않습니다. `private` 어차피 메소드도 오버라이딩이 불가능 하기 때문입니다.

```java
class FinalSample {

    final String method() {
        return "final method";
    }

}

public class FinalSampleImpl extends FinalSample {

    String method() { // 컴파일 에러
        return "overriding method";
    }

}
```

# Object 클래스

![](https://docs.oracle.com/javase/tutorial/figures/java/classes-object.gif)
_출처 : https://docs.oracle.com/javase/tutorial/java/IandI/subclasses.html_

`java.lang.Object` 클래스이며 모든 클래스의 부모 클래스 입니다.

별도의 필드는 정의되어 있지 않고 객체에서 기본적으로 사용되는 메소드들로 구성되어 있습니다.

상황에 따라 `equals`, `hashCode`, `toString` 메소드 들을 주로 재정의 하여 사용합니다.

이 외에 [공식 문서](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/lang/Object.html){:target="_blank"}를 살펴보면 많은 메소드들을 볼 수 있습니다.
