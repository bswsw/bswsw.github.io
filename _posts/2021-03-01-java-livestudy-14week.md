---
title: "[Live Study] 14주차 과제: 제네릭"
author: Bae Sangwoo
date: 2021-03-01 13:43:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, generic]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 제네릭에 대해 학습하세요.


# 제네릭 사용법

제네릭(Generic)은 자바에서 유용한 기능 중 하나로 특히 공통 데이터 구조를 만들 때 주로 사용 됩니다.

```java
List<String> hobbies = new ArrayList<String>();

hobbies.add("baseball");
hobbies.add(1);

String hobby1 = (String) hobbies.get(0);
String hobby2 = (String) hobbies.get(1); // ClassCastException 발생
```

`hobbies` 리스트의 문제는 아래와 같습니다.

1. 아무 타입의 데이터를 `add` 할 수 있음.
2. 어떤 데이터 타입을 포함 하는지 알 수 없음.
3. 데이터를 사용하려면 타입 캐스팅이 필요함.
4. 잘못된 데이터를 넣을 때 컴파일 타임에서 예측하기 어렵고 잘못된 타입 캐스팅으로 런타임 예외인 `ClassCastException`이 발생할 수 있음.

`hobbies` 리스트에서 처리할 수 있는 데이터를 제한함으로써 위와 같은 문제를 해결할 수 있습니다.

```java
List<String> hobbies = new ArrayList<>();

hobbies.add("baseball");
hobbies.add(1); // 컴파일 에러 발생
```


# 제네릭의 주요 개념

## 제네릭 타입, 타입 파라미터

`List`를 사용할 때 타입을 제한해서 처리가 가능한 이유는 이미 `List` 인터페이스에 제네릭 타입으로 타입 파라미터를 받고 있기 때문입니다.

```java
// 자바에서 제공되는 List 인터페이스
public interface List<E> extends Collection<E> {
    // ...
}
```

- `E` : 제네릭 타입
- `<E>` : 타입 파라미터

## 다이아몬드 연산자

제네릭 타입의 인스턴스를 생성할 때 타입 파라미터를 생략할 수 있습니다.

컴파일러가 타입 파라미터를 추론할 수 있기 때문인데 이를 다이아몬드 연산자, 다이아몬드 구문이라고 합니다.

```java
List<String> hobbies1 = new ArrayList<String>();
List<String> hobbies2 = new ArrayList<>(); // String 생략
```

## 바운디드 타입(Bounded Type)

```java
public class Wrapper<T> {

    private final T value;

    public Wrapper(T value) {
        this.value = value;
    }
}

Wrapper<String> wrapper1 = new Wrapper<>();
Wrapper<Integer> wrapper2 = new Wrapper<>();
```

이 `Wrapper` 클래스에서 타입 파라미터의 타입을 제한을 하고싶을 때 바운디드 타입을 사용할 수 있는데 `extends` 키워드를 사용하여 적용할 수 있습니다.

```java
public class Wrapper<T extends Number> {
    private T value;

    public byte getByteValue() {
        return value.byteValue();
    }
}

Wrapper<String> wrapper1 = new Wrapper<>(); // 컴파일 에러 발생
Wrapper<Integer> wrapper2 = new Wrapper<>();
```

이렇게 `<T extends Number>`를 사용하여 `Number`와 호환되는 타입만 받도록 제한할 수 있습니다.

또한 타입이 제한되어 있어서 `value.byteValue()` 처럼 해당 타입의 메소드를 사용할 수도 있습니다.

## Wildcard

만약 타입 파라미터에 구체적인 값을 제공하지 않고 모든 타입을 의미하는 값을 넣고 싶을 때 와일드카드를 사용할 수 있고 와일드카드는 물음표(`?`)로 나타냅니다.

```java
List<?> list = Arrays.asList("alan", 1, 100L, new User("bsw", "bsw@ss.com"));

List<?> hobbies = new ArrayList<String>();
```

> 리스트의 데이터 타입에 대해 모르지만 코드에 문제는 없습니다.

## Bounded Wildcard

와일드카드를 사용하는 예제에서 왜 `Object`를 사용하지 않고 와일드카드를 사용할까요?

바운디드 와일드카드를 사용하여 알려지지 않은 타입 파라미터의 상속 계층을 표현하는 데 사용합니다.

