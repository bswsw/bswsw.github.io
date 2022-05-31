---
title: "[Live Study] 15주차 과제: 람다식"
author: Bae Sangwoo
date: 2021-03-06 16:11:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, lambda]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 람다식에 대해 학습하세요.


# 람다식 사용법

람다식은 자바8에서 도입된 기능으로 자바 플랫폼에 큰 변화를 주었습니다.

- 더 표현력있는 프로그래밍
- 더 좋은 라이브러리
- 간결한 코드
- 향상된 프로그래밍 안전성
- 잠재적으로 증가된 데이터 병렬처리

또한 람다에는 기능의 필수 특성을 정의하는데 도움이 되는 주요 특징이 있습니다.

- 리터럴로 코드를 작성할 수 있음
- 타입 추론을 사용하여 자바 코드의 엄격한 네이밍 규칙을 완화함
- 자바 프로그래밍의 더 기능적인 스타일을 용의하게 하기 위함

자바8 이전에는 익명 클래스를 사용하던 것을 람다를 사용하여 더 간결한 코드를 작성할 수 있습니다.

> 람다가 익명 클래스의 문법 설탕은 아닙니다. 람다는 메소드 핸들과 `invokedynamic`이라는 특별한 바이트 코드를 사용하여 구현됩니다.

```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("run Runnable...");
    }
};
```

이런 익명클래스를 람다식으로 바꾸면 아래와 같습니다.

```java
Runnable runnable = () -> System.out.println("run Runnable...");
```


# 함수형 인터페이스

람다식은 특정한 타입에서만 사용이 가능한데 이것을 함수형 인터페이스(Functional Interface) 라고 합니다.

- 인터페이스(`interface`)이어야 함
- `default` 메소드가 아닌 메소드가 하나만 있어야 함

```java
// 함수형 인터페이스
@FunctionalInterface
public interface MyListener {
    void listen(String data);
}

// 함수형 인터페이스를 파라미터로 받는 메소드
public void onAction(MyListener listener) {
    listener.listen("data");
}

// 함수형 인터페이스 람다로 구현
MyListener listener = data -> System.out.println("listening data : " + data);

// 함수 실행
onAction(listener);
```

```
listening data : data
```

만약 `MyListener`가 재사용되지 않는다면 이렇게 작성도 가능합니다.

```java
// 함수형 인터페이스
@FunctionalInterface
public interface MyListener {
    void listen(String data);
}

// 함수형 인터페이스를 파라미터로 받는 메소드
public void onAction(MyListener listener) {
    listener.listen("data");
}

// 메소드 파라미터에 람다로 바로 구현
onAction(data -> {
    System.out.println("data : " + data);
    System.out.println("data length : " + data.length());
});
```

```
data : data
data length : 4
```


# Variable Capture

람다식에서 내부의 파라미터를 제외한 외부의 변수를 참조하는 것을 Variable Capture 라고 합니다.

이 때 참조할 수 있는 변수의 제약 조건이 있습니다.

- `final` 변수이어야 함
- `final` 변수가 아니라면 값이 재할당 되지 않아야 함 (Effectively final)

```java
public class LambdaApp {
    
    public void run() {
        final String str = "this is local variable"; // final 변수
        int num = 10; // final은 아니지만 정의 이후 재할당 되지 않는 변수

        onAction(data -> {
            System.out.println("str : " + str);
            System.out.println("num : " + num);
        });
    }

    public void onAction(MyListener listener) {
        listener.listen("data");
    }

    public static void main(String[] args) {
        LambdaApp app = new LambdaApp();
        app.run();
    }
}
```

```
str : this is local variable
num : 10
```

만약 `num` 변수의 값이 재할당 된다면 컴파일 에러가 발생 합니다.

```java
public class LambdaApp {
    
    public void run() {
        final String str = "this is local variable"; // final 변수
        int num = 10; // final은 아니지만 정의 이후 재할당 되지 않는 변수

        num = 20; // num 재할당

        onAction(data -> {
            System.out.println("str : " + str);
            System.out.println("num : " + num); // 컴파일 에러 발생!
        });
    }

    ...
}
```

