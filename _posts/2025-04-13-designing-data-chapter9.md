---
title: 09장. 일관성과 합의
author: Bae Sangwoo
date: 2025-04-13 19:00:00 +0900
categories: [ Study, 데이터 중심 어플리케이션 설계 ]
tags: [ study, data, design, architecture ]
image:
  src: https://wikibook.co.kr/images/cover/l/9791158393601.jpg
---

## 0. 일관성과 합의

- 애플리케이션은 다양한 결함이 발생 가능하다.
  - 네트워크 패킷 손실
  - 타임아웃으로 인한 중복 수신
  - 네트워크 지연 발생 및 순서 변경
  - 정확하지 않은 시간
  - 노드가 멈추거나 죽는 상황
- 내결함성을 지닌 시스템을 구축하는 가장 좋은 방법은 유용한 보장을 해주는 범용 추상화를 찾아 이를 구현하고 애플리케이션에서 이 보장에 의존하게 하는 것이다.
  - 트랜잭션
    - 애플리케이셔션은 충돌이 없음 (원자성)
    - 데이터베이스에 동시에 접근하지 않음 (격리성)
    - 데이터를 믿을 수 있음 (지속성)
- 분산 시스템에 가장 중요한 추상화 중 하나는 **합의**, 모든 노드가 어떤 것에 동의하게 만드는 것이다.
  - 단일 리더 복제 데이터베이스에서 새 리더 선출 과정 - 스플릿 브레인 회피

## 1. 일관성 보장

- 복제 데이터베이스는 대부분 **최종적 일관성**을 제공한다.
  - 모든 업데이트가 완료되면 모든 노드에 동일한 데이터가 저장된다.
  - 언제 복제본이 최종적인지 알 수 없기 때문에 보장이 매우 약하다.
  - 약한 보장만 제공하는 데이터베이스를 다룰 때는 그 제한을 계속 알아야 하고 뜻하지 않게 너무 많은 것을 가정하면 안 된다.
  - 약한 보장의 문제는 결함이 있거나 동시성이 높을 때 드러나며 테스트로 발견하기 힘든 버그가 발생 할 수 있다.
- 성능이 떨어지고 내결함성이 비교적 약하지만 데이터를 올바르게 사용하기 위해서 강한 보장이 필요할 수 있다.

## 2. 선형성

- 원자적 일관성, 강한 일관성, 즉각 일관성, 외부 일관성
- 데이터 복사본이 하나만 있고 그 데이터를 대상으로 수행하는 모든 연산은 원자적인 것처럼 보이게 만들어 애플리케이션이 신경 쓸 필요가 없다.
  - 읽은 데이터가 최근 갱신 데이터이고 뒤처진 캐시나 복제보에서 나온 데이터가 아니라고 보장해준다는 뜻 - 최신성 보장
- 비선형성 시스템 예시 (p323): 리더의 데이터가 여러 팔로워에 반영하는데 시간 차가 발생하고 클라이언트는 각각 다른 팔로워를 바라본다.

### 2.1. 시스템에 선형성을 부여하는 것은 무엇인가?

- 선형성 예제 (p324-236)
  - 쓰기가 처리되는 동안에는 읽기 요청은 old, new 데이터를 반환할 수 있다. - 완전하지 않은 선형성
  - 쓰기가 처리되는 동안에 읽기 요청이 new 데이터를 반환했다면 후속 읽기 요청은 new 데이터를 반환해야 한다. - 완전한 선형성
  - 원자적 compare-and-set 연산을 추가
- 선형성 대 직렬성
  - 직렬성: 트랜잭션들의 격리 속성. 트랜잭션들이 순서에 따라 실행되는 것처럼 보장한다.
  - 선형성: 레지스터에 실행되는 읽기와 쓰기에 대한 최신성 보장이다. 쓰기 스큐 문제를 막지 못한다.
  - 둘다 제공하면 엄격한 직렬성, 강한 단일 복사본 직렬성이라고 한다.

### 2.2. 선형성에 기대기

#### 2.2.1. 잠금과 리더 선출

- 단일 리더 복제를 사용하는 시스템은 리더가 진짜로 하나만 존재하도록 보장해야한다.
- 리더를 선출하기 위해 잠금을 사용하여 잠금 획득을 성공한 노드가 리더가 된다.
- 주키퍼나 etcd 같은 코디네이터를 사용하여 분산 잠금과 리더 선출을 구현한다.
- 잠금과 리더 선출을 구현할 대 펜싱 문제 같은게 발생할 수 있다.
- 코디네이터는 합의 알고리즘을 사용해 선형성 연산을 내결함성이 있는 방식으로 구현한다.

#### 2.2.2. 제약 조건과 유일성 보장

