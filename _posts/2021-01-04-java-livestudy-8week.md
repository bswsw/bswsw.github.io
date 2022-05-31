---
title: "[Live Study] 8주차 과제: 인터페이스"
author: Bae Sangwoo
date: 2021-01-04 18:05:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, interface]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 인터페이스에 대해 학습하세요.


# 인터페이스를 정의하는 방법

## 인터페이스란?

자바는 클래스를 하나만 상속이 가능한 특성이 있는데 이는 객체지향 프로그래밍에서 큰 제약이기 때문에 인터페이스(Interface)라는 개념을 도입하였습니다.

인터페이스라는 이름답게 디자인(설계)적인 요소를 위한 유형이므로 메소드를 정의하지만 기본적으로 구현 코드는 제공하지 않습니다.

다만 인터페이스를 구현(implementation)하는 클래스에서 메소드의 내용을 구현해야합니다.

## 인터페이스 정의

- 인터페이스의 모든 메소드는 추상적입니다.
- 인터페이스는 공용 API를 정의하기 때문에 암시적으로 `public` 이므로 생략하는 것이 일반적입니다.
- 인스턴스 필드를 정의할 수 없습니다. (필드는 구현의 세부사항이고 인터페이스는 구현이 아니라 사양입니다.)
- 인터페이스는 인스턴스화 할 수 없으므로 생성자가 필요 없습니다.
- 인터페이스는 중첩될 수 있습니다.
- `Java 8`부터 인터페이스에 `static` 메소드를 정의할 수 있습니다. (Java의 설계 결함으로 알려져 있습니다.)
- `Java 8`부터 인터페이스에 `default` 메소드를 정의할 수 있습니다.
- `Java 9`부터 인터페이스에 `private` 메소드를 정의할 수 있습니다.

```java
public interface Animal {
    String getName();
    int getLegs();
}
```

> 메소드 정의만 있고 구현은 없습니다.


# 인터페이스를 구현하는 방법

인터페이스를 구현하는 클래스를 만드는 방법은 상속과 비슷하지만 키워드가 다릅니다.

상속과 개념적으로 다르고 인터페이스를 구현하는 의미이기 때문에 `implements` 키워드를 사용합니다.

그리고 인터페이스에 정의되어 있는 메소드를 필수적으로 구현해야 합니다.

```java
public class Cat implements Animal {

    @Override
    public String getName() {
        return "고양이";
    }

    @Override
    public int getLegs() {
        return 4;
    }

}
```

> 필수적으로 `getName()`, `getLegs()` 메소드를 구현해야 합니다.


# 인터페이스 레퍼런스를 통해 구현체를 사용하는 방법

먼저 위에서 `Animal` 인터페이스를 구현한 `Cat` 클래스처럼 다른 구현체를 만들어보겠습니다.

```java
public class Bird implements Animal {

    @Override
    public String getName() {
        return "새";
    }

    @Override
    public int getLegs() {
        return 2;
    }

}
```

이제 `Animal` 인터페이스의 구현체인 `Cat`, `Bird` 클래스가 있습니다.

이 두 클래스를 인스턴스화 하여 메소드를 호출 해보겠습니다.

```java
Cat cat = new Cat();
Bird bird = new Bird();

System.out.println(cat.getName()); // 고양이
System.out.println(cat.getLegs()); // 4

System.out.println(bird.getName()); // 새
System.out.println(bird.getLegs()); // 2
```

`cat`, `bird` 인스턴스는 `Animal` 타입으로도 생성 가능합니다.

```java
Animal cat = new Cat();
Animal bird = new Bird();
```

`Animal` 인터페이스를 파라미터로 받는 메소드가 있다면 `Cat`, `Bird` 타입의 인스턴스를 파라미터로 사용할 수 있습니다.

```java
public void printAnimal(Animal animal) {
    System.out.println("이름 : " + animal.getName());
    System.out.println("다리 개수 : " + animal.getLegs());
}

Cat cat = new Cat();
Bird bird = new Bird();

printAnimal(cat);
printAnimal(bird);
```


# 인터페이스 상속

인터페이스는 다른 인터페이스를 상속(확장)할 수 있습니다.

