---
title: "[Live Study] 4주차 과제: 제어문"
author: Bae Sangwoo
date: 2020-12-07 22:57:00 +0900
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

자바가 제공하는 제어문을 학습하세요.


# 선택문

## if / else if / else

### if

`if` 문은 자바가 결정을 내릴 수 있도록 하는 근본적인 제어문 중 하나 입니다.

조건부로 문장(statement)을 실행하기 위해 사용 되므로 조건문이라고 합니다.

```java
public String getName(String name) {
    if (name == null) { // name이 null이라면...
        name = "alan";
    }

    return name;
}

System.out.println(getName("sangwoo")); // sangwoo
System.out.println(getName(null)); // alan
```

### if / else

필요에 따라 `else` 문을 추가하여 조건에 해당하지 않을 때 처리를 할 수 있습니다.

```java
public String getName(String name) {
    if (name == null) {
        name = "alan bae";
    } else { // if 문이 true가 아니라면...
        name = name + " bae";
    }

    return name;
}

System.out.println(getName("sangwoo")); // sangwoo bae
System.out.println(getName(null)); // alan bae
```

### if / else if / else

위와 같이 `if/else`문으로 두 가지 조건(`if`문의 `true` 혹은 `false`)을 처리할 수 있지만

더 많은 조건을 평가하고 싶다면 `else if`를 `if`와 `else`의 사이에 추가하여 처리할 수 있습니다.

```java
public String intToString(int n) {
    if (n == 0) {
        return "zero";
    } else if (n == 1) {
        return "one";
    } else if (n == 2) {
        return "two";
    } else { // n이 0도 1도 2도 아니라면...
        return "other";
    }
}

System.out.println(intToString(0)); // zero
System.out.println(intToString(1)); // one
System.out.println(intToString(2)); // two
System.out.println(intToString(3)); // other
System.out.println(intToString(10)); // other
```

더불어 `if/else if/else`문 블록 안에서 바로 반환(`return`)이 가능한 것을 볼 수 있습니다.

### if 중첩

`if`문 안에 추가적인 `if`문을 추가할 수 있습니다. 당연히 `else if`문이나 `else`문 에도 가능합니다.

다만 중첩하는 것은 코드를 읽기 어렵게 만들기 때문에 불가피한 경우가 아니라면 지양하는 것이 좋다고 생각 합니다.

```java
public String intToString(int n) {
    if (n == 0) {
        return "zero";
    } else if (n == 1) {
        return "one";
    } else if (n == 2) {
        return "two";
    } else {
        if (n < 10) { // n이 0,1,2가 아니면서 10 이하라면...
            return "less then ten";
        }

        return "other";
    }
}

System.out.println(intToString(0)); // zero
System.out.println(intToString(1)); // one
System.out.println(intToString(8)); // less then ten
```

## switch

`switch`문을 사용한다면 어떤 한 조건에 대해서 결과를 다양하게 분기할 수 있습니다.

동일한 조건으로 `if / else if / else`문으로도 표현이 가능하지만 가독성이 더 떨어질 수 있기 때문에

`switch`를 사용하는 것을 고려해볼 수 있습니다.

조건으로 사용할 수 있는 타입은 `int`, `short`, `char`, `byte`, `String`, `enum`이 가능하며

해당하는 primitive type의 wrapper type도 사용 가능합니다.

```java
public String intToString2(int n) {
    String result = "";
    
    switch (n) {
        case 0: // n이 0일때
            result = "zero";
            break;
        case 1: // n이 1일때
            result = "one";
            break;
        case 2: // n이 2일때
            result = "two";
            break;
        default: // 기본값
            result = "other";
            break;
    }
    
    return result;
}

System.out.println(intToString2(0)); // zero
System.out.println(intToString2(1)); // one
System.out.println(intToString2(8)); // other
```

위와 같이 사용할 수 있으며 `case` 구문 마다 `break`를 걸어주지 않으면 다음 `case` 식을 실행합니다.

`default` 키워드는 모든 `case`에 해당하지 않는 기본 값을 의미하며 `if`문의 `else`와 유사합니다.

또한 `case`문에서 바로 반환이 가능 합니다.

```java
public String intToString3(int n) {
    String result = "";

    switch (n) {
        case 0:
            result = "zero"; // break가 없어 통과
        case 1: // 결국 0 or 1과 동일함
            result = "one";
            break;
        case 2:
            return "two"; // two 리턴
        case 3:
        case 4:
            result = "three or four";
            break;
        default:
            result = "other";
            break;
    }

    return result;
}

System.out.println(intToString3(0)); // one
System.out.println(intToString3(1)); // one
System.out.println(intToString3(3)); // three or four
```


