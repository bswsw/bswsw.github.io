---
title: "[Live Study] 11주차 과제: Enum"
author: Bae Sangwoo
date: 2021-01-29 21:12:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, enum]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 열거형에 대해 학습하세요.


# Enum 이란?

Enum은 제한된 기능을 까지고 허용하는 값이 적은 클래스의 변형된 유형입니다.

흔히 열거형 클래스라고도 하며 어떤 데이터 집합에서 유일한 값을 표현할 때 사용할 수 있습니다.

예를 들어 색상을 표현하는 집합에서 어떤 색상인지 나타내면서 그 값이 집합에서 유일하게 표현된다면 사용할 수 있습니다.

Enum은 아래와 같이 몇가지 특성이 있습니다.

- 모든 Enum은 암묵적으로 `java.lang.Enum`을 상속 합니다.
- 인터페이스를 구현할 수 있습니다. (상속은 불가능)
- Enum을 상속할 수 없습니다.
- `new` 키워드를 통해 인스턴스화 될 수 없습니다.


# Enum 정의 하기

`enum` 키워드를 사용하여 Enum을 정의할 수 있습니다.

```java
public enum Color {
    RED, GREEN, BLUE
}
```

이렇게 Enum 클래스를 정의하고 각 `RED`, `GREEN`, `BLUE`는 정적(static) 필드인 것처럼 참조할 수 있으며 열거 상수(enum constant)라고 합니다.

```java
Color r = Color.RED;
Color g = Color.GREEN;
Color b = Color.BLUE;
```

열거형 클래스인 Enum을 정의하면 암묵적으로 `java.lang.Enum`을 상속하므로 이 부모 클래스의 메소드들을 사용이 가능하게 됩니다.

`java.lang.Enum` 클래스의 `name`, `ordinal` 필드가 있습니다.

`name` 필드에는 정의한 열거 상수명이 `String`으로 들어가 있고 

`ordinal`에는 열거 상수의 정의 순서가 `int`로 있습니다.

이 필드들은 `private` 하기 때문에 직접 접근을 하지 못하지만 메소드를 제공합니다.

```java
Color r = Color.RED;
Color g = Color.GREEN;
Color b = Color.BLUE;

System.out.println(r.name()); // RED
System.out.println(g.name()); // GREEN
System.out.println(b.name()); // BLUE

System.out.println(r.ordinal()); // 0
System.out.println(g.ordinal()); // 1
System.out.println(b.ordinal()); // 2
```

`ordinal` 필드는 `EnumSet`, `EnumMap`과 같은 enum 기반 데이터 구조에서 사용하기 위해 설계 되어 있습니다.

## Enum의 필드와 메소드

Enum은 키워드가 다르긴 하지만 클래스이기 때문에 생성자, 필드, 메소드를 가질 수 있습니다.

만약 필드와 메소드가 존재한다면 Enum 상수 목록 마지막에 세미콜론(`;`)을 입력한 후 아래에 정의할 수 있습니다.

```java
public enum Color {
    // 생성자 파라미터 대로 적용
    RED("빨강"), GREEN("초록"), BLUE("파랑");

    // 필드
    private String desc;

    // 메소드
    public String getDesc() {
        return desc;
    }

    // 생성자
    Color(String desc) {
        this.desc = desc;
    }
}

Color r = Color.RED;
Color g = Color.GREEN;
Color b = Color.BLUE;

// 메소드에 접근
System.out.println(r.getDesc()); // 빨강
System.out.println(g.getDesc()); // 초록
System.out.println(b.getDesc()); // 블루
```

> `RED`와 같이 열거 상수를 정의하는 것이 `Color` 인스턴스 생성과 비슷하다고 생각할 수 있습니다. 그렇기 떄문에 생성자 포맷대로 정의 합니다.

열거 상수는 자바 런타임에 의해 생성되고 생성자는 자동으로 `private`으로 선언되기 때문에 외부에서 인스턴스화는 불가능 합니다.

