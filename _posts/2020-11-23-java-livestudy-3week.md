---
title: "[Live Study] 3주차 과제: 연산자"
author: Bae Sangwoo
date: 2020-11-23 19:57:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, operator]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바가 제공하는 다양한 연산자를 학습하세요.


# 산술 연산자

산술 연산자(Arithmetic Operators)는 정수, 부동소수점, 문자열 등 `boolean` 타입을 제외한 

모든 Primitive Type에서 사용이 가능합니다.

피연산자들 중 부동소수점이 있다면 부동소수점 산술이 되고 그렇지 않다면 정수 산술이 됩니다.

정수 산술과 부동소수점 산술이 나누기를 수행하는 방식과 

언더플로우(underflow)와 오버플로우(overflow)가 처리되는 방식이 다르기 때문에 중요합니다.

## 더하기 (Addition : `+`)

`+`는 기본적으로 두 수를 더하는 연산이지만 문자열을 연결시키는데도 사용할 수 있습니다.

만약 `+`의 피연산자 중 문자열이 있다면 다른 피연산자도 문자열로 변환됩니다.

```java
int num1 = 10;
int num2 = 20;

int sum = num1 + num2;
System.out.println(sum); // 30

String str1 = "Hello";
int num3 = 123;

String s = str1 + num3;
System.out.println(s); // Hello123
```

## 빼기 (Subtraction : `-`)

`-`연산자는 첫번째 피연산자에서 두번째 피연산자를 빼는 연산자입니다. (binary operator)

하나의 피연산자로만 사용된다면 1차 연산 (unary negation) 으로 사용될 수 있습니다.

다만 더하기 연산 처럼 문자열 연산은 불가합니다.

```java
int num1 = 10;
int num2 = 2;

int num3 = num1 - num2;
System.out.println(num3); // 8

String str1 = "Hello";
int num4 = 10;

str1 - num4; // 컴파일 에러
```

## 곱하기 (Multiplication : `*`)

`*` 연산자는 두 피연산자를 곱합니다.

빼기 연산과 마찬가지로 문자열 연산은 불가합니다.

```java
int num1 = 10;
int num2 = 2;

int num3 = num1 * num2;
System.out.println(num3); // 20

String str1 = "Hello";
int num4 = 10;

str1 * num4; // 컴파일 에러
```

## 나누기 (Division : `/`)

`/` 연산자는 첫번째 피연산자를 두번째 피연산자로 나눕니다.

두 피연산자가 모두 정수라면 결과도 정수이며 나머지는 내림으로 없어지고 피연산자 중 부동소수점이 있다면 결과도 부동소수점 입니다.

```java
System.out.println(7 / 3); // 2
System.out.println(7 / 3.0); // 2.3333333333333335
```

정수를 0으로 나누면 `ArithmeticException`이 발생 합니다.

```java
System.out.println(7 / 0);
```

부동소수점을 0으로 나눈다면 `Infinity` 혹은 `NaN`이 결과로 나옵니다.

```java
System.out.println("7 / 0.0 : " + 7 / 0.0);
System.out.println("0 / 0.0 : " + 0 / 0.0);
```

## 나머지 연산자 (Modulo : `%`)

`%` 연산자는 첫번째 피연산자를 두번째 피연산자로 나누고 남은 나머지를 정수로 리턴 합니다.

리턴된 결과는 첫번째 피연산자의 부호와 동일합니다.

일반적으로는 정수 피연산자와 사용되지만 부동소수점에도 사용이 가능하고 결과도 부동소수점으로 리턴 합니다.

```java
System.out.println(10 % 4); // 2
System.out.println(-10 % 4); // -2 (첫번째 피연산자의 부호와 결과의 부호가 동일)
System.out.println(10 % -4); // 2
System.out.println(); // 2.1000000000000014
```

정수로 연산할 때 0으로 연산하면 나누기와 마찬가지로 `ArithmeticException`이 발생 합니다.

