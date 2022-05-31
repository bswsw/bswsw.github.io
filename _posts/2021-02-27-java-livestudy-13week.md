---
title: "[Live Study] 13주차 과제: I/O"
author: Bae Sangwoo
date: 2021-02-27 20:17:00 +0900
categories: [Java, Live Study]
tags: [java, livestudy, whiteship, io]
image:
  src: https://user-images.githubusercontent.com/12427330/103539943-44dc6c00-4edc-11eb-8dbb-9d6f3bacf911.png
---


이 스터디는 백기선님께서 [Github](https://github.com/whiteship/live-study){:target="_blank"}와 [유튜브](https://www.youtube.com/user/whiteship2000){:target="_blank"}로 진행하시는 스터디 입니다.

참여하시고 싶으신 분은 아래 링크를 참고해 주세요 :)

대부분의 내용은 [[O'REILLY] Java in a Nutshell, 7th Edition](https://www.oreilly.com/library/view/java-in-a/9781492037248/){:target="_blank"} 에서 참고 하였습니다.

(최대한 직접 해석하면서 읽고 있으며 모르는 단어는 번역기로 찾았습니다.)


# 학습 목표

자바의 Input과 Output에 대해 학습하세요.


# 스트림(Stream), 버퍼(Buffer), 채널(Channel) 기반의 I/O

## 스트림(Stream)

I/O 스트림(Stream)은 디스크 또는 기타 소스의 순차적 바이트 스트림을 처리하는 방법으로 자바1.0부터 존재 했습니다.

스트림은 단방향 통신만 가능하고 그 때문에 입출력을 모두 처리하려면 입력 스트림과 출력 스트림 모두 필요합니다. 

`System.in`, `System.out`은 자주 사용되는 표준 입출력 스트림입니다.

```java
System.out.println("this is stream!");
```
> 대표적인 출력 스트림 입니다.
> 자바8에 추가된 컬렉션 API의 `Stream`과 다릅니다.

## NIO (New I/O)

자바1.4에 추가된 API로 `java.nio` 패키지에 포함되어 있으며 자바7부터는 NIO.2 API가 추가 되었습니다.

NIO는 넌블로킹(Non-Blocking) 처리가 되며 스트림(Stream)이 아닌 채널(Channel)을 사용 합니다.

## 버퍼(Buffer)

`Buffer`는 특정한 원시자료형(Primitive Type)을 저장하는 컨테이너이며 필수 속성은 capacity, limit, position 입니다.

채널을 통해서 소켓, 파일 등에 데이터를 전송하거나 읽어올 때 버퍼를 사용하여 가비지량을 최소화 시킬 수 있으며 GC 회수를 줄여 서버 전체의 처리량(throughput)을 즐가시켜 줍니다.

`ByteBuffer`, `CharBuffer`, `ShortBuffer`, `IntBuffer`, `LongBuffer` 등이 있습니다.

## 채널(Channel)

채널은 데이터가 통과하는 통로이며 이 때 사용되는 것이 버퍼 입니다.

`SocketChannel`, `FileChannel` 등이 있습니다.


# InputStream, OutputStream

`InputStream`과 `OutputStream`은 스트림의 핵심적인 API 입니다.

이 둘은 모두 데이터를 바이트(byte) 단위로 입출력을 하는 스트림 입니다.

다양한 구현체가 있지만 `ByteArrayInputStream`, `ByteArraayOutputStream`으로 예제를 작성 했습니다.

## InputStream

`InputStream`은 입력 스트림으로 데이터를 읽는 역할을 하며 핵심 메소드인 `read()` 메소드를 사용하여 순차적으로 데이터를 읽을 수 있습니다.

```java
// 핵심 메소드
public abstract int read() throws IOException;
public int read(byte b[]) throws IOException;
public int read(byte b[], int off, int len) throws IOException;
```
> 리턴 타입이 `int`인 이유는 0~255까지는 데이터이고 스트림의 끝을 나타내는 데이터는 -1 입니다.

## OutputStream

`OutputStream`은 출력 스트림으로 데이터를 쓰기위해 사용할 수 있습니다.

```java
// 핵심 메소드
public abstract void write(int b) throws IOException;
public void write(byte b[]) throws IOException;
public void write(byte b[], int off, int len) throws IOException;
```


# Byte, Character 스트림

## Byte 스트림

1바이트 단위로 데이터를 처리하는 스트림으로 위 예제가 바이트 스트림의 예제 입니다.

![image](https://user-images.githubusercontent.com/12427330/109421560-3cf00280-7a1b-11eb-98a9-6717a85f27a3.png)
_http://javadevwannabe.blogspot.com/2012/02/high-level-streams.html_

## Character 스트림

바이트가 아닌 문자(Character) 단위로 처리하는 스트림으로 개발자에게 더 친숙하고 인코딩이나 유니코드 등의 문제를 숨긴 API를 제공 합니다.

`Reader`, `Writer` 클래스를 사용하고 다양한 하위 클래스가 존재 합니다.

![image](https://user-images.githubusercontent.com/12427330/109421542-26e24200-7a1b-11eb-9f3d-e14b4faea299.png)
_http://javadevwannabe.blogspot.com/2012/02/reader-and-writer-classes.html_


# 표준 스트림 (System.in, System,out, System.err)

표준스트림은 `java.lang.System` 클래스에 정의되어 있습니다.

- `System.out`: 콘솔에 출력하기 위함
- `System.in`: 키보드로 입력을 받기 위함
- `System.err`: 에러 출력을 위함

# 파일 읽고 쓰기

## InputStream, OutputStream 사용

```java
try (InputStream is = new FileInputStream("input.png");
     OutputStream os = new FileOutputStream("output.png")) {
    int line;
    while ((line = is.read()) > -1) {
        os.write(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

## Reader, Writer 사용

```java
try (BufferedReader in = new BufferedReader(new FileReader("input.txt"));
     BufferedWriter out = new BufferedWriter(new FileWriter("output.txt"))) {
    String line;
    while ((line = in.readLine()) != null) {
        out.write(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```
