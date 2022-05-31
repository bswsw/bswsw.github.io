---
title: "[Live Study] 1주차 과제: JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가."
author: Bae Sangwoo
date: 2020-11-17 21:18:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, jvm]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기


# JVM이란 무엇인가

- JVM은 Java Virtual Machine의 약자로 자바 바이트코드를 실행하기 위한 환경을 제공하는 프로그램입니다.
- 한 번에 하나의 바이트코드 명령을 실행하는 인터프리터를 실행합니다.
- 자바 소스코드를 바로 실행할 수는 없으며 바이트코드로 컴파일하여 실행할 수 있습니다.
- 다양한 플랫폼의 실행 환경을 제공하기 때문에 한 번 작성된 자바 어플리케이션은 JVM이 설치되어 있는 환경이라면 코드의 변경 없이 실행이 가능합니다.
- Garbage Collector(GC)를 통해 메모리 관리를 해주기 때문에 개발자의 부담을 덜어줍니다. (다만, 튜닝이 필요할 수는 있습니다.)


# 컴파일 하는 방법

컴파일을 하기 위해서는 자바 컴파일러가 필요한데 이 컴파일러는 JDK(Java Development Kit)이 필요합니다.

JDK가 설치되어 있고 만약 `HelloWorld.java` 라는 자바 소스 파일이 있다면 아래와 같이 컴파일이 가능합니다.

```console
$ javac HelloWorld.java
```

위 명령으로 컴파일을 하면 바이트코드인 `HelloWorld.class` 이 생성됩니다.

실제 바이트코드의 내용을 보려면 아래의 명령어를 입력하면 됩니다.

```console
$ javap -c HelloWorld
```


# 실행하는 방법

위 예제로 컴파일하여 생성한 바이트코드를 실행하려면 아래와 같이 입력해주세요.

```console
$ java HelloWorld
```


# 바이트코드란 무엇인가

바이트코드는 하드웨어 프로세서에서 실행되는 기계어와 유사하지는 않으며 

컴퓨터의 CPU에서 동작하는 코드라고 생각할 수 있지만 그렇지 않습니다.

자바 프로그램은 JVM에서 실행되기 때문입니다.

바이트코드란 JVM 인터프린터가 이해하고 실행할수 있는 형태를 말합니다.


# JIT 컴파일러란 무엇이며 어떻게 동작하는지

JIT(Just-In-Time) 컴파일러는 자바 바이트코드를 프로세서가 이해할 수 있는 기계어로 컴파일하여 

자바 어플리케이션의 성능을 향상시킬 수 있는 런타임 환경의 구성요소 입니다.

플랫폼 초기에는 자바 컴파일러(javac)로 컴파일 할 때 최적화된 바이트코드를 많이 만들어냈지만 

JIT 컴파일러가 나오면서 중요한 메소드들은 이에 의해 매우 빠른 기계어로 컴파일됩니다.

JIT 컴파일러의 작업을 쉽게 하는 것이 바이트코드를 최적화하는 것 보다 더 큰 이득이 있습니다.


기본적으로 JVM은 한번의 하나의 바이트코드 명령만을 인터프리터로 실행하기 때문에 

JIT 컴파일러를 사용하여 미리 플랫폼이 이해할 수 있는 기계어로 컴파일 해놓고 캐싱이 되어 

마치 처음부터 그 플랫폼에서 컴파일 된 것처럼 보이기 때문에 성능이 향상 될 수 있습니다.

![](/assets/img/posts/2020-11-17-java-livestudy-1week/jit.png)
*출처 : https://www.javatpoint.com/jit-in-java*


# JVM 구성요소

![](/assets/img/posts/2020-11-17-java-livestudy-1week/jvm.png)
*출처 : https://www.alieranotes.com/post/jvm-architecture*

## 클래스 로더 (Class Loader)

클래스 로더는 클래스 파일을 읽고 메소드 영역(Method Area)에 바이트 코드를 저장합니다.

## 메소드 영역 (Method Area)

메소드 영역에는 `.class` 파일의 클래스 레벨 정보가 있습니다. 

JVM은 모든 클래스들 중에서 공유되는 하나의 메소드 영역만 제공 합니다.

## 힙 (Heap)

힙은 객체가 할당되는 JVM의 메모리 영역입니다. JVM은 각 클래스 파일에 대한 객체를 생성합니다.

## 스택 (Stack)

스택은 JVM의 메모리 영역이지만 힙과는 다르게 메소드 파라미터와 같은 임시 변수를 저장하는데 사용합니다.

## PC (Program Counter) Register

PC Register는 어떤 명령이 실행되고 어떤 명령이 실행될 것인지 추적을 유지하기 위해 사용 됩니다. 

명령은 스레드(Thread)에 의해 실행되기 때문에 각 스레드는 별도의 PC Register를 가지고 있습니다.

## JIT Compiler

[위의 설명을 읽어주세요~!](#jit-컴파일러란-무엇이며-어떻게-동작하는지)

## GC (Garbage Collection)

GC는 JVM의 메모리 관리를 위해서 사용 됩니다.

새로운 객체들에게 더 많은 메모리를 할당하기 위해 힙에서 참조되지 않는 객체를 파괴 합니다.

## 네이티브 메소드 스택 (Native Method Stack)

네이티브 메소드 스택은 JVM의 런타임 데이터 영역에 엑세스하는데 사용이 됩니다.

## 네이티브 메소드 인터페이스 (Native Method Interface)

C, C++로 작성된 네이티브 프로그램 코드를 호출하는데 사용 됩니다.

네이티브 프로그램은 시스템의 하드웨어나 OS에 고유한 Low-level 언어로 개발된 프로그램 입니다.


# JDK와 JRE의 차이

![](/assets/img/posts/2020-11-17-java-livestudy-1week/jdk-jre.png)
*출처 : https://www.worldofitech.com/java-programming-jvm-jre-jdk/*

## JRE (Java Runtime Environment)

JRE는 자바로 개발된 어플리케이션을 실행하기 위한 환경 입니다.

JVM과 자바 클래스 라이브러리를 포함하고 있으며 그 외 자바 어플리케이션을 실행하기 위한 세그먼트를 제공합니다.

## JDK (Java Development Kit)

JDK는 자바 어플리케이션을 개발하기 위한 도구를 제공 합니다.

JRE를 포함하고 있으며 개발을 위한 컴파일러, 디버거, JavaDoc 등이 포함 됩니다.