클래스가 다른 클래스를 상속하는 것과 동일하게 `extends` 키워드를 사용하여 인터페이스를 상속할 수 있습니다.

부모 인터페이스의 모든 메소드와 상수를 상속하고 새 메소드와 상수를 정의할 수 있습니다.

```java
public interface PrintableAnimal extends Animal {
    void print();
}

// 구현 클래스
public class Puppy implements PrintableAnimal {

    // Animal 인터페이스 메소드 구현
    @Override
    public String getName() {
        return "강아지";
    }

    // Animal 인터페이스 메소드 구현
    @Override
    public int getLegs() {
        return 4;
    }

    // PrintableAnimal 인터페이스 메소드 구현
    @Override
    public void print() {
        System.out.println("이름 : " + getName());
        System.out.println("다리개수 : " + getLegs());
    }

}
```

> `PrintableAnimal`의 구현체를 만들면 `PrintableAnimal`의 부모 인터페이스인 `Animal` 인터페이스의 메소드도 구현해야 합니다.

또한 클래스는 단 하나의 클래스만 상속이 가능하지만 인터페이스는 여러 인터페이스를 상속할 수 있습니다.

```java
public interface PrintableAnimal extends Animal, Comparable<PrintableAnimal> {
    void print();
}

// 구현체
public class Puppy implements PrintableAnimal {

    // Animal 인터페이스 메소드 구현
    @Override
    public String getName() {
        return "강아지";
    }

    // Animal 인터페이스 메소드 구현
    @Override
    public int getLegs() {
        return 4;
    }

    // PrintableAnimal 인터페이스 메소드 구현
    @Override
    public void print() {
        System.out.println("이름 : " + getName());
        System.out.println("다리개수 : " + getLegs());
    }

    // Comparable 인터페이스 메소드 구현
    @Override
    public int compareTo(PrintableAnimal o) {
        return getLegs() - o.getLegs();
    }
}
```

