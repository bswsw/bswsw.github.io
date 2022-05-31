---
title: "[Live Study] 12주차 과제: 애노테이션"
author: Bae Sangwoo
date: 2021-02-05 19:03:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, annotation]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 애노테이션에 대해 학습하세요.


# 애노테이션이란?

애노테이션(annotation)은 자바의 특수한 인터페이스 이며 기본적으로 이름처럼 주석을 다는 역할을 합니다.

다만 일반적인 주석과는 다르게 추가적인 작업을 덧붙일 수 있으며 IDE에 유용한 힌트를 줄 수도 있습니다.

예를 들어 메소드에 `@Override` 애노테이션이 붙으면 오버라이딩 메소드라고 알려주는 주석의 역할을 하면서 만약 오버라이딩 메소드가 아니라면 IDE 단에서 알려주는 힌트의 역할을 합니다.

![](https://user-images.githubusercontent.com/12427330/107027956-368db280-67f0-11eb-83b0-7644a084a0dd.png)

그 외 특징은 아래와 같습니다.

- 모든 애노테이션은 암묵적으로 `java.lang.annotation.Annotation` 인터페이스를 상속합니다.
- 다른 인터페이스를 상속할 수 없습니다.
- 예외를 던지는 메소드를 정의할 수 없습니다.
- 메소드의 리턴 타입에 대한 제약 조건이 있습니다.
- 메소드에 기본 리턴값을 가질 수 있습니다.


# 애노테이션 정의

애노테이션은 `@interface` 키워드를 사용하여 정의할 수 있습니다.

```java
@Retention(RetentionPolicy.SOURCE)
@Target(ElementType.METHOD)
public @interface Logger {
    String value() default "";
}
```

위와 같은 애노테이션이 적용 되었다면 코드에 적용할 수 있습니다.

```java
public class MyService {

    @Logger
    public void process() {
        // business...
    }

}
```


# 메타 애노테이션

메타 애노테이션은 개발자가 새로운 애노테이션을 만들 때 사용할 위치나 자바 컴파일러나 런타임에서 어떻게 처리될지 정책을 명시하는 애노테이션을 말합니다.

위 `@Logger` 예제에서 `@Retention`과 `@Target`이 이에 해당하며 가장 기본적인 메타 애노테이션 입니다.

## @Retention

위 예제를 컴파일하여 `MyService.class` 파일을 열어보면 애노테이션이 빠져있는 것을 볼 수 있습니다.

![](https://user-images.githubusercontent.com/12427330/107028446-eb27d400-67f0-11eb-9286-ab240557ec59.png)

이 이유는 `@Logger` 애노테이션에 붙어있는 `@Retention` 애노테이션 때문 입니다.

`@Retention` 애노테이션은 자바 컴파일러와 자바 런타임이 커스텀 애노테이션을 어떻게 처리해야하는지 알려주는 역할을 합니다.

3가지 열거 상수가 정해져 있는 `java.lang.annotation.RetentionPolicy` Enum으로 이를 표현 합니다.

- `SOURCE` : 소스 상에서만 애노테이션이 보이고 컴파일 되면 사라집니다.
- `CLASS` : 애노테이션이 클래스 파일에 존재하지만 JVM을 통한 자바 런타임이 접근할 수 없습니다. 잘 사용되지는 않지만 바이트 코드를 분석할 때 사용될 수 있습니다.
- `RUNTIME` : 자바 런타임이 애노테이션을 접근할 수 있고 그를 통해 무언가 추가적인 작업이 가능함을 의미 합니다.

이와 같은 이유로 `@Logger` 애노테이션은 컴파일된 클래스 파일에서는 볼 수 없었던 것이고 만약 `RUNTIME`으로 변경한다면 클래스 파일에서도 애노테이션을 볼 수 있습니다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Logger {
    String value() default "";
}
```

![](https://user-images.githubusercontent.com/12427330/107029658-bae13500-67f2-11eb-956b-d951e0e67fcf.png)
_컴파일 되었기 때문에 당연히 FQCN으로 보입니다._

## @Target

예제에서 `@Logger` 애노테이션을 클래스에 붙인다면 컴파일 에러가 발생합니다.

![](https://user-images.githubusercontent.com/12427330/107028157-83718900-67f0-11eb-9f1a-56e029b71177.png)

이유는 애노테이션에 정의되어 있는 `@Target` 애노테이션 설정 때문입니다.

이 애노테이션은 `java.lang.annotation.ElementType` Enum을 사용하여 표현합니다.

- `TYPE` : 클래스, 인터페이스, 어노테이션, Enum, 레코드(jdk14 이상)
- `FIELD` : 필드, Enum 상수
- `METHOD` : 메소드
- `PARAMETER` : 파라미터
- `CONSTRUCTOR` : 생성자
- `LOCAL_VARIABLE` : 지역 변수
- `ANNOTATION_TYPE` : 애노테이션
- `PACKAGE` : 패키지
- 이외 `TYPE_PARAMETER`, `TYPE_USE`, `MODULE`

`@Target`은 값을 배열로 받기 때문에 애노테이션을 여러 타겟에 달도록 지정할 수 있습니다.

클래스에 `@Logger` 애노테이션을 추가할 수 있도록 `TYPE`을 추가 하였습니다.

```java
@Target({ElementType.METHOD, ElementType.TYPE}) // 메소드와 타입에 애노테이션 설정 가능
@Retention(RetentionPolicy.RUNTIME)
public @interface Logger {
    String value() default "";
}
```

```java
@Logger // 컴파일 이상 없음.
public class MyService {

    @Logger
    public void process() {
        // business...
    }

}
```

## @Documented

애노테이션에 `@Documented`가 붙어있다면 Javadoc에 해당 애노테이션 정보를 표시합니다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Logger {
    String value() default "";
}
```

```java
@Logger
public class MyService {
    ...
}
```

이후에 `MyService` 클래스의 javadoc을 보면 애노테이션 정보가 있는 것을 볼 수 있습니다.

> Gradle Wrapper 기준 `./gradlew javadoc`을 프로젝트 루트에서 실행시키면 javadoc이 생성됩니다.

![](https://user-images.githubusercontent.com/12427330/107043360-3dbfbb00-6806-11eb-9dda-b2e97e65b3ed.png)
_@Documented 애노테이션 붙이기 전_

![](https://user-images.githubusercontent.com/12427330/107043315-2d0f4500-6806-11eb-918d-85d8b601543d.png)
_@Documented 애노테이션 붙인 후_

## @Inherited

`@Inherited`를 적용한 애노테이션을 클래스에 적용하면

그 클래스를 상속한 자식 클래스도 해당 애노테이션을 가지게 됩니다.


# 애노테이션 프로세서

애노테이션은 기본적으로 주석의 역할만 하지만 애노테이션 프로세서가 있다면 애노테이션을 통해 어떤 작업을 할 수 있게 됩니다.

하나의 예로 [롬복](https://projectlombok.org/)의 애노테이션들은 가장 많이 사용하는 애노테이션 프로세서가 적용된 애노테이션 모음 입니다.

## 애노테이션 프로세서 만들기

`javax.annotation.processing.Processor` 인터페이스를 구현하여 만들 수 있지만 자바가 제공하는 기본적인 추상 클래스인 `AbstractProcessor`를 사용할 수 있습니다.

> 애노테이션은 위에서 예제로 사용한 `@Logger` 애노테이션을 사용하겠습니다.

```java
public class LoggerProcessor extends AbstractProcessor {

    /**
     * 이 프로세서로 처리할 애노테이션
     */
    @Override
    public Set<String> getSupportedOptions() {
        return Set.of(Logger.class.getName());
    }

    /**
     * 어떤 소스코드 버전을 지원할 것인지 명시 (필요하지 않다면 재정의하지 않아도 됨)
     */
    @Override
    public SourceVersion getSupportedSourceVersion() {
        return SourceVersion.latestSupported();
    }

    /**
     * 애노테이션을 처리하기
     */
    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        Set<? extends Element> elements = roundEnv.getElementsAnnotatedWith(Logger.class);

        elements.forEach(el -> {
            if (el.getKind() != ElementKind.METHOD) {
                processingEnv.getMessager().printMessage(Diagnostic.Kind.ERROR, "Cannot use this annotation!"); // 애노테이션이 메소드에 붙어있지 않다면 에러 발생!
            } else {
                processingEnv.getMessager().printMessage(Diagnostic.Kind.NOTE, "Processing...");
            }
        });

        return true; // 애노테이션이 이 프로세서에 특화되어있다면 true, 다른 프로레서에서도 처리가 가능하다면 false
    }
}
```

## 애노테이션 프로세서 등록하기

이제 `src/main/resources/META-INF/services` 디렉토리에 `javax.annotation.processing.Processor` 라는 파일을 생성하고 이 애노테이션 프로세서의 FQCN을 입력한 후 컴파일 합니다.

![](https://user-images.githubusercontent.com/12427330/107050667-da865680-680e-11eb-91af-17d820fb33bc.png)

그러나 컴파일 한다면 에러가 발생합니다. 아직 프로세서가 컴파일되지 않은 시점에 `javax.annotation.processing.Processor` 파일의 설정을 읽으려고하기 때문에 문제가 됩니다.

해결책은 아래와 같습니다.

1. `javax.annotation.processing.Processor` 파일의 프로세서를 주석을 건다.
2. 컴파일 한다.
3. `javax.annotation.processing.Processor` 파일에서 프로세서 주석을 제거한다.
3. 다시 컴파일 한다. (이 때 `clean` 명령은 사용하지 않는다.)

구글에서 만든 [AutoService](https://github.com/google/auto/tree/master/service){:target="_blank"}를 사용한다면 이 과정이 간단해 집니다.

## 더 자세히 보려면...

인프런에서 백기선님 강의인 [더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation#){:target="_blank"} 강의에서 더 자세하게 알아볼 수 있습니다.