부동소수점을 연산할 때 0으로 연산한다면 `NaN` 평가가 됩니다.

```java
System.out.println(10 % 0.0); // NaN
System.out.println(10.0 % 0); // NaN
System.out.println(10.0 % 0.0); // NaN
```

## 단항 마이너스 연산 (Unary minus : `-`)

`-` 연산자를 단항 연산자로 사용하면 값의 부정이 수행 됩니다.

```java
int a1 = 1;
System.out.println(-a1); // -1

int a2 = -1;
System.out.println(-a2); // 1
```


# 비트 연산자

비트 연산자(Bitwise Operators) 및 시프트 연산자(Shift Operators)는 개별 비트를 조작하는 저수준(low-level) 연산자 입니다.

비트 연산을 이해하기 위해서는 이진수와 음의 정수를 나타내는 데 사용되는 2의 보수를 이해해야 합니다.

부동소수점, 부울(`bool`), 배열, 객체 등을 피연산자로 사용할 수 없습니다.

비트 연산자를 공부할 때는 디버거를 활용하여 바이너리(binary) 값을 보면 이해가 빨라집니다.

인텔리제이 기준으로는 아래 스크린샷을 참고해주시면 됩니다.

![](/assets/img/posts/2020-11-23-java-livestudy-3week/intellij-binary.png)
*Binary를 클릭하세요!*

## 비트 보수 (Bitwise complement : `~`)

`~` 연산자는 비트 반전 또는 비트 NOT 연산자라고 합니다.

각 비트를 반전시켜 1을 0으로, 0을 1로 변환합니다.

```java
int i = ~1;
byte b = ~1;
```

위 예제에서 `i`와 `b`의 값은 무엇이 들어가 있을까요? 출력을 해보면 둘다 -2가 나옵니다.

다만 디버거를 사용하여 바이너리 값으로 보면 아래와 같이 조금 다르게 나오는 것을 볼 수 있습니다.

이유는 `int`는 32bit이고 byte는 8bit이기 때문입니다.

![](/assets/img/posts/2020-11-23-java-livestudy-3week/bit-complement.png)

## AND (`&`)

`&` 연산자는 두 정수 피연산자를 AND 연산 합니다. (비트곱)

```java
int num1 = 10;
int num2 = 20;

int result1 = num1 & num2; // 0

int num3 = 11;
int num4 = 15;

int result2 = num3 & num4; // 11
```

`result1`과 `result2`의 결과가 왜 0과 11일까요?

비트곱은 두 피연산자의 해당 비트가 모두 1일때만 1, 아니면 0을 리턴하기 때문입니다.

![](/assets/img/posts/2020-11-23-java-livestudy-3week/bit-and-1.png)
![](/assets/img/posts/2020-11-23-java-livestudy-3week/bit-and-2.png)

## OR (`|`)

`|` 연산자는 두 정수 피연산자를 OR 연산 합니다.  (비트합)

```java
int num1 = 10;
int num2 = 20;

int result1 = num1 | num2; // 30 (0b00011110)

int num3 = 11;
int num4 = 15;

int result2 = num3 | num4; // 15 (0b00001111)
```

AND 연산 예제와 다르게 비트를 합한 결과가 나오게 됩니다.

## XOR (`^`)

`^` 연산자는 두 정수 피연산자를 XOR (exclusive OR) 연산을 합니다.

두 피연산자의 해당 비트가 같으면 0, 다르면 1을 리턴합니다.

```java
int num1 = 10;
int num2 = 20;

int result1 = num1 ^ num2; // 30 (0b00011110)

int num3 = 11;
int num4 = 15;

int result2 = num3 ^ num4; // 4 (0b00000100)
```

## 왼쪽 시프트 연산 (`<<`)

`<<` 연산자는 비트를 왼쪽으로 두번째 피연산자로 제시된 비트 수 만큼 이동 시킵니다.

