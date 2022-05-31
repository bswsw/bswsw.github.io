---
title: "[Live Study] 10주차 과제: 멀티스레드 프로그래밍"
author: Bae Sangwoo
date: 2021-01-24 10:05:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, thread]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 멀티스레드 프로그래밍에 대해 학습하세요.


# 프로세스와 스레드

![](https://user-images.githubusercontent.com/12427330/109407962-86633200-79c8-11eb-9609-78e2d523d64a.png)
_https://www.javaquery.com/2016/07/what-is-difference-between-process-and.html_

## 프로세스

프로세스는 자체적으로 실행 환경을 가지고 있으며 프로그램의 인스턴스를 프로세스라고 말합니다.

- 각 프로세스에는 자체 주소 공간이 있습니다.
- 각 프로세스는 다른 프로세스와 통신하기 위해 파이프 및 소켓과 같은 IPC(Inter Process Communication) 리소스를 사용해야 합니다.
- 새 프로세스에는 상위 프로세스의 복제가 피요합니다.
- 프로세스에는 상당한 오버 헤드가 있습니다.
- 프로세스에는 상위 프로세스의 데이터 세그먼트에 대한 자체 사본이 있습니다.
- 프로세스는 하위 프로세스를 제어 합니다.
- 상위 프로세스의 변경 사항은 하위 프로세스에 영향을 주지 않습니다.
- 프로세스는 운영 체제에 의해 제어됩니다.
- 프로세스는 독립적 입니다.

## 스레드

스레드는 프로세스 내에서 실행되는 작업의 단위로 경량 프로세스라고 불리기도 합니다.

- 스레드는 자신을 생성한 프로세스의 주소 공간을 공유 합니다.
- 스레드는 프로세스의 다른 스레드와 직접 통신할 수 있습니다.
- 새 스레드를 쉽게 만들 수 있습니다.
- 스레드에는 오버 헤드가 거의 없습니다.
- 스레드는 프로세스의 데이터 세그먼트에 직접 엑세스할 수 있습니다.
- 스레드는 동일한 프로세스의 스레드를 상당히 제어 합니다.
- 주 스레드의 변경 (취소, 우선순위 변경 등)은 프로세스의 다른 스레드 동작에 영향을 미칠 수 있습니다.
- 스레드는 프로그램의 프로그래머가 제어합니다.
- 스레드는 종속적입니다.


# Thread 클래스와 Runnable 인터페이스

`Thread` 클래스와 `Runnable` 인터페이스 모두 새로운 쓰레드를 생성하는데 사용할 수 있습니다.

`Thread` 클래스는 `Runnable` 인터페이스의 구현체입니다.

```java
// Thread 상속
public class MyThread extends Thread {

    @SneakyThrows
    @Override
    public void run() {
        Thread.sleep(1000);
        System.out.println("MyThread run");
    }
}

// Runnable 구현
public class MyRunnable implements Runnable {

    @SneakyThrows
    @Override
    public void run() {
        Thread.sleep(1000);
        System.out.println("MyRunnable run");
    }
}

// 실행
public class MultiThreadApp {

    public static void main(String[] args) {
        MyThread thread = new MyThread();

        MyRunnable runnable = new MyRunnable();
        Thread thread2 = new Thread(runnable);

        thread.start();
        thread2.start();
    }
}
```

위 예제처럼 `Thread`를 사용하는게 더 간단하지만 다른 클래스를 상속할 수 없기 때문에 `Runnable`를 구현해서 사용할 수도 있습니다.


# 스레드의 상태 (라이프사이클)

![](https://user-images.githubusercontent.com/12427330/109408873-f5dd1f80-79d0-11eb-928f-41cc881ba6a5.png)
_[O'REILLY] Java in a Nutshell, 7th Edition_ 

모든 운영체제는 스레드에 대한 뷰가 있습니다. 세부 사항이 다를 수는 있지만 대부분의 경우 높은 수준에서 대체로 유사 합니다.

자바는 이러한 세부 정보를 추상화 하기 위해 스레드 상태를 나타내는 `Thread.State`라는 Enum을 제공합니다.

- `NEW`: 스레드가 생성되었지만 `start()` 메소드가 호출되지 않은 상태로 모든 스레드는 이 상태에서 시작.

- `RUNNABLE`: 쓰레드가 실행중이거나 운영체제가 예약하여 실행이 가능할 때.

- `BLOCKED`: 동기화된(synchronized) 메소드 또는 블록에 들어갈 수 있도록 락을 얻으려고(aquire) 대기 중이기 때문에 스레드가 샐행중이 아닌 상태.

- `WAITING`: `Object.wait()`, `Thread.join()` 메소드가 호출되어 스레드가 실행되고 있지 않는 상태.

- `TIMED_WAITING`: `Thread.sleep()`를 호출했거나 타임아웃을 사용하여 `Object.wait()`, `Thread.join()`을 호출했을 때 스레드가 실행되고 있지 않는 상태.

- `TERMINATED`: `run()` 메소드가 정상적으로 종료되었거나 예외가 발생하여 스레드가 실행이 완료된 상태.


# 스레드의 우선순위

`Thread` 클래스의 `getPriority()`, `setPriority()` 메소드를 사용하여 스레드의 우선순위를 컨트롤할 수 있습니다.

스케쥴러는 스레드 우선순위를 처리하는 방법을 결정합니다.

우선 순위가 높은 스레드가 대기중인 동안 낮은 우선 순위 스레드가 실행되지 않도록하는 전략이 있습니다.

대부분의 경우 스케쥴러가 우선 순위를 해석하는 방법에 영향을 줄 수 없습니다.

스레드 우선 순위는 1 ~ 10 사이의 정수로 표시되며 10이 가장 높습니다.

```java
public class FirstThread extends Thread {

    @Override
    public void run() {
        System.out.println("FirstThread Priority : " + getPriority());
    }
}

public class SecondThread extends Thread {
    
    @Override
    public void run() {
        System.out.println("SecondThread Priority : " + getPriority());
    }
}

public class MultiThreadApp {

    public static void main(String[] args) {
        FirstThread firstThread = new FirstThread();
        SecondThread secondThread = new SecondThread();

        firstThread.setPriority(1);
        secondThread.setPriority(10);

        firstThread.start();
        secondThread.start();
    }
}
```

```
SecondThread Priority : 10
FirstThread Priority : 1
```

> 실제 출력 순서는 다를 수 있습니다.


# 메인 스레드

![](https://user-images.githubusercontent.com/12427330/109409499-9aae2b80-79d6-11eb-90c3-ff5542ae426b.png)
_https://www.geeksforgeeks.org/main-thread-java/_

자바 프로그램이 시작되면 하나의 스레드가 즉시 실행되며 이렇게 프로그램이 시작될 때 실행되는 스레드를 일반적으로 메인 스레드라고 합니다.

```java
public class MultiThreadApp {

    public static void main(String[] args) {
        // ...
    }
}
```

> 사용하던 예제의 메인스레드

## 스레드 그룹 (Thread Group)

`ThreadGroup` 클래스를 사용하여 스레드 그룹을 만들 수 있으며 여러 스레드를 그룹 단위로 관리하는 방법을 제공합니다.

특히 여러 스레드를 일시 중단하거나 재개할 때 유용합니다.

```java
public class FirstThread extends Thread {

    // 스레드 그룹을 위한 생성자 추가
    public FirstThread(@Nullable ThreadGroup group, @NotNull String threadName) {
        super(group, threadName);
    }

    @Override
    public void run() {
        System.out.println("FirstThread Priority : " + getPriority());
    }
}

public class SecondThread extends Thread {

    // 스레드 그룹을 위한 생성자 추가
    public SecondThread(@Nullable ThreadGroup group, @NotNull String threadName) {
        super(group, threadName);
    }

    @Override
    public void run() {
        System.out.println("SecondThread Priority : " + getPriority());
    }
}

public class MultiThreadApp {

    public static void main(String[] args) {
        ThreadGroup threadGroup = new ThreadGroup("thread-group"); // 스레드 그룹 생성

        FirstThread firstThread = new FirstThread(threadGroup, "first-thread");
        SecondThread secondThread = new SecondThread(threadGroup, "second-thread");

        firstThread.start();
        secondThread.start();

        System.out.println("threadGroup.activeCount() : " + threadGroup.activeCount());
    }
}
```

```
FirstThread Priority : 5
SecondThread Priority : 5
threadGroup.activeCount() : 2
```

## 데몬 스레드 (Daemon Thread)

데몬 스레드는 메인 스레드를 보조하는 스레드를 말하며 가비지 수집과 같은 작업을 수행하기 위해 백그라운드에서 실행되는 우선 순위가 낮은 스레드 입니다.

JVM은 데몬 스레드가 실행 중인지 여부를 신경쓰지 않습니다.


# 동기화 (synchronized)

멀티 스레드 프로그램은 여러 스레드가 동일한 리소스에 접근하려고 할 때 예기치 않는 결과를 생성하게 될 수도 있습니다.

따라서 필요에 따라 특정 시점에 하나의 스레드만 리소스에 접근할 수 있도록 처리해야 합니다.

자바는 동기화된 블록을 사용하여 스레드를 생성하고 작업을 동기화하는 방법을 제공하는데 이때 `synchronized` 키워드를 사용할 수 있습니다.

```java
/**
 * 출금만 가능한 계좌
 */
public class Account {

    private int balance;

    public Account(int money) {
        this.balance = money;
    }

    private boolean hasBalance() {
        return balance > 0;
    }

    public void withdraw(int money) {
        if (!hasBalance()) {
            System.out.println("Insufficient balance");
            return;
        }

        balance -= money;
        System.out.println("balance is : " + balance);
    }
}

/**
 * 100원씩 출금하는 스레드
 */
public class AccountWithdrawThread extends Thread {

    private final Account account;

    public AccountWithdrawThread(Account account) {
        this.account = account;
    }

    @Override
    public void run() {
        account.withdraw(100);
    }
}

public class AccountApp {
    public static void main(String[] args) {
        Account account = new Account(1000);

        // 스레드 여러개 만들어서 실행하기
        IntStream.range(0, 15).forEach(i -> new AccountWithdrawThread(account).start());
    }
}
```

```
Insufficient balance
Insufficient balance
Insufficient balance
Insufficient balance
Insufficient balance
balance is : 0
balance is : 600
balance is : 900
balance is : 500
balance is : 300
balance is : 100
balance is : 700
balance is : 400
balance is : 200
balance is : 800
```

위 예제에서 보면 출력 순서가 엉망인 것을 볼 수 있습니다.

이 상태에서 `Account` 클래스의 `withdraw` 메소드에 `synchronized` 키워드를 붙여주면 동시접근이 되지 않기 때문에 순차적으로 출력이 되는 것을 볼 수 있습니다.

```java
/**
 * 출금만 가능한 계좌
 */
public class Account {

    // ...

    public synchronized void withdraw(int money) {
        // ...
    }
}
```

```
balance is : 900
balance is : 800
balance is : 700
balance is : 600
balance is : 500
balance is : 400
balance is : 300
balance is : 200
balance is : 100
balance is : 0
Insufficient balance
Insufficient balance
Insufficient balance
Insufficient balance
Insufficient balance
```

# 데드락 (Deadlock)

![image](https://user-images.githubusercontent.com/12427330/109411145-3fcf0100-79e3-11eb-82c0-7e59681396fb.png)
_https://www.geeksforgeeks.org/deadlock-in-java-multithreading/_

데드락(Deadlock) 또는 교착상태란 최소 두개의 스레드가 다른 리소스에 대해 락(lock)을 유지하고 있고 둘 다 다른 리소스가 작업을 완료할 때까지 기다리는 상황입니다.

그리고 어느 누구도 자신이 보유하고 있는 리소스에 대한 잠금을 해제할 수 없는 상태입니다.

## 데드락 발생 조건

아래 조건이 동시에 성립하는 경우에 데드락이 발생 합니다.

즉, 하나의 조건이라도 불충족해야 데드락이 발생하지 않습니다.

- 상호 배제(Mutual Exclusion): 자원은 한 번에 한 프로세스만이 사용할 수 있어야 한다.
- 점유 대기(Hold and Wait): 자원을 점유하면서도 다른 프로세스가 사용하고 있는 자원에 대해대기
- 비선점(No Preemption): 다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 뺏을 수 없음.
- 순환 대기(Circular Wait): 락 자원 획득이 T1(A -> B), T2(B -> C), T3(C -> D), T4(D -> A) 로 되어 있고, 서로가 물려 있는 상태를 뜻함.