- 유일성 제약 조건을 보장하기 위해 선형성이 필요하다.
- 잠금과 비슷하며 원자적 compare-and-set 연산과도 매우 비슷하다.
- 은행 잔고, 재고 관리, 좌석 예매 등을 보장하고 싶을 때도 비슷한 문제가 생긴다.

#### 2.2.3. 채널 간 타이밍 의존성

- 이미지 업로드와 썸네일을 생성하는 예제 (p330)
- 이미지를 업로드하고 썸네일 생성 요청을 메시지 큐로 처리하는 경우, 이미지를 파일저장소에 업로드하기 전에 메시지 큐가 더 빠를 수 있다.
- 선형성의 최신성 보장이 없으면 이 두 채널 사이에 경쟁 조건이 발생할 수 있다.

### 2.3. 선형성 시스템 구현하기

- 선형성은 근본적으로 데이터 복사본이 하나만 있는 것처럼 동작하고 그 데이터에 실행되는 모든 연산은 원자적이라는 것을 의미한다.
- 가장 간단한 방법은 데이터 복사본을 하나만 사용하는 것이다.
  - 하나의 복사본을 저장한 노드에 장애가 나면 데이터는 손실되거나 노드가 살아날 때까지 접근할 수 없다.
- 복제를 사용하여 선형적으로 동작하는 시스템을 구현할 수 있다.
  - 단일 리더 복제 (선형적일 가능성)
    - 리더는 쓰기에 사용되는 데이터의 복사본을 갖고 있고 팔로워는 백업 복사본을 보관한다.
    - 동기식으로 갱신된 팔로워에서 실행한 읽기는 선형적이 될 가능성이 있다.
    - 동시성 버그, 스냅숏 격리 사용 등으로 비선형적일 수 있다.
  - 합의 알고리즘 (선형적)
    - 합의 프로콜에는 스플릿 브레인과 복제본이 뒤처지는 문제를 막을 수단이 포함된다.
    - 주키퍼, etcd 등 코디네이터
  - 다중 리더 복제 (비선형적)
    - 여러 노드에서 동시에 쓰기를 처리하고 비동기로 복제하기 때문에 비선형적이다.
    - 충돌 해소가 필요한 충돌 쓰기를 만들 수 있다.
  - 리더 없는 복제 (아마도 비선형적)
    - 정족수 읽기와 쓰기를 요구하여 엄격한 일관성을 달성할 수 있다고 주장한다.
    - 일 기준 시게를 기반으로 한 최종 쓰기 승리 충돌 해소 방법은 시계 스큐 때문에 순서를 보장할 수 없어서 비선형적이다.

#### 2.3.1. 선형성과 정족수

- 엄격한 정족수를 사용한 읽기 쓰기는 선형적인 것처럼 보이지만 네트워크 지연 변동이 심하면 경쟁 조건이 생길 수 있다. (p332)
- 정족수 조건이 만족되더라도 처음 읽은 값과 나중에 읽은 값이 다를 수 있어서 선형적이지 않다.
- 성능이 떨어지는 비용을 지불하고 다이나모 스타일 정족수를 선형적으로 만드는 게 가능하다.
  - 읽기를 실행하는 클라이언트는 결과를 애플리케이션에 반환하기 전에 읽기 복구를 동기식으로 수행해야 하고 쓰기를 실행하는 클라이언트는 쓰기 요청을 보내기 전에 노드들의 정족수로부터 최신 상태를 읽어야 한다.
  - 리더 없는 복제 시스템은 선형성을 제공하지 않는다고 보는게 가장 안전한다.

### 2.4. 선형성의 비용

- 두 데이터센터 사이의 네트워크가 끊기면...
- 다중 리더 데이터베이스는 비동기로 복제되므로 큐에 쌓았다가 정상화되면 전달하여 각 데이터센터를 정상 동작할 수 있다.
- 단일 리더 복제는 팔로워는 데이터가 뒤처졌을 수 있다. (비선형적)

#### 2.4.1. CAP 정리

- 선형성이 필요 없는 애플리케이션은 네트워크 문제에 더 강인하다.
- CAP 정리는 일관성, 가용성, 파티션 내구성 중 두 가지만 보장할 수 있다고 주장한다.
- 네트워크 분단이 생겼을 때 일관성과 가용성 중 하나를 선택하라는 의미로 보는게 더 좋다.
- 네트워크 분단 외 다양한 결함이나 트레이드오프를 논하지 않아 실용적 가치가 거의 없다.

#### 2.4.2. 선형성과 네트워크 지연

- 다중 코어 CPU 램 예제 (p335)
  - 메인 메모리 데이터 갱신을 비동기로 처리하여 선형성이 손실된다.
