---
title: "[Live Study] 5주차 과제: 클래스"
author: Bae Sangwoo
date: 2020-12-19 17:22:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, class]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 `class`에 대해 학습하세요.


# 클래스 정의하는 방법

클래스란 객체를 만들어서 사용할 수 있는 설계도나 틀로 이해할 수 있습니다.

클래스는 데이터와 기능으로 구성되어 있으며 데이터는 필드로 정의하고 기능은 메소드로 구현합니다.

- `class` 키워드와 중괄호(`{`, `}`)를 사용하여 클래스 정의
- `class` 키워드 앞에 제어자(modifier)가 필수로 들어가야함
    - `public`, `protected`, `private` : 접근 제어자
    - `abstract` : 추상 클래스 정의
    - `final` : 확장 불가능한 클래스 정의
    - `strictfp` : 엄격한 부동소수점 클래스 정의 (플랫폼 간 부동소수점 정밀도 보장)
- 어노테이션(annotation) 추가 가능 (ex: `@Controller`)
- 다른 클래스를 확장(상속)하는 경우 `extends` 를 사용
- 하나 이상의 인터페이스를 구현할 수 있으며 `implements` 키워드와 쉼표(`,`)를 사용

```java
// 클래스 정의
public class User {

    // 데이터를 정의한 필드들
    private static final String DESCRIPTION = "유저 클래스";
    private String name = "sangwoo";
    private int age = 20;

    // 기능을 정의한 메소드들
    public static String getDescription() {
        return DESCRIPTION;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

}
```


# 객체 만드는 방법 (new 키워드 이해하기)

객체(instance)란 클래스를 통해 만들어 사용할 수 있는 실체를 말합니다.

흔히 붕어빵에 비교를 많이 하는데 붕어빵 틀을 클래스, 붕어빵을 객체로 비교하고는 합니다.

객체를 생성할 때 가장 일반적인 방법으로는 `new` 키워드를 사용할 수 있습니다.

```java
User sangwoo = new User();
```

위 예제로 보면 `User`은 클래스(붕어빵 틀)이며 `sangwoo`는 객체(붕어빵)이 됩니다.


# 메소드 정의하는 방법

메소드란 클래스에서 기능을 담당하며 특정 작업을 수행하고 결과를 호출자에게 반환하는 역할을 합니다.

결과를 반환하지 않고 내부적인 작업만 수행할 수도 있으며 반복적인 작업을 재사용하는 메소드도 작성할 수 있습니다.

메소드는 클래스 메소드(Class Method)와 객체 메소드(Instance Method)로 구분할 수 있습니다.

- 클래스 메소드 : 정적 메소드라고도 하며 객체를 생성하지 않고 클래스 자체와 관련이 있는 메소드
- 객체 메소드 : 클래스를 통해 생성된 객체에서 사용할 수 있는 메소드

![](https://media.geeksforgeeks.org/wp-content/uploads/methods-in-java.png)
_메소드 작성 방법 (출처 : https://www.geeksforgeeks.org/methods-in-java/)_

위 예제 `User` 클래스에서 메소드는 아래와 같습니다.

```java
public class User {

    ...
    
    // 클래스 메소드
    public static String getDescription() {
        return DESCRIPTION;
    }

    // 객체 메소드
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

}
```

`static` 키워드가 붙은 메소드가 클래스 메소드(정적 메소드)이며 아래와 같이 사용할 수 있습니다.

객체를 생성하지 않고 클래스를 통해 직접 사용할 수 있습니다.

```java
User.getDescription(); // 유저 클래스
```

객체 메소드는 객체를 생성하여 사용할 수 있습니다.

```java
// 객체 생성
User sangwoo = new User();

//메소드 사용
sangwoo.getName(); // sangwoo
sangwoo.getAge(); // 20
```


# 생성자 정의하는 방법

생성자(constructor)는 객체를 생성하는 방법을 말합니다.

위 `User` 클래스에는 아직 생성자가 없기 때문에 객체를 생성할 때 필드에 원하는 값을 넣어줄 수 없습니다.

생성자를 사용하여 원하는 이름을 주입할 수 있습니다.

생성자의 규칙은 아래와 같습니다.

```java
<public | private | protected> 클래스명 (<파라미터1>, <파라미터...>) {
    // 생성자 로직...
}
```

`User` 클래스에 생성자를 적용하여 객체를 생성하려면 아래와 같이 할 수 있습니다.

```java
public class User {

    ...

    private String name;
    private int age;

    // 생성자 : 이름을 입력 받는다.
    public User(String name) {
        this.name = name;
        this.age = 20;
    }

    ...

}

// 생성자를 사용하여 객체 생성
User sangwoo = new User("baesangwoo");
sangwoo.getName(); // baesangwoo
```

다만 이제는 파라미터 없는 기본 생성자는 사용할 수 없으며 필요하다면 여러개의 생성자를 정의할 수 있습니다.

![](/assets/img/posts/2020-12-19-java-livestudy-5week/constructor.png)

```java
public class User {

    ...

    private String name;
    private int age;

    public User(String name) {
        this.name = name;
        this.age = 20;
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    ...

}

// 여러 생성자를 사용하여 객체 생성
User user1 = new User("sangwoo");
User user2 = new User("alan", "30");
```


# this 키워드 이해하기

`this`는 현재 객체를 나타내며 일반적으로는 필요하지 않기 때문에 메소드 등에서 사용되지 않습니다.

어떠한 메소드에서 객체의 필드나 메소드에 접근한다면 암시적으로 `this`에 접근하는 것입니다.

만약 메소드에서 받은 파라미터가 객체의 필드와 이름이 동일하다면 명시적으로 `this`로 구분해야 합니다.

생성자 예제를 다시 보면...

```java
public class User {

    ...

    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    ...

}
```

위 예제에서 `this.name`과 `this.age`는 객체의 필드를 의미하며 `name`과 `age`는 파라미터를 의미합니다.

또한 메소드가 해당 객체의 필드나 메소드를 사용하고 있다는 것을 분명히 할 때 `this`를 사용할 수 있습니다.