# 반복문

## while

`while`문은 자바에서 반복적인 액션을 수해할 수 있게 해주는 기본적인 문장(statement) 이며

자바의 다양한 루프(loop) 구조 중 하나 입니다.

조건이 거짓일 경우 `while`문을 건너 뛰며 참일 경우 블록 안의 내용이 실행 되며 거짓이 될때까지 반복합니다.

그러한 특징을 살려서 무한 루프를 만들기가 용이 합니다.

```java
int count = 0;
while (count < 10) { // count가 10보다 작으면 실행
    System.out.println(count); // 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
    count++;
}

while (true) { // 무조건 true 이므로 무한 루프
    System.out.println("this is infinity loop"); // 계속 출력...
}
```

## do / while

`do/while`문은 위의 `while`문과 유사하지만 문장과 조건식이 반대 입니다.

`while`은 조건 판단 후 문장을 실행하지만 `do/while`은 무조건 한번 문장을 실행 후 조건을 판단하여 반복 합니다.

```java
int count = 0;
do { // 무조건 한 번 실행
    System.out.println(count); // 0
    count++;
} while (count < 1); // count가 1이 되어 반복문 종료
```

## for

`for` 문은 일반적인 루프(loop) 패턴이며 `while`문 이나 `do`문에 비해 사용하기 편리합니다.

그래서 자바에서는 반복적인 작업을 할때는 주로 `for`문을 사용하게 될 것 입니다.

```java
for (<초기값>; <조건>; <증감>) {
  <문장>
}
```

문법은 위와 같으며 간단한 예제로는 아래와 같습니다.

```java
for (int i = 0; i < 5; i++) {
  System.out.println(i); // 0, 1, 2, 3, 4
}
```

- 초기값 : i는 0 (`int i = 0`)
- 조건 : i가 5미만일때 (`i < 5`)
- 증감 : i를 1씩 증가 (`i++`)

위 예제에서는 조건으로 사용되는 변수를 `i` 하나만 사용했지만 2개 이상도 사용 가능합니다.

```java
for (int i = 0, j = 5; i < 5; i++, j--) {
  System.out.println("(" + i + "," + j + ")"); // (0,5) (1,4) (2,3) (3,2) (4,1)
}
```

## foreach

`foreach`문은 우리나라 말로 `향상된 for문`이라고도 불립니다.

`for`문은 원시 타입(primitive type)을 사용하여 조건을 처리하지만 (counter)

`foreach`문은 배열이나 컬렉션을 사용하고 요소를 하나씩 반복하여 처리할 때 주로 사용 됩니다.

```java
int[] nums = new int[]{1, 2, 3, 4, 5};
for (int num : nums) { // nums의 요소를 하나씩 num에 대입합니다.
    System.out.println(num); // 1, 2, 3, 4, 5
}
```

다만 `foreach`를 사용하게 되면 반복자(counter)가 없기 때문에 현재 어느 위치인지 알기가 쉽지 않습니다.

`foreach`를 사용한다면 할 수 없는 작업들은 아래와 같습니다.

- 배열 또는 컬렉션의 요소를 거꾸로 반복 할 수 없습니다.
- 반복자를 사용할수 없기 때문에 여러 배열의 동일한 위치의 요소를 가져올 수 없습니다.
- 컬렉션의 요소를 `get()` 메소드를 사용하여 가져올 수 없습니다.

## break

만약 `while`, `do/while`, `for`, `switch`문을 사용할 때 어느 조건에 따라 문장을 종료하고 싶다면 `break` 키워드를 사용할 수 있습니다.

```java
for (int i = 0; i < 5; i++) {
    if (i == 3) {
        break; // i가 3이면 for문을 종료
    }

    System.out.println(i); // 0, 1, 2
}
```

## continue

`while`, `do/while`, `for` 반목문을 사용할 때 `continue`를 사용한다면 해당 조건은 넘어가고 다음 조건을 실행 시킬 수 있습니다.

```java
for (int i = 0; i < 5; i++) {
    if (i == 3) {
        continue; // i가 3이면 다음 조건으로 넘어가기
    }

    System.out.println(i); // 0, 1, 2, 4
}
```