시프트 될 때 기존의 가장 왼쪽 비트는 삭제되고 가장 오른쪽 비트는 0으로 채워 집니다.

```java
int num = 10; // 0b00001010
int result = num << 1; // 0b00010100 (20)
int result2 = num << 2; // 0b00101000 (40)
```

추가적으로 2의 비트 수의 제곱 만큼 곱하는 값이 왼쪽 시프트 연산의 결과가 됩니다.

`result`는 10 * 2 * 1 의 결과와 같고 `result2`는 10 * 2 * 2의 결과와 같습니다.

```java
int z = x << y; // z = x * (2 * y);
```

## 오른쪽 시프트 연산 (`>>`)

`>>` 연산자는 왼쪽 시프트 연산과 반대로 우측으로 두번째 피연산자로 제시된 비트 수 만큼 이동 시킵니다.

가장 오른쪽 비트는 삭제되고 기존의 값이 양수인 경우 가장 왼쪽 비트는 0으로 채워지고 음수인 경우는 1이 채워 집니다.

```java
int num = 10; // 0b00001010
int result = num >> 1; // 0b00000101 (5)
int result2 = num >> 2; // 0b00000010 (2)

int num2 = -10; // 0b11110110
int result3 = num2 >> 1; // 0b11111011 (-5)
int result4 = num2 >> 2; // 0b11111101 (-2)
```

왼쪽 시프트는 곱셈이었지만 오른쪽 시프트는 나눗셈이 됩니다.

```java
int z = x >> y; // z = x / (2 * y);
```

## 부호없는(unsigned) 오른쪽 시프트 연산 (`>>>`)

기본적으로는 오른쪽 시프트 연산과 동일하지만 부호에 관계없이 왼쪽 비트는 무조건 0으로만 채워집니다.

그렇기 때문에 음수에 `>>>` 연산을 한다면 결과는 양수가 됩니다.

```java
int num = 10;
int result = num >>> 1; // 5
int result2 = num >>> 2; // 2

int num2 = -10; // 0b11111111_11111111_11111111_11110110
int result3 = num2 >>> 1; // 0b01111111_11111111_11111111_11111011
int result4 = num2 >>> 2; // 0b00111111_11111111_11111111_11111101
```

`int`는 4 byte 이기 때문에 총 32비트가 채워져 있어 음수를 `>>>` 연산 했을 경우 꽤 큰 값이 나오게 됩니다.


# 관계 연산자

- 같거나 같지 않음을 평가하는 비교 연산자(Comparison Operators)
- 크고 작은 관계를 평가하는 관계 연산자(Relational Operators)
- `boolean` 결과를 도출 하므로 조건문(`if`) 이나 반복문 (`for`, `while`) 등에서 결정을 위해 사용

## Equals (`==`)

`==` 연산자는 Primitive Type를 비교할 때 두 피연산자의 `값`이 같다면 `true`를 그렇지 않다면 `false`를 리턴 합니다.

그러나 `String` 이나 참조 타입의 피연산자를 비교하면 `동일한 객체` 인지 여부를 체크합니다.

```java
int num1 = 10;
int num2 = 10;

num1 == num2 // true

int num3 = 12;
int num4 = 13;

num3 == num4 // false
```

두 피연산자의 타입이 다를 때는 크기가 작은 피연산자 타입이 큰 피연산자의 타입으로 변환되어 비교 됩니다.

```java
int i = 10;
float f = 10.0f;

i == f // true
```

위 예제에서 `i`는 `float` 타입으로 변환된 후 비교 됩니다.

부동소수점의 `NaN`의 경우엔 모든 숫자와 같지 않으며 

`NaN` 여부를 체크하려면 `Float.isNan()`이나 `Double.isNan()`을 사용해야합니다.

## Not Equals (`!=`)

`!=` 연산자는 `==` 연산자와 정반대로 두 피연산자의 `값`이 다른지 여부를 체크하여 `true`/`false`를 리턴 합니다.