- 선형성과 성능은 트레이드오프 이다.
- 효율적인 선형 저장소를 찾을 수는 있지만 선형성을 원하면 쓰기 요청의 응답 시간이 적어도 네트워크 지연의 불확실성에 비례해야 함을 증명했다.
- 선형성 읽기와 쓰기의 응답 시간은 필연적으로 높다.


## 3. 순서화 보장

- 선형적 레지스터는 복사본이 하나만 있는것처럼 동작하고 그 데이터에 실행되는 모든 연산은 원자적이다.
  - 단일 리더 복제의 리더의 주 목적은 복제 로그에서 팔로워가 쓰기를 적용하는 순서를 보장하는 것
  - 직렬성은 트랜잭션들이 일련의 순서에 따라 실행되는 것처럼 동작하도록 보장하는 것
  - 분산 시스템에서 타임스탬프와 시계 사용은 무질서한 세상에 질서를 부여하려는 것
- 순서화, 선형성, 합의 사이에는 깊은 연결 관계가 있음이 드러난다.

### 3.1. 순서화와 인과성

- 순서화가 인과성을 보존하는데 도움을 준다.
  - 일관된 순서로 읽기에서 질문에 대한 응답의 인과적 의존성이 존재함
  - 네트워크 지연으로 쓰기 추월이 발생하지 않고 로우가 갱신되기 이전에 생성되야할 때
  - 동시 쓰기 감지에서 이전 발생 관계는 인과성을 표현하는 방법
  - 스냅숏 격리와 반복 읽기에서 트랜잭션이 일관된 스냅숏으로 읽을 때 일관적이란 인과성에 일관적이라는 의미
  - 쓰기 스큐와 팬텀에서도 호출대기에서 빠지는 동작은 누가 호출 대기 중인지 관찰하는 것에 인관적으로 의존
  - 서버로부터 뒤처진 결과를 받았다는 사실은 인과성 위반이다.
- 시스템이 인과성에 의해 부과된 순서를 지키면 그 시스템은 인과적으로 일관적이라고 한다.

#### 3.1.1. 인과적 순서가 전체 순서는 아니다

- 선형성
  - 연산의 전체 순서를 정할 수 있다.
  - 선형성 데이터스토어에는 동시적 연산이 없다.
  - 단일 데이터 복사본에 연산을 실행에 모든 요청이 한 시점에 원자적으로 처리되도록 보장해준다.
- 인과성
  - 두 이벤트에 인과적인 관계가 있으면 순서가 있지만 동시에 실행되면 비교할 수 없다.
  - git 같은 분산 버전 관리 시스템은 인과적 의존성 그래프와 유사하다.

#### 3.1.2. 선형성은 인과적 일관성보다 강하다

- 선형성은 인과성을 내포하여 선형적이라면 인과성도 올바르게 유지한다.
- 시스템을 선형적으로 만들면 네트워크 지연이 클 때 성능과 가용성에 해가될 수 있다.
- 인과적 일관성은 네트워크 지연 대문에 느려지지 않고 네트워크 장애가 발생해도 가용한 일관성 모델 중 가장 강력하다.
- 많은 경우에 더 필요한 것은 인과적 일관성이다.

#### 3.1.3. 인과적 의존성 담기

- 인과성을 유지하기 위해 어떤 연산이 어떤 다른 연산보다 먼저 실행됐는지 알아야 한다. (부분 순서)
- 버전 벡터를 일반화하여 인과적 의존성을 추가해야 한다.
- 이전 연산의 버전 번호를 데이터베이스로 되돌려주거나 직렬성 스냅숏 격리에서 트랜잭션이 커밋될 때 읽은 데이터가 최신인지 확인해야하는 것.

### 3.2. 일련번호 순서화

- 일련번호나 타임스탬프를 써서 이벤트의 순서를 정할 수 있다.
- 물리적 시게를 사용하면 시계 스큐가 발생할 수 있기 때문에 논리적 시계에서 얻어도 된다.
- 일련번호나 타임스탬프는 크기가 작고 전체 순서를 제공하며 인과성에 일관적인 전체 순서대로 일련변호를 생성할 수 있다.

#### 3.2.1. 비인과적 일련번호 생성기

- 단일 리더가 없이 여러 노드에서 일련변호를 생성하면 인과성에 일관적이지 않다.

#### 3.2.2. 램포트 타임스탬프

- 램포트 타임스탬프는 노드ID와 카운터를 조합한 일련번호를 뜻한다.
- 물리적 일 기준 시계와 아무 관련 없지만 전체 순서화를 제공한다.
- 카운터가 큰 것이 타임스탬프가 크고 카운터 값이 같다면 노드ID가 큰 것이 타임스탬프가 크다.
- 버전 벡터와 차이점
  - 버전 벡터는 두 연산의 인과적 의존성을 구별하지만 램포트 타임스탬프는 전체 순서화를 강제하여 두 연산이 동시적인지 인과적 의존성이 있는지 알 수 없다.
  - 버전 벡터보다 크기가 더 작다.