![](https://user-images.githubusercontent.com/12427330/103527652-06d44d80-4ec6-11eb-8ec0-ba60e265dfba.png)
_Puppy 클래스의 계층 구조_

`Puppy` 클래스는 계층 구조에 따라 `Animal`, `PrintableAnimal` 타입으로 인스턴스화가 가능합니다.

그리고 위에서 사용했던 `printAnimal` 메소드도 사용할 수 있습니다.

```java
Puppy puppy1 = new Puppy();
PrintableAnimal puppy2 = new Puppy();
Animal puppy3 = new Puppy();

printAnimal(puppy1);
printAnimal(puppy2);
printAnimal(puppy3);
```


# 인터페이스의 기본 메소드 (Default Method)

`Java 8` 부터 도입된 기능으로 구현을 포함하는 메소드를 포함한 인터페이스를 정의할 수 있습니다.

위에서 정의한 `PrintableAnimal` 인터페이스의 `print()` 메소드를 매번 구현할 필요가 없다고 느끼면 기본 메소드로 정의하여 사용할 수 있습니다.

또한 상속한 인터페이스의 메소드도 기본 메소드로 정의할 수 있습니다.

```java
// 인터페이스
public interface PrintableAnimal extends Animal, Comparable<PrintableAnimal> {
    // 자신의 메소드를 기본 메소드로 구현
    default void print() {
        System.out.println("이름 : " + getName());
        System.out.println("다리개수 : " + getLegs());
    }

    // Comparable 인터페이스의 메소드를 상속하여 기본 메소드 구현
    @Override
    default int compareTo(PrintableAnimal o) {
        return getLegs() - o.getLegs();
    }
}

// 구현체
public class Snake implements PrintableAnimal {

    @Override
    public String getName() {
        return "뱀";
    }

    @Override
    public int getLegs() {
        return 0;
    }

}

// 사용하기
Snake snake = new Snake();
snake.print(); // PrintableAnimal의 기본 메소드 사용
```

> `default` 키워드를 사용하여 기본 메소드를 구현할 수 있고 기본 메소드는 구현체에서 필수로 구현할 필요가 없습니다.

## 하위 호환성

기본 메소드는 구현체에서 필수적으로 해당 메소드를 구현할 필요가 없기 때문에 하위 호환이 가능합니다.

만약 `PrintableAnimal` 인터페이스에 새로운 기능의 추가가 필요하다면 기존에는 메소드 정의 후 각 구현체에서 구현이 필요했거나 인터페이스를 래핑하는 기본 클래스가 필요했지만 기본 메소드로 구현한다면 인터페이스의 구현체인 `Snake` 클래스에는 영향이 없습니다.

```java
public interface PrintableAnimal extends Animal, Comparable<PrintableAnimal> {

    // ...

    // 기본 메소드 추가
    default void otherDefaultMethod() {
        System.out.println("other default method");
    }

}

// 구현체는 변화 없음...
public class Snake implements PrintableAnimal, Printer {

    @Override
    public String getName() {
        return "뱀";
    }

    @Override
    public int getLegs() {
        return 0;
    }
}
```

## 기본 메소드 주의 사항

만약 아래와 같이 `Snake` 클래스에서 두 인터페이스를 상속하는데 각 인터페이스에 `print()` 기본 메소드가 정의되어 있다면 어떻게 할까요?

```java
public interface Printer {

    default void print() {
        System.out.println(getClass().getSimpleName());
    }
}

public class Snake implements PrintableAnimal, Printer {
    // 어떤 print() 메소드를 사용하게 될까...
}
```

요즘 IDE는 너무 똑똑하기 때문에 문제를 발견하는데는 어려움이 없습니다.

![](https://user-images.githubusercontent.com/12427330/103530872-8fa1b800-4ecb-11eb-9d4e-f0955f67b018.png)
_Intellij는 똑똑합니다._

`Snake` 클래스에서 `print` 메소드를 직접 구현해야하며 두 인터페이스의 메소드를 사용하는 방법도 있습니다.

```java
public class Snake implements PrintableAnimal, Printer {

    // ...

    @Override
    public void print() {
        PrintableAnimal.super.print(); // PrintableAnimal의 print() 메소드 호출
        Printer.super.print(); // Printer의 print() 메소드 호출

        // 혹은 직접 구현...
    }
}
```


# 인터페이스의 static 메소드

`Java 8`에서 도입된 기능으로 인터페이스에 `static` 메소드 추가가 가능합니다.

클래스에서와 동일하게 사용 가능하며 기본적으로 `public`으로 간주합니다.

`static` 메소드 이므로 상속이 불가능 합니다.

```java
public interface PrintableAnimal extends Animal, Comparable<PrintableAnimal> {

    static String getDescription() {
        return "출력기능이 있는 동물 인터페이스";
    }
}

System.out.println(PrintableAnimal.getDescription()); // 출력기능이 있는 동물 인터페이스v
```

# 인터페이스의 private 메소드

`Java 9`에서 도입된 기능으로 이미 `Java 8`에서 기본 메소드 사용이 가능해졌기 때문에 로직을 분리하기 위해 사용할 수 있습니다.

- `private` 메소드 이기 때문에 인터페이스에서 구현이 되어 있어야하고 추상(`abstract`) 메소드일 수 없습니다.
- 구현체에서 구현할 수 없고 자식 인터페이스에서도 상속이 불가능 합니다.
- `static` 메소드도 `private`이 가능합니다.

```java
public interface PrintableAnimal extends Animal, Comparable<PrintableAnimal> {

    static String getDescription() {
        return "출력기능이 있는 동물 인터페이스";
    }

    // 자신의 메소드를 기본 메소드로 구현
    // private 메소드 사용
    default void print() {
        printName();
        printLegs();
    }
    
    // private 메소드로 기본 메소드에서 사용할 로직 분리 1
    private void printName() {
        System.out.println("이름 : " + getName());
    }
    
    // private 메소드로 기본 메소드에서 사용할 로직 분리 2
    private void printLegs() {
        System.out.println("다리개수 : " + getLegs());
    }

    // Comparable 인터페이스의 메소드를 상속하여 기본 메소드 구현
    @Override
    default int compareTo(PrintableAnimal o) {
        return getLegs() - o.getLegs();
    }

    default void otherDefaultMethod() {
        System.out.println("other default method");
    }
}
```

> 이번 포스팅의 모든 예제를 종합한 완전체 느낌...