`String` 이나 참조 타입일 경우엔 `동일한 객체` 인지 여부를 체크합니다.

```java
int num1 = 10;
int num2 = 10;

num1 != num2 // false

int num3 = 12;
int num4 = 13;

num3 != num4 // true
```

## 작음 (`<`), 작거나 같음 (`<=`)

첫번째 피연산자가 두번째 피연산자보다 작은지 여부를 평가(Evaluate)하려면 `<` 연산자를 사용하고 

같은지 여부까지 평가하려면 `<=` 연산자를 사용합니다.

```java
int num1 = 10;
int num2 = 10;

num1 <= num2 // true

int num3 = 12;
int num4 = 13;

num3 < num4 // true
```

## 큼 (`>`), 크거나 같음 (`>=`)

위 연산자와 정반대로 첫번째 피연산자가 두번째 피연산자보다 큰지 여부를 평가하기 위해 `>` 연산자를 사용하고 

같음 여부도 평가하려면 `>=` 연산자를 사용합니다.

```java
int num1 = 10;
int num2 = 10;

num1 >= num2 // true

int num3 = 12;
int num4 = 13;

num3 > num4 // false
```


# 논리 연산자

Boolean Operators 라고도 하며 관계 연산자와 동일하게 `true`/`false` 결과를 리턴 합니다.

여러 비교 연산을 사용하여 복잡도를 높인 조건식을 만들 때 주로 사용 됩니다.

## 조건부 AND (`&&`)

두 피연산자가 모두 `true`인지 여부를 평가하며 두 피연산자 중 하나라도 `false`라면 `false`를 리턴 합니다.

```java
int num1 = 10;
int num2 = 10;

boolean c1 = num1 >= num2; // true

int num3 = 12;
int num4 = 13;

boolean c2 = num3 > num4; // false

System.out.println(c1 && c2); // false
```

## 조건부 OR (`||`)

두 피연산자 중 하나라도 `true` 라면 `true`를 리턴 합니다.

```java
int num1 = 10;
int num2 = 10;

boolean c1 = num1 >= num2; // true

int num3 = 12;
int num4 = 13;

boolean c2 = num3 > num4; // false

System.out.println(c1 || c2); // true
```

## 단락 회로 평가 (Short Circuit Evaluation)

`&&`와 `||` 연산을 사용할 때 첫 번째 피연산자의 결과에 따라 결과 값이 정해졌을 때 두 번째 피연산자의 평가를 하지 않는 것을 말합니다.

- `&&` : 첫 번째 피연산자의 결과가 `false` 라면 두 번째 피연산자를 평가하지 않고 `false` 리턴
- `||` : 첫 번째 피연산자의 결과가 `true` 라면 두 번째 피연산자를 평가하지 않고 `true` 리턴

```java
public boolean isTrue() {
    System.out.println("isTrue");
    return 10 >= 10;
}

public boolean isFalse() {
    System.out.println("isFalse");
    return 12 > 13;
}
```

위와 같은 메소드가 있고 `&&`, `||` 를 사용하여 비교한다면 피연산자 순서에 따라 출력이 다릅니다.

출력 결과는 아래와 같습니다.

```java
isTrue() && isFalse(); // isTrue, isFalse
isFalse() && isTrue(); // isFalse

isTrue() || isFalse(); // isTrue
isFalse() || isTrue(); // isFalse, isTrue
```

## 부정 연산자 (`!`)

단항 연산자로써 해당 값의 결과의 반대로 평가 합니다.

위 `isTrue`와 `isFalse` 메소드를 대상으로 사용해보면 아래와 같습니다.

```java
!isTrue() // false
!isFalse() // true
```

## AND (`&`), OR (`|`), XOR (`^`)

비트 연산에서 사용되었던 연산자들과 동일하지만 `boolean` 연산을 사용할 때도 이 연산자들을 사용할 수 있습니다.