![](https://user-images.githubusercontent.com/12427330/110202063-cdaa6080-7ea9-11eb-8440-72ee72698085.png)

만약 재할당된 값을 람다식에서 사용하고 싶다면 해당 값을 다른 변수로 할당하여 처리할 수 있습니다.

```java

public class LambdaApp {
    
    public void run() {
        final String str = "this is local variable"; // final 변수
        int num = 10; // final은 아니지만 정의 이후 재할당 되지 않는 변수

        num += IntStream.range(0, 5).sum();

        int finalNum = num; // 새 변수에 할당

        onAction(data -> {
            System.out.println("str : " + str);
            System.out.println("num : " + finalNum); // 새 변수 사용
        });
    }

    ...
}
```

```
str : this is local variable
num : 20
```

다만 인스턴스 변수라면 상황이 다릅니다.

```java
public class LambdaApp {
    private int instanceInt = 100; // 인스턴스 변수 선언

    public void run() {
        final String str = "this is local variable";
        int num = 10;

        onAction(data -> {
            System.out.println("str : " + str);
            System.out.println("num : " + num);
            System.out.println("instanceInt : " + instanceInt); // 람다에서 인스턴스 변수 사용
        });
    }

    public void onAction(MyListener listener) {
        listener.listen("data");
    }

    public static void main(String[] args) {
        LambdaApp app = new LambdaApp();
        app.run();
    }
}
```

```
str : this is local variable
num : 10
instanceInt : 100
```

이렇게 람다에서 인스턴스 변수인 `instanceInt`를 사용할 수 있고 중간에 `instanceInt` 값이 재할당된다고 하더라도 람다에서 사용이 가능합니다.

```java
public class LambdaApp {
    private int instanceInt = 100;

    public void run() {
        final String str = "this is local variable";
        int num = 10;

        instanceInt += 200; // 인스턴스 변수 재할당

        onAction(data -> {
            System.out.println("str : " + str);
            System.out.println("num : " + num);
            System.out.println("instanceInt : " + instanceInt); // 컴파일 에러가 나지 않고 정상 실행
        });
    }

    ...
}
```

```
str : this is local variable
num : 10
instanceInt : 300
```

> 이게 가능한 이유는 JVM의 메모리 구조와 관련이 있습니다.
<br/>지역 변수는 스택 영역에 저장이 되며 스택 영역은 스레드마다 별도로 생성이 됩니다.
<br/>인스턴스 변수는 힙 영역에 생성이 되며 스레드끼리 공유가 가능합니다.
<br/>람다식에서는 스택에 저장되는 지역 변수를 바로 참조하는 것이 아니라 복사된 값을 사용하게 되는데 멀티 스레드 환경에서 문제가 되기 때문에 재할당된 변수를 람다에서 사용할 수 없게 처리하였습니다.


# 메소드, 생성자 레퍼런스

람다에서 조금 더 간결한 문법을 사용할 수 있는 방법으로써 기존 메소드를 람다식으로 사용하는 방법 입니다.

람다식의 파라미터의 타입을 추론하여 해당 타입의 메소드를 사용하는 것입니다.

```java
// 함수형 인터페이스
@FunctionalInterface
public interface ButtonClickListener {
    void onClick(String data);
}

public class Button {
    // 함수형 인터페이스를 받는 메소드
    public void click(ButtonClickListener listener) {
        listener.onClick(data);
    }
}

Button button = new Button();
```

이러한 함수형 인터페이스와 그 것을 받는 메소드가 있다면 다음과 같이 람다식으로 사용할 수 있을 겁니다.

```java
Button button = new Button();

button.click(data -> data.length()); // data의 메소드 사용
button.click(data -> System.out.println(data)); // data를 파라미터로 사용
button.click(data -> Integer.parseInt(data)); // 스태틱 메소드에서 data를 파라미터로 사용
```

이 것들을 모두 메소드 레퍼런스로 변경하면 다음과 같습니다.

```java
Button button = new Button();

// Unbound Method Reference
button.click(String::length); 
// Bound Method Reference
button.click(System.out::println);
// Static Bound Method Reference
button.click(Integer::parseInt); 
```

생성자 레퍼런스를 사용할 수도 있습니다.

```java
// 기존 람다식
button.click(data -> new StringBuilder(data));

// 생성자 레퍼런스
button.click(StringBuilder::new);
```