### Upper Bounded Wildcard (Type covariance)

공변적이라고도 표현하며 `extends` 키워드를 사용하여 특정 클래스의 동일 클래스나 자식 클래스만 사용할 수 있음을 나타냅니다.

```java
List<? extends String> list = new ArrayList<>();
String s = list.get(0);
list.add(""); // 컴파일 에러
```

### Loer Bounded Wildcard (Type contravariance)

반공변적이라고 표현하며 `super` 키워드를 사용하고 특정 클래스의 부모 클래스만 가진다는 의미를 나타냅니다.

```java
List<? super String> list = new ArrayList<>();
String s = list.get(0); // 컴파일 에러
list.add("");
```

# 제네릭 메소드 만들기

제네릭 메소드는 어떤 레퍼런스 타입의 인스턴스를 사용할 수 있는 메소드 입니다.

클래스나 인터페이스에 제네릭 타입 파라미터가 없더라도 사용할 수 있습니다.

```java
// 제네릭 메소드
public <T> String getClassName(T a) {
    return a.getClass().getSimpleName();
}

List<?> list = List.of("This is String", 1, 2.0, (byte) 3, 4f, 5L, new Child());

list.forEach(el -> {
    String className = getClassName(el);
    System.out.println(className);
});
```

```
String
Integer
Double
Byte
Float
Long
Child
```

제네릭 메소드에도 동일하게 바운디드 타입을 사용할 수 있습니다.

```java
public <T extends Number> String getClassName(T a) {
    return a.getClass().getSimpleName();
}

getClassName(1);
getClassName(2.0);
getClassName((byte) 3);
getClassName(4f);
getClassName(5L);
getClassName("This is String"); // 컴파일 에러
getClassName(new Child()); // 컴파일 에러
```

또한 클래스에 정의된 제네릭 타입 파라미터를 메소드에서 파라미터로 사용할 수 있습니다.

```java
public class Parent<T> {
    public String getClassName(T p) {
        return p.getClass().getSimpleName();
    }
}

Parent<String> p1 = new Parent<>();
p1.getClassName("only string"); // String만 입력 가능
p1.getClassName(1); // 컴파일 에러

Parent<Integer> p2 = new Parent<>();
p2.getClassName(1); // Integer만 입력 가능
p2.getClassName("string no"); // 컴파일 에러
```


# Erasure

제네릭은 자바5에서 추가된 기능 입니다.

그럼 이전 버전의 제네릭이 없는 컬렉션과 제네릭이 추가된 버전의 컬렉션이 함께 사용될 때 호환성 문제가 발생할 수 있습니다.

그래서 자바는 **타입 소거(Type Erasure)**를 통해 호환성을 해결 하였습니다.

제네릭 타입 파라미터는 컴파일이 되면 없어지고 바이트코드에 반영되지 않습니다.

```java
public interface TypeErasure {
    int getSize(List<String> list);
    int getSize(List<Integer> list);
}
```

이런 인터페이스가 있을 때 두 개의 `getSize` 메소드를 오버로딩했다고 생각할 수 있습니다.

그러나 `getSize` 메소드는 오버로딩 되지 않고 컴파일 에러가 발생합니다.

이유는 타입 소거 때문입니다.

제네릭 타입은 컴파일 후 바이트 코드를 보면 제네릭 타입이 생략되어 있는 것을 볼 수 있습니다.

```java
public interface TypeErasure {
    int getSize(List<String> list);
}
```

```java
// class version 58.0 (58)
// access flags 0x601
public abstract interface dev/baesangwoo/livestudy/week14/TypeErasure {

  // compiled from: TypeErasure.java

  // access flags 0x401
  // signature (Ljava/util/List<Ljava/lang/Integer;>;)I
  // declaration: int getSize(java.util.List<java.lang.Integer>)
  public abstract getSize(Ljava/util/List;)I
}
```

그리고 제네릭 타입으로는 Primitive Type이 아니라 Reference Type만 사용할 수 있는데 그 이유 또한 타입 소거 때문 입니다.

컴파일 후에 타입 소거가 발생하기 때문에 내부적으로 타입 파라미터를 `Object` 타입으로 처리하게 되는데 Primitive Type은 `Object` 클래스를 상속받지 않기 때문에 Reference Type을 사용해야 합니다.