![](https://user-images.githubusercontent.com/12427330/106282034-61718700-6283-11eb-8dcd-d52382b73365.png)
_생성자를 private으로 선언한 적은 없지만..._


# Enum 비교

## 동일성 비교

Enum 상수 간의 동일성 비교는 `==`을 사용하여 비교가 가능합니다.

```java
Color r = Color.RED;
Color r2 = Color.RED;
Color g = Color.GREEN;
Color b = Color.BLUE;

r == r2; // true
r == g; // false
```

## compareTo()

`compareTo()` 메소드를 사용한다면 동일성 부터 대소 비교까지 가능합니다.

이 메소드 또한 `java.lang.Enum` 클래스에서 `Comparable` 인터페이스를 구현하여 정의되어 있습니다.

```java
Color r = Color.RED;
Color r2 = Color.RED;
Color g = Color.GREEN;
Color b = Color.BLUE;

System.out.println(g.compareTo(r)); // -1
System.out.println(g.compareTo(g1)); // 0
System.out.println(g.compareTo(b)); // 1
```

# values(), valueOf()

Enum 클래스를 정의하면 `static`한 메소드인 `values()`와 `valueOf()` 메소드를 제공 합니다.

## values()

`values()` 메소드는 해당 Enum 클래스의 모든 열거 상수 목록을 리턴합니다.

```java
Color[] values = Color.values();

for (Color value : values) {
    System.out.println(value);
}
```
```
RED
GREEN
BLUE
```

## valueOf()

`valueOf()` 메소드는 `Enum`의 기본 필드인 `name` 필드를 기반으로 동일한 열거 상수를 반환하고 존재하지 않을 때엔 `IllegalArgumentException` 예외를 던집니다.

```java
Color.valueOf("RED"); // RED
Color.valueOf("BLACK"); // IllegalArgumentException 예외 발생!
```


# EnumSet, EnumMap

## EnumSet

`EnumSet`은 Enum과 함께 사용할 수 있는 특별한 `Set` 구현체 입니다.

![](https://user-images.githubusercontent.com/12427330/106355894-62b3ba00-633e-11eb-881b-68b25c4ea7c6.png)
_`EnumSet` 계층구조_

`HashSet`의 경우 데이터를 내부적으로 표현할 때 해시 테이블(Hash Table)을 사용하지만

`EnumSet`의 경우에는 비트 벡터(Bit Vector)로 표현합니다.

또한 이 클래스의 모든 기본적인 작업은 상수 시간이 걸리기 때문에 HashSet과 비교하여 더 빠를 수 있습니다.

벌크 연산일 경우에도 마찬가지로 인수가 열거 집합일 경우에는 상수 시간이 걸립니다.

`EnumSet`은 추상 클래스이므로 직접 `new`를 사용한 인스턴스화가 불가능하지만 다양한 `static` 메소드를 제공하므로 그를 통해 사용할 수 있습니다.

```java
// Color의 모든 열거 상수를 EnumSet으로 가져오기
EnumSet<Color> colors = EnumSet.allOf(Color.class);

// Color의 열거 상수 중 선택적으로 EnumSet으로 가져오기
EnumSet<Color> red = EnumSet.of(Color.RED, Color.BLUE);

// Color의 열거 상수를 범위로 지정하여 해당 범위 모든 열거 상수 가져오기
EnumSet<Color> range = EnumSet.range(Color.RED, Color.BLUE);
```

## EnumMap

`EnumMap`은 맵의 키의 타입을 열거형으로 가지는 특수한 `Map` 입니다.

![](https://user-images.githubusercontent.com/12427330/106355896-65161400-633e-11eb-8bd8-84a683a7b240.png)
_`EnumMap` 계층구조_

이 맵은 내부적으로 배열로 표현되며 특정 Enum만을 키로 가지기 때문에 작고 효율적입니다.

이 맵은 순서가 보장되는데 이 순서는 `put` 메소드를 사용하여 입력한 순서가 아닌 Enum 내부의 열거 상수의 순서인 `ordinal`의 순서에 따릅니다.

```java
EnumMap<Color, String> colorStringEnumMap = new EnumMap<>(Color.class);

colorStringEnumMap.put(Color.RED, "빨간색");
colorStringEnumMap.put(Color.BLUE, "파란색");
colorStringEnumMap.put(Color.GREEN, "초록색");

colorStringEnumMap.forEach((k, v) -> {
    System.out.println("key: " + k + ", value: " + v);
});
```
```
key: RED, value: 빨간색
key: GREEN, value: 초록색
key: BLUE, value: 파란색
```

> 맵에 데이터를 입력할 떄는 RED, BLUE, GREEN 순으로 입력하였지만 순차적으로 출력을 해보면 RED, GREEN, BLUE 순으로 출력되는 것을 확인할 수 있습니다.
