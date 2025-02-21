---
title: 08장. 분산 시스템의 골칫거리
author: Bae Sangwoo
date: 2025-02-09 13:00:00 +0900
categories: [ Study, 데이터 중심 어플리케이션 설계 ]
tags: [ study, data, design, architecture ]
image:
  src: https://wikibook.co.kr/images/cover/l/9791158393601.jpg
---


> 어떤 것이든지 잘못될 가능성이 있다면 잘못된다.

## 0. 분산 시스템의 골칫거리

- 분산 시스템을 다루는 것은 한 컴퓨터에서 실행되는 소프트웨어를 작성하는 일과는 근본적으로 다르다.
- 엔지니어로서의 우리의 임무는 모든게 잘못되더라도 사용자가 기대하는 보장을 만족시키는 제 역할을 해내는 시스템을 구축하는 것이다.

## 1. 결함과 부분 장애

- 컴퓨터에 내부 결함이 발생하면 잘못된 결과를 반환하기보다는 완전히 동작하지 않기를 원한다.
- 단일 컴퓨터 기준
  - 하드웨어가 올바르게 동작하면 같은 연산은 항상 같은 결과를 낸다. (결정적이다.)
  - 하드웨어 문제가 있으면 보통 시스템이 완전히 실패하는 결과를 낳는다.
- 분산 시스템 기준
  - 네트워크 문제, 부분적인 실패, 타이밍 문제 등 다양한 문제가 발생할 수 있다.
  - 부분 장애는 비결정적이다.
  - 이러한 문제들은 예측하기 어렵고, 디버깅하기도 어렵다.

### 1.1. 클라우드 컴퓨팅과 슈퍼컴퓨팅

- 슈퍼 컴퓨팅
  - 슈퍼컴퓨터에서 실행되는 작업은 보통 가끔씩 계산 상태를 지속성 있는 저장소에 체크포인트로 저장한다.
  - 노드 하나에 장애가 발생했을 때 흔한 해결책은 그냥 전체 클러스터 작업부하를 중단하는 것이다.
  - 슈퍼컴퓨터는 부분 장애를 전체 장애로 확대하는 방법으로 처리하는데 시스템의 어느 부분에 장애가 발생하면 그냥 전체가 죽게 한다.
- **분산 시스템이 동작하게 만들려면 부분 장애 가능성을 받아들이고 소프트웨어에 내결함성 메커니즘을 넣어야 한다.**
- 올바르게 동작하던 시스템도 시간이 지나면 어떤 부분에 결함이 생길 것이고 소프트웨어는 어떤 식으로든 그 결함을 처리해야 한다.

## 2. 신뢰성 없는 네트워크

- 분산 시스템은 비공유 시스템, 즉 네트워크로 연결된 다수의 장비다.
- 인터넷과 데이터센터 내부 네트워크 대부분은 비동기 패킷 네트워크다.
- 노드는 다른 노드로 패킷을 보낼 수 있지만 도착을 보장하지 않고 요청을 보내고 응답을 기다리는 사이에 여러 잘못이 생길 수 있다.
- 이런 문제를 다루는 가장 흔한 방법은 타임아웃이다.

### 2.1. 현실의 네트워크 결함

- 네트워크 문제는 놀랄 만큼 흔하다.
- 중간 규모의 데이터센터에서 매달 12번의 네트워크 결함이 발생함을 발견했다.
- EC2 같은 공개 클라우드 서비스는 일시적으로 네트워크 결함이 자주 발생하는 것으로 알려져 있다.
- 소프트웨어는 예측하지 못하는 상황이지만 네트워크 결함을 반드시 견뎌내도록 처리할 필요는 없다.
- 네트워크 문제에 어떻게 반응하는지 알고 시스템이 그로부터 복구할 수 있도록 보장해야 한다.
- 고의로 네트워크 문제를 유발하여 시스템의 반응을 테스트해보는 것도 좋다. (카오스 몽키)

### 2.2. 결함 감지

- 결함을 자동으로 감지할 수 있어야 한다.
- 네트워크는 불확실성이 있기 때문에 노드가 동작 중인지 아닌지 구별하기 어렵다.
- TCP가 패킷이 전달됐다는 확인 응답을 했더라도 애플리케이션이 그것을 처리하기 전에 죽을 수도 있다.
- 요청이 성공했음을 확신하고 싶다면 애플리케이션 자체로부터 긍정 응답을 받아야 한다.

### 2.3. 타임아웃과 기약 없는 지연

- 타임아웃이 길면 노드가 죽었다고 선언될 때까지 기다리는 시간이 길어진다.
- 타임아웃이 짧으면 결함을 빨리 발견하지만 노드가 일시적으로 느려졌을 뿐인데도 죽었다고 잘못 선언할 위험이 높아진다.
- 빠른 타임아웃은 다른 노드로 역할을 넘겨주게 되고 해당 요청이 중복되어 실행될 수도 있다.
- 역할이 다른 노드로 전달되어 다른 노드와 네트워크에 추가적인 부하를 준다.
- 비동기 네트워크는 기약 없는 지연(unbounded delay)이 있다.

#### 2.3.1. 네트워크 혼잡과 큐 대기

- 네트워크 링크가 붐비면 패킷은 슬롯을 얻을 수 있을 때까지 잠시 기다려야할 수도 있는데 이를 네트워크 혼잡(Network Congestion)이라고 부른다.
- 패킷이 목적지에 도착했을 때 모든 CPU가 바쁜 상태라면 운영체제 큐에 넣어둔다.
- TCP는 혼잡 회피, 배압이라고 하는 흐름 제어를 수행하여 데이터가 네트워크로 들어가기 전에 송신을 제어하는 부가적인 큐 대기를 할 수 있다.
- TCP는 타임아웃이 발생하면 손실로 간주하고 자동으로 재전송하는데 애플리케이션에서는 패킷의 손실과 재전송이 보이지 않지만 지연은 확인할 수 있다.
- 다양한 요인으로 네트워크 지연의 변동성에 영향을 준다.
- 이런 환경에서는 실험적으로 타임아웃을 선택하는 수 밖에 없다.
- 고정된 타임아웃을 설정하는 대신 시스템이 지속적으로 응답 시간과 그들의 변동성을 측정하고 관찰된 응답 시간 분포에 따라 타임아웃을 자동으로 조절하게 하는 방법이 있다.

### 2.4. 동기 네트워크 대 비동기 네트워크

- 전화 통화는 회선(circuit)을 만들어 고정되고 보장된 양의 대역폭이 할당된다. (동기식 네트워크)
- 큐 대기가 없으므로 네트어워크 종단 지연 시간의 최대치가 고정돼 있다. - 제한 있는 지연 (bounded delay)
- TCP 연결의 패킷은 가용한 네트워크 대역폭을 기회주의적으로 사용한다. (비동기식 네트워크)
- 웹 페이지 요청, 이메일/파일 전송 처럼 순간적으로 몰리는 트래픽의 경우 TCP 패킷 교환이 적절하다.
- 네트워크 혼잡, 큐 대기, 기약 없는 지연이 발생할 것이라고 가정하고 타임아웃의 올바른 값은 실험을 통해 결정해야 한다.

## 3. 신뢰성 없는 시계

### 3.1. 단조 시계 대 일 기준 시계

### 3.2. 시계 동기화와 정확도

### 3.3. 동기화된 시계에 의존하기

### 3.4. 프로세스 중단

## 4. 지식, 진실, 그리고 거짓말

### 4.1. 진실은 다수결로 결정된다

### 4.2. 비잔틴 결함

### 4.3. 시스템 모델과 현실
