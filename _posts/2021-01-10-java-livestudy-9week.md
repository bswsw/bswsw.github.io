---
title: "[Live Study] 9주차 과제: 예외 처리"
author: Bae Sangwoo
date: 2021-01-10 18:05:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, exception]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 예외 처리에 대해 학습하세요.


# Error / Exception

![](https://media.geeksforgeeks.org/wp-content/uploads/Exception-in-java1.png)
_자바 예외의 계층 구조 (출처: https://www.geeksforgeeks.org/exceptions-in-java/)_

> 계층 구조를 보시면 `Error`와 `Exception` 모두 `Throwable`의 서브 클래스인 것을 볼 수 있습니다.

## Error

`Error`는 어플리케이션 내에서 핸들링 할 수 없으므로 예외처리가 불가능하거나 할 수 없는 심각한 문제입니다.

주로 JVM에서 발생되는 것이 많으며 어플리케이션에서 처리를 할 수 없습니다.

자주 보는 에러로는 `OutOfMemoryError`,  `StackOverflowError` 등이 있습니다.

![](https://user-images.githubusercontent.com/12427330/104319271-fb1b0380-5523-11eb-8e49-e8f57c149bee.png)
_IDE에서 Error 클래스를 찾아서 자식 클래스들을 확인할 수 있습니다._

## Exception

`Error`가 어플리케이션에서 핸들링할 수 없었다면 `Exception`은 어플리케이션에서 처리를 할 수 있는 에러들을 얘기하며 `예외`라고 합니다.

`NullPointerException`, `IllegalArgumentException` 등이 이에 해당합니다.


# Checked / Unchecked Exception

위에서 공부한 Exception은 또 다시 Checked Exception과 Unchecked Exception으로 구분 됩니다.

## Checked Exception

Checked Exception은 확인된 예외로 컴파일 시점에 확인되는 예외를 말합니다.

그렇기 때문에 Checked Exception은 명확하고 잘 정의된 특정 상황에 발생하고 어플리케이션이 부분적 또는 전체적으로 복구될 수 있는 상태의 예외 입니다.

```java
InputStream is = new FileInputStream("");
```

이런 코드를 작성한다면 이 상태로는 컴파일이 불가하다는 것을 알 수 있습니다.

저 코드에 처리해야할 확인된 예외가 있기 때문입니다.

```java
try {
    InputStream is = new FileInputStream("");
} catch (FileNotFoundException e) {
    e.printStackTrace();
}
```

간단한 예시 이지만 이렇게 예외 처리를 컴파일 시점에 강제 합니다.

## Unchecked Exception

Checked Exception과 반대로 컴파일 시점에 확인할 수 없는 예외를 말합니다.

모든 Unchecked Exception은 `RuntimeException`을 상속합니다.

```java
List<String> list = new ArrayList<>();
list.get(0);
```

`List`의 `get` 메소드에서 `IndexOutOfBoundsException` 예외가 발생할 수 있습니다.

이 예외는 `RuntimeException`을 상속하고 있고 이름답게 컴파일 시점에 처리를 강제하지 않기 때문에 확인이 어렵고 런타임 시점에 예외를 발생시켜 장애를 유발할 수 있습니다.


# 예외 처리를 하는 이유

```java
private void process(String str) {
    System.out.println("str의 길이 : " + str.length());
}

process(null);
```

위 코드는 컴파일 에러가 나지 않고 실행이 가능하지만 실제로 실행한다면 어떻게 될까요?

`str.length()`를 호출할 때 `NullPointerException`이 발생하여 프로그램이 비정상적으로 종료가 됩니다.

![](https://user-images.githubusercontent.com/12427330/104124211-e2c7af00-5392-11eb-85d4-c74c1f2b7440.png)
_NullPointerException은 Unchecked Exception 입니다._

이렇게 어떠한 예외가 발생했을 때 그에 대응하는 코드를 작성하여 **비정상적인 종료를 방지하고 장애에 대비**할 수 있습니다.

위 예제를 통해 예외 처리하는 방법을 알아보겠습니다.


# try / catch / finally

자바는 기본적인 예외 처리 문(statement)인 `try`/`catch`/`finally` 문을 지원합니다.

## try

`try` 블럭은 예외를 처리할 대상이 되는 코드를 입력하기 위해 사용 됩니다.

`try` 블럭 자체로는 특별한 역할을 하지 않지만 이어서 `catch` 블럭 혹은 `finally` 블럭이 필수로 필요합니다.

기본적인 사용 방법은 아래와 같습니다.

```java
private void process(String str) {
    try {
        System.out.println("str의 길이 : " + str.length());
    }
}
```

> `try` 블럭을 단독으로 사용하면 컴파일 에러가 발생합니다.

![](https://user-images.githubusercontent.com/12427330/104122089-4b5c5f00-5386-11eb-8cb7-33ca91babd33.png)

## catch

`catch` 블럭은 `try` 블럭에서 발생한 예외를 처리하기 위해 사용되며 여러 `catch` 블럭을 사용하여 다양한 예외를 처리할 수 있습니다.

`catch` 블럭이 처리할 수 있는 타입은 `Throwable`의 서브클래스들 입니다.

`try` 블럭에서 예외가 던져지면(throw) Java 인터프리터는 던져진 예외와 동일한 타입 혹은 예외의 슈퍼 클래스가 파라미터로 있는 `catch` 절을 찾습니다.


```java
private void process(String str) {
    try {
        System.out.println("str의 길이 : " + str.length());
    } catch (NullPointerException e) {
        System.out.println("str는 null 입니다.");
    }
}

process(null); // str는 null 입니다.
process("sangwoo"); // str의 길이 : 7
```

`NullPointerException` 예외는 `RuntimeException`의 자식 클래스 입니다.

![](https://user-images.githubusercontent.com/12427330/104124483-81084480-5394-11eb-9932-435cb8d9fbf7.png)

그러므로 `catch` 블럭에서 처리할 예외를 `RuntimeException`으로 변경하여도 동일하게 동작합니다.

```java
private void process(String str) {
    try {
        System.out.println("str의 길이 : " + str.length());
    } catch (RuntimeException e) { // NullPointerException을 RuntimeException으로 변경
        System.out.println("str는 null 입니다.");
    }
}

process(null); // str는 null 입니다.
process("sangwoo"); // str의 길이 : 7
```

여러 예외를 별도로 처리하려면 `catch` 블럭을 여러개 추가하면 됩니다.

만약 `List` 객체에서 요소가 없는 인덱스에 접근하면 `IndexOutOfBoundsException`이 발생합니다.

이 예시를 추가해보면 아래와 같아집니다.

```java
private void process(String str) {
    List<String> list = new ArrayList<>();

    try {
        System.out.println("str의 길이 : " + str.length());
        System.out.println("list.get(1) : " + list.get(1)); // 요소가 없는 인덱스 접근
    } catch (NullPointerException e) {
        System.out.println("str는 null 입니다.");
    } catch (IndexOutOfBoundsException e) {
        System.out.println("IndexOutOfBoundsException이 발생하였습니다.");
    }
}

process(null); // str는 null 입니다.
process("sangwoo"); // str의 길이 : 7, IndexOutOfBoundsException이 발생하였습니다.
```

만약 여러 예외를 공통 처리하려면 하나의 `catch` 블럭으로 처리하면 됩니다.

```java
private void process(String str) {
    List<String> list = new ArrayList<>();

    try {
        System.out.println("str의 길이 : " + str.length());
        System.out.println("list.get(1) : " + list.get(1));
    } catch (NullPointerException | IndexOutOfBoundsException e) { // 여러 예외를 하나의 catch로 처리
        System.out.println("NullPointerException 또는 IndexOutOfBoundsException 발생");
    }
}

process(null); // NullPointerException 또는 IndexOutOfBoundsException 발생
process("sangwoo"); // str의 길이 : 7, NullPointerException 또는 IndexOutOfBoundsException 발생
```

`NullPointerException`, `IndexOutOfBoundsException` 두 예외 모두 `RuntimeException`의 자식 클래스에 해당하므로 아래와 같이 한 번에 처리도 가능합니다.

```java
private void process(String str) {
    List<String> list = new ArrayList<>();

    try {
        System.out.println("str의 길이 : " + str.length());
        System.out.println("list.get(1) : " + list.get(1));
    } catch (RuntimeException e) { // NullPointerException, IndexOutOfBoundsException 둘다 캐치 됨
        System.out.println("RuntimeException 발생");
    }
}

process(null); // RuntimeException 발생
process("sangwoo"); // str의 길이 : 7, RuntimeException 발생
```

## finally

`finally` 블럭은 일반적으로 `try` 블럭의 코드를 정리하는데 사용합니다.

`try` 블럭의 코드의 완료 방식과 상관 없이 `try` 블럭이 일부만 실행되더라도 `finally` 블럭의 실행은 보장됩니다.

즉 중간에 예외 발생으로 `catch` 블럭으로 빠지더라도 `finally` 블럭은 실행됩니다.

```java
InputStream is = null;

try {
    is = new FileInputStream("");
    
    // is 사용하기...
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    is.close();
}
```

> 이렇게 파일을 닫거나 네트워크 연결을 종료할 때 유용합니다.

위 예제처럼 `catch` 블럭이 없더라도 `finally` 블럭이 적용 가능합니다.

```java
private void processFinally() {
    String str = null;
    try {
        str = "여기는 try 블럭";
    } finally {
        str = "여기는 finally 블럭";
    }

    System.out.println(str);
}

processFinally(); // 여기는 finally 블럭
```


# throw

`throw` 문(statement)은 특정 시점에 예외를 던져서 호출한 곳에 예외를 알리는 것 입니다.

던질 수 있는 타입은 `Exception`을 포함한 하위 타입 입니다.

```java
private void printInt(int i) {
    if (i < 5) {
        throw new IllegalArgumentException("i는 5 이상이어야 합니다.");
    }

    System.out.println("i : " + i);
}

printInt(6); // i : 6
printInt(1); // 예외 발생!
```

위 예제에서 `printInt(1)` 문장에서 예외가 발생하고 아래와 같이 로그가 나옵니다.

![](https://user-images.githubusercontent.com/12427330/104300884-5e983780-550a-11eb-95dc-4bcfc10f8a33.png)

이를 처리하기 위해서는 위에서 공부한 것 처럼 `try`/`catch` 블럭을 사용하면 됩니다.

```java
try {
    printInt(1);
} catch (IllegalArgumentException e) {
    System.out.println(e.getMessage());
}

// i는 5 이상이어야 합니다.
```

위 문장을 실행하면 `catch` 블럭으로 빠지고 예외에 정의된 메시지를 가져오는 `e.getMessage()`를 출력하게 되는데 이 에러 메시지는 `printInt()` 메소드에서 에러를 던지는 부분에 정의되어 있습니다.

```java
throw new IllegalArgumentException("i는 5 이상이어야 합니다.");
```

> `IllegalArgumentException` 생성자의 파라미터가 에러 메시지 입니다.


# throws

Checked Exception은 명시적으로 에러를 처리하기를 강제하고 이 예외를 처리하기 위해서 `try`/`catch` 절을 사용할 수 있습니다.

그런데 만약 이 메소드에서 예외 처리를 하지 않고 호출하는 메소드로 예외를 전파시키고 싶다면 사용할 수 있는 것이 바로 `throws` 절 입니다.

메소드 시그니처 끝에 붙일 수 있고 호출하는 메소드로 예외 처리를 전파시켜 떠넘기는 개념으로 볼 수 있습니다.

메소드가 하나 이상의 Checked Exception을 `throw` 하고 있다면 `throws`에 명시해야 합니다.

단, Uncheked Exception의 경우에는 예외 처리가 강제되지 않기 때문에 `throws` 절을 명시하지 않더라도 예외가 전파됩니다.

```java
private void doCheckedException() throws Exception {
    // Exception은 Checked Exception
    throw new Exception();
}

private void processException() {
    doCheckedException();
}
```

위와 같이 작성했다면 `doCheckedException` 부분에서 컴파일 에러가 발생 합니다.

![](https://user-images.githubusercontent.com/12427330/104317304-22240600-5521-11eb-93b9-673f724ca699.png)

이 때 `throws` 절을 사용하여 예외를 전파 시킵니다.

```java
private void processException() throws Exception {
    doCheckedException();
}

private void receive() {
    try {
        processException(); 
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

> `processException`에서 던진 예외를 호출하는 쪽에서 처리하도록 강제합니다.

만약 Unchecked Exception이라면 `throws` 절을 명시하지 않아도 됩니다.

```java
private void doUncheckedException() {
    // RuntimeException은 Unchecked Exception
    throw new RuntimeException();
}

private void processException() {
    doUncheckedException();
}
```

> 단, 예외로 인해 프로그램이 장애가 발생할 수 있는 것은 동일 합니다.


# try-with-resources

만약 리소스를 더이상 사용하지 않아 자원을 정리하거나 닫아야할 때 일반적으로는 `finally` 문에서 `is.close()` 메소드를 사용하는 것처럼 자원을 해제해야 합니다.

```java
InputStream is = null;
try {
    is = new FileInputStream("");

    // is 사용하기...
} catch (IOException e) {
    e.printStackTrace();
} finally {
    is.close();
}
```

### 예제의 문제점

1. 자원 처리를 위해 `InputStream` 객체를 상단에 `null`로 선언이 필요함.
2. 개발자는 잊지말고 `finally`에서 자원해제를 해줘야함. (실수하면 메모리 누수가 발생함.)
3. `is.close()` 메소드는 Checked Exception을 던지고 있기 때문에 예외 처리가 필요함. (주로 `throws`로 처리함)

위 문제들을 쉽게 해결하기 위해 `Java 7` 이상에서 지원하는 문법이 등장했는데 바로 `try-with-resources` (줄여서 `TWR`) 입니다.

`try-with-resources`를 사용한다면 위 예제에 비해 깔끔한 코드를 볼 수 있습니다.

```java
try (InputStream is = new FileInputStream("")) {
    // is 사용하기...
} catch (IOException e) {
    System.out.println("IOException 발생 : " + e.getMessage());
}
```

> 예외 처리는 필요하다면 해주어야 합니다!

`try-with-resources`의 핵심은 `AutoCloseable` 인터페이스 입니다.

이 인터페이스는 `Closable`의 부모 클래스이며 `close()` 메소드를 가지고 있습니다.

이 인터페이스를 통해 리소스는 `try` 블럭으로 스코프가 한정되고 올바른 순서로 자동으로 닫히게 됩니다.


# assert

`assert` 문(statement)는 주로 코드에서 설계를 검증하는 기능을 제공합니다.

`boolean` 타입의 표현식을 만들어 평가할 수 있고 결과가 `false`일 경우 검사가 실패하고 `java.lang.AssertionError`를 발생시킵니다.

```java
assert <검사식> : <에러코드>
```

## assertion 활성화

assert를 사용하기 위해서는 assertion을 활성화시켜야하는데 필요한 옵션은 `-ea` 입니다.

콘솔로 실행할 경우 아래와 같이 옵션을 줄 수 있습니다.

```
java -ea <클래스명>
```

Intellij의 경우엔 아래와 같이 옵션을 줄 수 있습니다.

Edit Configurations - Modify options - Add VM options

![](https://user-images.githubusercontent.com/12427330/104333384-83a1a000-5534-11eb-963c-57d895fb2fe6.png)

## assert 사용

assertion이 활성화되었고 아래와 같이 코드에 `assert` 문이 존재한다면 검사할 수 있습니다.

```java
private void doAssert(int i) {
    assert i > 1;
    System.out.println("i : " + i);
}

doAssert(2); // 정상 통과
doAssert(0); // AssertionError 발생!
```

![](https://user-images.githubusercontent.com/12427330/104334175-63beac00-5535-11eb-875e-8d72f6c385ad.png)
_doAssert(2)는 정상 통과 한 것을 볼 수 있습니다._

만약 에러코드를 추가하여 실행하면 아래와 같습니다.

```java
private void doAssert(int i) {
    assert i > 1 : "i는 1보다 커야함";
    System.out.println("i : " + i);
}

doAssert(0); // AssertionError 발생!
```

![](https://user-images.githubusercontent.com/12427330/104334127-59041700-5535-11eb-9b2f-891f34b0932d.png)
_readable한 에러 코드가 있습니다._


# 예외처리 전략

위에서 알아본 다양한 예외처리를 위한 문법을 사용하여 예외처리 전략에 따라 적용해 볼 수 있습니다.

> 출처 : 토비의 스프링 3.1 Vol.1 4장 예외

## 예외 복구

예외 복구는 문제를 파악하고 해결하여 정상 상태로 돌려놓는 방법 입니다.

어떤 예외로 발생하였을 때 다른 작업 흐름으로 자연스럽게 유도해주는 것입니다.

가장 대표적으로 `try`/`catch`/`finally` 절을 사용할 수 있습니다.

```java
private void printStr(String str) {
    try {
        System.out.println("str length : " + str.length());
    } catch (NullPointerException e) {
        System.out.println("str is null!");
    }
}

printStr("hello"); // 5 (정상 케이스)
printStr(null); // str is null! (예외 처리되어 다른 작업 흐름으로 이어짐)
```

## 예외처리 회피

예외처리 회피는 자신이 직접 예외처리하지 않고 호출하는 메소드로 전파시키는 방법 입니다.

`throws` 문으로 예외를 회피하는 방법과 `catch` 블럭으로 일단 예외를 잡은 후에 로그를 남기고 다시 `throw` 하는 방법이 있습니다.

```java
// throws로 회피하기
public void process() throws SQLException {
    // jdbc 로직...
}

// catch 후 로그 남기고 다시 throw
public void process2() throws SQLException {
    try {
        // jdbc 로직...
    } catch (SQLException e) {
        System.out.println(e.getMessage())
        throw e;
    }
}
```

> Unchecked Exception이라면 `throws` 없이 회피가 가능합니다.


## 예외 전환

예외 전환은 예외 회피와 비슷하다고 볼 수 있지만 발생한 예외를 그대로 넘기지 않고 더 적절한 예외로 전환하여 던지는 특징이 있습니다.

내부에서 발생한 예외가 모호하여 의미가 명확한 예외를 던지기 위해 전환할 수 있습니다.

```java
// 예외의 에러 코드에 따라 분기하여 예외 전환
public void add(User user) throws DuplicateUserIdException, SQLException {
    try {
        // code ..
    } catch (SQLException e) {
        if (e.getErrorCode() == MysqlErrorNumbers.ER_DUP_ENTRY) {
            throw new DuplicateUserIdException();
        }

        throw e;
    }
}
```

이렇게 예외를 전환할 때는 기존 예외를 담아서 중첩 예외로 만드는 것이 좋습니다.

```java
catch (SQLException e) {
    throw new DuplicateUserIdException(e);
}
```

예외를 처리하기 쉽게 포장하기 위해 전환할 수 있습니다.

예를 들어 Checked Exception을 잡아서 Unchecked Exception으로 던져서 해당 메소드를 사용 하는 곳에서 일일히 에러 처리를 할 필요가 없습니다.

```java
private void process() {
    try {
        // code ...
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```


# 커스텀한 예외 만드는 방법

커스텀 예외가 필요한 이유는 다음과 같습니다.

- 비즈니스 로직이나 업무 흐름과 관련된 예외를 만들어 개발자나 사용자가 정확한 문제를 파악하기 더 쉽습니다. (커스텀 Checked Exception으로 예제 코딩)
- 기존 예외 집합의 특정 처리를 제공하기 위해서 커스텀 예외를 만들 수 있습니다. (커스텀 Unchecked Exception으로 예제 코딩)

## Checked Exception 커스텀

`Exception` 클래스를 상속 받아서 간단하게 만들 수 있습니다.

`Exception` 클래스에는 다양한 생성자가 존재하니 요구사항에 맞춰 사용할 수 있습니다.

![](https://user-images.githubusercontent.com/12427330/104459501-279c5180-55f0-11eb-8831-b2e34aab4d64.png)
_`Exception` 클래스의 생성자들_

이렇게 만든 예외를 필요에 따라 광범위한 예외에서 일부분의 경우의 예외로 분리할 수 있습니다.

이 예제는 예외 전환 예제와 비슷합니다.

```java
// 커스텀한 Checked Exception
public class InvalidFileNameException extends Exception {
    public InvalidFileNameException(Throwable e) { // 예외 중첩을 사용하기 위한 생성자
        super(e);
    }
}

// 적용하기
private void processFile(String fileName) throws InvalidFileNameException, FileNotFoundException {
    try {
        new FileInputStream(fileName);
    } catch (FileNotFoundException e) {
        if (fileName.length() < 1) {
            throw new InvalidFileNameException(e);
        }
        throw e;
    }
}
```

> Checked Exception 이기 때문에 throws 절에 입력해줘야 합니다.

또한 예외 전환 뿐만 아니라 일반적인 비즈니스 로직에서 예외를 발생시키고 싶을 때도 사용이 가능합니다.

```java
private void printFileName(String fileName) throws InvalidFileNameException {
    if (fileName.length() < 1) {
        throw new InvalidFileNameException(e);
    }

    System.out.println(fileName);
}
```

## Unchecked Exception 커스텀

Unchecked Exception을 커스텀하려면 `RuntimeException`을 상속해야 합니다.

실제 개발 중에 예외를 커스텀 해야할 일이 있다면 대부분 이 경우에 해당합니다.

만약 한 메소드에서 여러 예외를 발생시키는데 하나의 예외로 처리할 수 있습니다.

```java
// Unchecked Exception 상속
public class AddContentException extends RuntimeException {

    private final String code;
    
    public AddContentException(Throwable cause, String code) {
        super(cause);
        this.code = code;
    }

    public String getCode() {
        return code;
    }
}

// 적용하기
private void addContent() {
    try {
        // logic...........   
    } catch (SomeException1 | SomeException2 e) {
        throw new AddContentException(e, e.getCode());
    } catch (SomeException3 e) {
        throw new AddContentException(e, "SE3");
    }
}
```

> 예제의 SomeException 시리즈는 모두 가상입니다!


# 결론

그동안 결론은 작성하지 않았었는데 이번에는 포스팅이 꽤 길어져 써 봅니다.

완벽한 프로그램은 없다고 생각하기 때문에 언제든지 장애가 발생할 수 있다고 생각합니다.

그럴 때 예외 처리를 잘 한다면 장애에 대응하고 복구하고 개선하는 작업까지 조금 더 수월하게 할 수 있을 것이라고 생각합니다.

모두 열공하세요!