`&`와 `|`의 경우 `&&`와 `||`의 결과와 동일하지만 단락 회로 평가가 되지 않습니다.

```java
isTrue() & isFalse(); // isTrue, isFalse
isFalse() & isTrue(); // isFalse, isTrue

isTrue() | isFalse(); // isTrue, isFalse
isFalse() | isTrue(); // isFalse, isTrue
```

`^` 연산자는 두 피연산자 중 하나만 `true`라면 `true`를 리턴 합니다. 이 연산자도 위 두 연산자와 동일하게 단락 회로 평가가 되지 않습니다.

```java
isTrue() ^ isFalse(); // true
isFalse() ^ isTrue(); // true
isTrue() ^ isTrue(); // false
isFalse() ^ isFalse(); // false
```


# instanceof

객체(Object) 또는 배열(Array) 값을 어떠한 참조 유형에 맞는 값인지를 평가하는 연산자 입니다.

만약 `null`을 평가한다면 항상 `false`가 리턴 됩니다.

만약 평가 결과가 `true`라면 비교된 참조 유형으로 안전하게 캐스팅하고 할당할 수 있다는 것을 의미합니다.

```java
"sangwoo" instanceof String // true
null instanceof String // false

Object o = new int[]{1, 2, 3};
o instanceof int[] // true

// safety casting
if (obj instanceof MyClass) {
    MyClass c = (MyClass) obj;
}

i instanceof int // compile error : Primitive Type은 사용할 수 없습니다.
```

> 일반적으로 Java 프로그래머 사이에서 `instanceof`를 사용하지 않는 것이 좋습니다.
> 종종 의심스러운 설계의 신호일 수 있습니다.
> 정상적인 상황이라면 `instanceof`의 사용을 피할 수 있지만 필요한 경우도 있긴 합니다.


# 대입 연산자 (Assignment Operator)

어떠한 변수에 값을 할당할 때 이 연산자를 사용할 수 있으며 메모리에 값을 저장하거나 할당한다는 의미 입니다.

```java
int i = 10;
String s = "hello";
```

위와 같이 `i` 변수에 10, `s` 변수에 hello를 할당할 수 있습니다.

이 외에 5개의 산술 연산자와 6개의 비트 연산자 및 시프트 연산자와 할당을 결합하는 다른 연산자도 정의 되어 있습니다.

```java
int num = 10;

// 산술 연산자 결합
num += 2; // num = num + 2
num -= 3; // num = num - 3
num *= 2; // num = num * 2
num /= 2; // num = num / 2
num %= 3; // num = num % 3

// 비트 연산자 결합
num &= 2; // num = num & 2
num |= 3; // num = num | 3
num ^= 4; // num = num ^ 4

// 시프트 연산자 결합
num <<= 3; // num = num << 3
num >>= 4; // num = num >> 4
num >>>= 2; // num = num >>> 2
```


# 화살표 연산자 (Lamda Expression : `->`)

Java 8 부터 도입된 연산자로 람다 표현식(Lamda Expression) 이라고 하며 메소드 본문에 해당 실행 가능한 자바 코드의 익명 컬렉션입니다.

함수형 프로그래밍 언어에서 주로 사용되던 문법이며 유연성이 좋아 모던 프로그래밍 언어에서도 많이 사용합니다.

메소드 파라미터 목록, 연산자, 코드 블럭 순으로 구성 됩니다.

코드 블럭이 한 문장으로 끝난다면 중괄호(`{`, `}`)를 생략할 수 있습니다.

```java
Runnable r = () -> {
    System.out.println("1");
    System.out.println("2");
}

// 코드가 한 문장이면 중괄호 생략 가능
Runnable r = () -> System.out.println("1");
```


# 3항 연산자 (Conditional Operator : `? :`)

3항 연산자는 C언어로 부터 차용한 연산자 입니다.

`if ~ else` 문장을 연산자로 표현할 수 있고 조건에 따라 결과를 반환할 수 있습니다.

