---
title: "[Live Study] 2주차 과제: 자바 데이터 타입, 변수 그리고 배열"
author: Bae Sangwoo
date: 2020-11-21 22:04:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, type, variable, array]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 프리미티브 타입, 변수 그리고 배열을 사용하는 방법을 익힙니다.


# 프리미티브 타입 종류와 값의 범위 그리고 기본 값

|타입	|설명	|기본값	|크기	|범위|
|:---|:----|:-----|:---|:---|
|`boolean`	|`true` 또는 `false`	|`false`	|1 bit	|-|
|`char`	|유니코드 문자	|\u0000	|2 byte	|\u0000 ~ \uFFFF|
|`byte`	|부호가 있는 정수 (Signed Integer)	|0	|1 byte	|-128 ~ 127|
|`short`	|부호가 있는 정수 (Signed Integer)	|0	|2 byte	|-32768 ~ 32767|
|`int`	|부호가 있는 정수 (Signed Integer)	|0	|4 byte	|–2147483648 ~ 2147483647<br/>(-2e31 ~ 2e31-1)|
|`long`	|부호가 있는 정수 (Signed Integer)	|0	|8 byte	|–9223372036854775808 ~ 9223372036854775807<br/>(-2e63 ~ 2e63-1)|
|`float`	|IEEE 754 부동소수점	|0.0	|4 byte	|1.4E–45 ~ 3.4028235E+38|
|`double`	|IEEE 754 부동소수점	|0.0	|8 byte	|4.9E–324 ~ 1.7976931348623157E+308|


# 프리미티브 타입과 레퍼런스 타입

## 프리미티브 타입 (Primitive Type)

- 기본형 타입이라고 말하며 미리 정의되어 제공됩니다.
- 기본값이 존재합니다. (`null` 불가)
- 스택(Stack) 에 값이 저장됩니다.

## 레퍼런스 타입 (Reference Type)

- 프리미티브 타입을 제외하면 모두 레퍼런스 타입 입니다.
- `null`이 존재합니다.
- 힙 (Heap) 에 값이 저장됩니다.


# 리터럴

어떠한 변수를 선언할 때 변수에 넣는 값을 리터럴(Literal) 이라고 합니다.

```java
int a = 1;
```

> `int`는 자료형 `a`는 변수명 `1`은 리터럴이 됩니다.

## 정수 리터럴

```java
// 10진수 리터럴 : 일반적인 정수 입력을 합니다.
int decimal = 101;

// 8진수 리터럴 : 앞에 0을 붙이고 한자리 수에 0~7까지의 정수를 허용합니다.
int octal = 025;

// 16진수 리터럴 : 0x로 시작하며 0~9와 a~f를 허용합니다.
int hex = 0x34f;

// 바이너리 리터럴 : 0b로 시작하고 0, 1을 허용합니다.
int binary = 0b111;
```

## 부동 소수점 리터럴

```java
// double 리터럴
double d1 = 123.456;
double d2 = 123.456d;
double d3 = 0123.456;

// float 리터럴
float f1 = 123.456f;
float f2 = 0123.1222f;
```

## 문자 리터럴

```java
// 문자 1개를 리터럴할 수 있습니다.
char ch1 = 'a';

// 0 ~ 65535 까지 char형 리터럴이 될 수 있습니다.
char ch2 = 62;

// 유니코드 리터럴이 가능합니다.
char ch3 = '\u0012';

// 이스케이프 문자도 char형 리터럴이 가능합니다.
char ch4 = '\n';
```

## 문자열 리터럴

```java
// 쌍따옴표를 사용하여 기본적인 문자열 리터럴이 가능합니다.
String s = "Hello Java";

// + 연사자로 문자열을 결합하는 리터럴도 사용할 수 있습니다.
String text = "Hello Java\n"
        + "Hello String literal"
        + "Hello Java Study";
```

## 불리언(`boolean`) 리터럴

```java
// 참 리터럴
boolean b1 = true;

// 거짓 리터럴
boolean b2 = false;
```


# 변수 선언 및 초기화 하는 방법

```java
// 변수 선언 후 별도로 초기화
String a;
a = "Hello";

// 변수 선언하면서 초기화
String b = "Hello";

// 변수 여러개 선언
String c, d;

// 변수 여러개 선언 및 초기화
String e = "eee", f = "fff";
```


# 변수의 스코프와 라이프타임

|설명	|스코프	|라이프타임|
|:---|:-----|:-------|
|클래스 변수<br/>(Class Variable)	|클래스 내부나 외부의 모든 블럭에서 정의 되면서 `static`으로 선언된 변수|	클래스 전체	|프로그램 종료시 까지|
|인스턴스 변수<br/>(Instance Variable)	|클래스 내부에 선언되고 메소드나 블럭에서 선언되지 않는 변수	|`static` 메소드를 제외한 클래스 전체	|클래스의 객체가 메모리에 올라가 있는 동안|
|로컬 변수<br/>(Local Variable)	|클래스 변수, 인스턴스 변수가 아닌 모든 변수	|선언된 블럭 내	|변수가 선언된 블럭의 종료시 까지|

```java
public class ScopeAndLifetime {

    static String a; // 클래스 변수

    String b; // 인스턴스 변수

    void f() {
        String c; // 로컬 변수
    }

}
```


# 타입 변환, 캐스팅 그리고 타입 프로모션

## 타입 캐스팅

변수의 타입을 명시적으로 변환하는 방법이고 크기가 더 큰 자료형을 더 작은 자료형으로 대입할 때 

자료형을 명시하여 강제로 변환하는 방법이기도 합니다.

```java
float a = 1f;

int b = a; // 컴파일 에러 발생
int c = (int) a; // 명시적으로 int로 형변환
```

## 타입 프로모션

변수의 타입을 자동으로 변환하는 방법이고 크기가 더 작은 자료형을 더 큰 자료형으로 대입할 때 발생합니다.

```java
int a = 1;

float b = a; // 자동 형변환 (타입 프로모션)
```


# 1차 및 2차 배열 선언하기

배열(Array) 이란 동일한 자료형의 데이터를 연속된 공간에 저장하기 위한 선형 자료구조를 말합니다.

## 1차원 배열 선언하기

```java
int[] arr1;
int arr2[];

int[] arr3 = new int[2];
int[] arr4 = new int[]{1, 3, 3};
int[] arr5 = {1, 2, 3, 4};
```

## 2차원 배열 선언하기

```java
int[][] arr1;
int arr2[][];

int[][] arr3 = new int[2][3];
int[][] arr4 = new int[][]{ {1, 2}, {3, 4} };
int[][] arr5 = { {5, 6}, {7, 8}, {9, 10} };
```


# 타입 추론, var

타입 추론(Type Inference) 이란 타입이 정해지지 않은 변수의 타입을 컴파일러가 유추하는 기능을 말합니다.

이미 스칼라나 코틀린 등 다른 언어에서는 적극적으로 사용되고 있습니다.

자바는 `자바10` 부터 지원하며 `var` 키워드를 사용하여 타입 추론이 가능합니다.

```java
var a = "String"; // String
var b = 11; // int
var c = 'a'; // char
var d = 12.34; // double
var e = 12.34f; // float
```