#### 3.2.3. 타임스탬프 순서화로는 충분하지 않다

- 유일성을 보장해야하는 연산을 동시에 요청되면 타임스탬프가 더 큰 연산은 실패하도록 사후에 결정할 수 있다.
- 노드는 이 요청이 당장 성공해야하는지는 알 수 없다.
- 노드는 이 요청 성공여부를 확신하려면 다른 노드를 모두 확인해야하고 장애나 네트워크 문제가 발생하면 내결함성이 떨어진다.
- 유일성 제약 조건 같은 것을 구현하려면 연산의 전체 순서와 순서의 확정 여부도 알아야 한다.

### 3.3. 전체 순서 브로드캐스트

- 전체 순서 브로드캐스트, 원자적 브로드캐스트
  - 처리량이 단일 리더가 처리할 수 있는 수준을 넘어 설 때 시스템을 확장하거나 리더에 장애가 발생했을 때 장애를 어떻게 복구할 것인가?
  - 두 가지 안정성 속성을 항상 만족해야함
    - 신뢰성 있는 전달: 메시지가 손실되지 않고 한 노드에 전달되면 모든 노드에도 전달된다.
    - 전체 순서가 정해진 전달: 모든 노드에 같은 순서로 전달된다.

#### 3.3.1. 전체 순서 브로드캐스트 사용하기

- 주키퍼나 etcd 같은 합의 서비스(코디네이터)는 전체 순서 브로드캐스트 구현한다.
- 모든 메시지가 데이터베이스에 쓰기를 나타내고 모든 복제 서버가 같은 쓰기 연산을 같은 순서로 처리하면 복제 서버들은 일관성 있는 상태를 유지한다. (상태 기계 복제)
- 메시지가 전달되는 시점에 그 순서가 고정되어 후속 메시지가 이미 전달됐다면 그 순서 앞에 메시지를 끼워넣는 게 허용되지 않는다. (타임스탬프 순서화보다 강하다.)
- 복제 로그, 트랜잭션 로그, 쓰기 전 로그 등을 만드는 방법 중에 하나고 직렬성 트랜잭션을 구현하는 데도 쓸 수 있다.
- 잠금을 획득하는 모든 요청은 로그에 추가되고 순서대로 일련번호가 붙는다. 단조 증가하는 일련번호는 펜싱 토큰의 역할을 할 수 있다. (주키퍼에서 zxid)

#### 3.3.2. 전체 순서 브로드캐스트를 사용해 선형성 저장소 구현하기

- 전체 순서 브로드캐스트는 비동기식이기 때문에 순서는 보장하지만 언제 전달될지는 보장하지 않는다.
- 선형성 저장소는 최신성을 보장해 읽기가 최근에 쓰여진 값을 보는 게 보장된다.
- 전체 순서 브로드캐스트에 추가 전용 로그를 사용하여서 선형성 CAS 연산을 구현하여 쓰기를 선형적으로 할 수 있다.
- 읽기를 선형적으로 만드려면?
  - 로그를 통해 순차 읽기하여 로그를 읽어서 메시지가 되돌아왔을 때 실제 읽기를 수행한다. (etcd 정족수 읽기)
  - 최신 로그 위치를 질의하고 그 위치까지 모든 항목이 전달되기를 기다린 후 읽기를 수행할 수 있다. (주키퍼 sync 함수)
  - 쓰기를 실행할 때 동기식으로 갱신돼서 최신이 보장되는 복제 서버에서 읽을 수 있다. (연쇄 복제)

#### 3.3.3. 선형성 저장소를 사용해 전체 순서 브로드캐스트 구현하기

- 가장 쉬운 방법은 정수를 저장하고 원자적 increment-and-get 연산이 지원되는 선형성 레지스터가 있다고 가정하는 것이다. (CAS 로도 가능)
- 전체 순서 브로드캐스트를 통해 보내고 싶은 모든 메시지에 대해 성형성 정수로 increment-and-get 연산을 수행하고 그 값을 메시지에 붙여서 보내면 된다.
- 램포트 타임스탬프와는 다르게 정확한 순서를 보장한다.
- 메시지 4를 전달하고 6인 메시지를 받았다면 메시지 5를 기다려야 한다는 것을 알 수 있다.
- 노드와 네트워크 문제로 연결이 끊긴 상황을 처리하고 노드에 장애가 날 때 그 값을 복구하는데 어려움이 있다.
- 일반적인 선형성 일련번호 생성기에 대해 고민하다보면 필연적으로 합의 알고리즘에 도달하게 된다.
- 선형성 CAS 레지스터와 전체 순서 브로드캐스트는 합의와 동등하다고 증명할 수 있다.

## 4. 분산 트랜잭션과 합의