조건 ? true일때 결과 : false 일때 결과

```java
public int max(int x, int y) {
    return x > y ? x : y;
}

max(3, 2) // 3
max(4, 5) // 5

public String getDefaultName(String name) {
    return name != null ? name : "unknown";
}

getDefaultName("sangwoo") // sangwoo
getDefaultName(null) // unknown
```

이미 위 예제의 `max` 메소드와 비슷한 역할을 하는 기능이 [Math 클래스](https://docs.oracle.com/javase/8/docs/api/java/lang/Math.html#max-int-int-){:target="_blank"}에 정의 되어 있습니다.


# 연산자 우선 순위

|우선순위	|연산자	|연산 방향	|동작|
|:--------|:-----|:----------|:--|
|1	|`.`	|->	|객체 멤버 접근|
||`[`, `]`	|->	|배열 요소 접근|
||`(args)`	|->	|메소드 호출|
||`data++`, `data--`	|->	|후위 증감|
|2	|`++data`, `--data`	|<-	|전위 증감|
||`+`, `-`	|<-	|단항 증감|
||`~`, `!`	|<-	|비트 보수, 부정 연산|
|3	|`new`	|<-	|객체 생성|
||`(type)`	|<-	|캐스팅|
|4	|`*`, `/`, `%`	|->	|곱하기, 나누기, 나머지|
|5	|`+`, `-`	|->	|더하기, 빼기|
||`+`	|->	|문자열 결합|
|6	|`<<`, `>>`, `>>>`	|->	|왼쪽 시프트, 오른쪽 시프트, 부호없는 오른쪽 시프트|
|7	|`<`, `<=`, `>`, `>=`	|->	|작음, 작거나 같음, 큼, 크거나 같음|
||`instanceof`	|->	|타입 비교|
|8	|`==`, `!=`	|->	|같음, 같지 않음|
|9	|`&`	|->	|AND|
|10	|`^`	|->	|XOR|
|11	|`|`	|->	|OR|
|12	|`&&`	|->	|AND|
|13	|`||`	|->	|OR|
|14	|`? :`	|<-	|3항 연산자|
|15	|`=`, `*=`, `/=`, `%=`, `+=`, `-=`, `<<=`, `>>=`, `>>>=`, `&=`, `^=`, `|=`	|<-	|대입 연산자|
|16	|`->`	|->	|람다 표현식|


# Java 13. `switch` 연산자

- `yield` 키워드를 사용하여 `switch`표현식을 리턴할 수 있습니다.
- `->`를 사용하여 `case` 구문 처리가 가능합니다. (label rules)

```java
int result;

switch (mode) {
    case "a":
        result = 1;
        break;
    case "b":
        result = 2;
        break;
    case "c", "d", "e":
        result = 3;
    case "f", "g":
        System.out.println("this is f or g");
        result = 4;
        break;
    default:
        result = -1;
}
```

위와 같은 `switch` 문을 `yield`를 사용한다면 아래와 같습니다.

```java
int result = switch (mode) {
    case "a":
        yield 1;
    case "b":
        yield 2;
    case "c", "d", "e":
        yield 3;
    case "f", "g":
        System.out.println("this is f or g");
        yield 4;
    default:
        yield -1;
};
```

문법이 간결해지고 `result` 변수의 변화가 없기 때문에 안전하게 코딩할 수 있습니다.

`->`를 사용한다면 조금 더 간단하게 변경할 수 있습니다.

```java
int result = switch (mode) {
    case "a" -> 1;
    case "b" -> 2;
    case "c", "d", "e" -> 3;
    case "f", "g" -> {
        System.out.println("this is f or g");
        yield 4;
    }
    default -> -1;
};
```

`case`문 별로 한 문장으로 리턴이 가능하다면 `yield`까지 생략할 수 있습니다.

두 문장 이상이 된다면 중괄호로 처리하고 `yield`문을 사용해야 합니다.
