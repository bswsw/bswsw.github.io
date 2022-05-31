---
title: "[Live Study] 7주차 과제: 패키지"
author: Bae Sangwoo
date: 2020-12-28 21:10:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, package]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 패키지에 대해 학습하세요.


# package 키워드

## 패키지란?

- 패키지란 여러분이 만든 클래스나 인터페이스 등을 모은 단위 입니다.
- 관련 클래스를 그룹화하고 포함된 클래스의 네임스페이스를 정의하는 역할 입니다.
- 모든 클래스에는 정의된 클래스 이름과 패키지 이름이 있습니다.
- 이 둘을 합쳐야 완전하게 한 클래스를 표현한다고 할 수 있으며 `FQCN(Fully Qualified Class Name)` 이라고 합니다.
- 파일 시스템의 디렉토리와 비슷한 역할을 하기 때문에 패키지명과 동일한 디렉토리 구조를 따라야 합니다.

예를 들어 `String` 클래스의 패키지는 `java.lang`이며 FQCN은 `java.lang.String`이 됩니다.

```java
String a = "Class Name";
java.lang.String b = "Full Package Class Name";
```

## package 키워드 사용법

`package` 키워드는 클래스가 포함될 패키지를 지정할 때 사용 됩니다.

예들 들어 여러분이 만든 클래스에 패키지명을 주기 위해 클래스 파일 상단에 아래와 같이 작성할 수 있습니다.

```java
package dev.baesangwoo.livestudy.week7;

class SampleClass { 
    // code...
}
```

위와 같은 클래스가 있다면 FQCN은 `dev.baesangwoo.livestudy.week7.SampleClass`가 됩니다.

만약 동일 패키지에 있는 클래스를 사용한다면 패키지명은 명시하지 않아도 됩니다.

```java
// Sample Class
package dev.baesangwoo.livestudy.week7;

class SampleClass { 
    // code...
}

// Example Class
package dev.baesangwoo.livestudy.week7; // 동일 패키지

class ExampleClass {
    SampleClass sample = new SampleClass();
}
```


# import 키워드

`import` 키워드는 다른 패키지에 있는 클래스나 인터페이스 등을 참조할 때 사용합니다.

동일 패키지의 클래스나 `java.lang` 패키지의 클래스는 `import` 구문 없이 참조 가능합니다.

이때 규칙은 FQCN을 입력해야 합니다.

```java
package dev.baesangwoo.livestudy.week7.model; // 클래스의 패키지

import dev.baesangwoo.livestudy.week7.ExampleClass; // 타 패키지 클래스 임포트
import dev.baesangwoo.livestudy.week7.SampleClass; // 타 패키지 클래스 임포트

public class Week7Model {
    SampleClass sample = new SampleClass();
    ExampleClass example = new ExampleClass();
}
```

만약 한 패키지의 여러 클래스를 임포트 한다면 `*`를 사용하여 임포트 할 수 있습니다. (다만 추천하진 않습니다.)

```java
package dev.baesangwoo.livestudy.week7.model;

import dev.baesangwoo.livestudy.week7.*; // week7 패키지 모든 클래스 참조 가능

public class Week7Model {
    SampleClass sample = new SampleClass();
    ExampleClass example = new ExampleClass();
}
```

또한 정적(static) 멤버도 임포트도 가능합니다.

```java
package dev.baesangwoo.livestudy.week7.model;

import static java.lang.System.out; // static 임포트

public class Week7Model {
    public void print() {
        out.println("println");
        out.print("print");
    }
}
```


# 클래스패스(Classpath)

클래스패스는 JVM 혹은 Java 컴파일러가 사용하는 파라미터인데 클래스나 패키지를 찾을 때 기준이 되는 경로를 말합니다.

즉, `java` 명령을 통해 클래스 파일을 실행할 때 클래스 파일을 찾는 기준이 되는 경로를 클래스패스라고 하며 기본적으로는 `java` 명령을 실행하는 위치를 의미 합니다.

![](/assets/img/posts/2020-12-28-java-livestudy-7week/classpath.png)

`java HelloWorld` 명령이 실행 되는 현재 경로가 기본 클래스패스 입니다.


# CLASSPATH 환경변수

위에서 설명한 클래스패스를 환경 변수를 통해 설정할 수 있습니다.

![](/assets/img/posts/2020-12-28-java-livestudy-7week/classpath-environment.png)

위 예제와 같은 경로에서 동일한 명령인 `java HelloWorld`를 실행했지만 클래스를 찾을 수 없습니다.

상단에서 `CLASSPATH` 환경 변수를 설정 했기 때문에 환경 변수에 설정된 클래스패스에서 `HelloWorld.class` 파일을 찾기 때문에 클래스를 찾을 수 없는 것 입니다.


# -classpath 옵션

`java` 명령 실행 시 옵션으로 클래스패스를 지정할 수 있습니다.

![](/assets/img/posts/2020-12-28-java-livestudy-7week/classpath-option.png)

또한 `CLASSPATH` 환경 변수 설정 후 실행 하였는데 `-classpath` 옵션이 우선 순위가 높은 것을 볼 수 있습니다.

또한 단축 옵션인 `-cp` 옵션도 동일한 기능을 합니다.


# 접근지시자 (Access Modifiers)

접근 지시자는 멤버 변수나 메소드들의 접근 범위를 정의하기 위해 사용 합니다.

- `public` : 접근을 제한하지 않아 어디서든 접근이 가능합니다.
- `private` : 클래스 내부에서만 접근을 허용 합니다.
- `protected` : 클래스 내부, 동일 패키지, 상속받은 클래스에서만 접근을 허용 합니다.
- default(명시하지 않음) : 클래스 내부와 동일 패키지에서만 접근이 가능합니다.

|지시자	|클래스 내부	|동일 패키지	|상속받은 클래스  |이외 |
|:-----|:----------|:----------|:--------------|:----|
|`private`|O |X|X|X|
|default|O|O|X|X|
|`protected`|O|O|O|X|
|`public`|O|O|O|O|
