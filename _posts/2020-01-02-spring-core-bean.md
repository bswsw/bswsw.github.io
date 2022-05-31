---
title: "[Spring] 스프링 AOP 초간단 예제 (애노테이션 기반)"
author: Bae Sangwoo
date: 2020-01-04 23:33:00 +0900
categories: [Spring, Core]
tags: [spring, aop]
---

![](/assets/img/posts/2020-01-02-spring-core-aop/aop-img.jpg)
*Aspect는 어떻게 적용될까요? 출처 : https://stackoverflow.com/questions/29650355/why-in-spring-aop-the-object-are-wrapped-into-a-jdk-proxy-that-implements-interf*

AOP 란 [Aspect Oriented Programming](https://flowframework.readthedocs.io/en/stable/TheDefinitiveGuide/PartIII/AspectOrientedProgramming.html){:target="_blank" rel="noopener"}의 약자로 우리말로는 [관점 지향 프로그래밍](https://ko.wikipedia.org/wiki/%EA%B4%80%EC%A0%90_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D) 이라 합니다.

관점이라는 말이 어렵게 느껴진다면 관심사에 따라 기능을 분리하여 모듈화하여 개발하는 개념이라고 생각해 볼 수 있습니다.

여러 메소드에 비즈니스 로직과 크게 관련이 없는 로직이 중복되어 있을때 사용할 수 있고 모듈화된 해당 기능은 재사용성도 용이해집니다.


간단하게 설명했지만 위 그림처럼 복잡하고 다양한 개념과 이론이 존재하고 실무에 적용하려고 하면 꽤나 학습이 필요한 개념입니다.

최대한 초보자가 AOP에 입문하는데 도움을 주기 위해 예제 코드는 단순하게 작성하였습니다. (저도 아직 초보자입니다...)

함수 실행 여부의 로깅하는 기능을 AOP를 사용한 샘플입니다.


예제는 Gradle, 스프링부트, 코틀린 기반으로 작성이 되어있습니다. 

AOP를 적용하기 위해 의존성을 추가해주어야 하고 테스트 를 위해 스프링부트 2.2에서 테스트 의존성으로 추가된 JUnit5를 사용하겠습니다.

<script src="https://gist.github.com/sangwoobae/deb358925d6c7d60de4693eb0bee043c.js"></script>

로깅이 필요한 함수에 사용할 애노테이션을 추가하고 레벨 지정을 위한 `Enum` 클래스도 생성하였습니다.

애노테이션에 로그 레벨 지정과 로그 메시지를 전달하기 위한 변수를 추가하였고 함수를 호출할 때 마다 애노테이션을 사용해야하기 때문에 리텐션을 런타임으로 주었고 

함수에 사용할 애노티에션이므로 타켓을 함수로 지정하였습니다.

<script src="https://gist.github.com/sangwoobae/768a036c48f073d640748127ac5af511.js"></script>

이제 로깅하는 기능을 분리해보겠습니다.

해당 기능을 담당하는 클래스를 생성하여 `Bean`으로 등록하고 AOP에서 관점이라는 개념으로 사용되는 [@Asepct](https://www.eclipse.org/aspectj/doc/next/aspectj5rt-api/org/aspectj/lang/annotation/Aspect.html){:target="_blank" rel="noopener"} 애노테이션을 추가해줍니다.

로깅하는 함수 내에 파라미터가 두개 들어가 있는데 [ProceedingJoinPoint](https://www.eclipse.org/aspectj/doc/next/runtime-api/org/aspectj/lang/ProceedingJoinPoint.html){:target="_blank"}는 실제 실행될 핵심 함수를 의미하고 

위에서 생성한 애노테이션의 변수를 사용하여 로깅하기 위해 두번째 파라미터로 넣어줍니다.

[@Around](https://www.eclipse.org/aspectj/doc/next/aspectj5rt-api/org/aspectj/lang/annotation/Around.html){:target="_blank"}는 해당 함수의 실행 조건을 지정할 수 있습니다. 

현재는 애노테이션 기반의 AOP를 구현 중이므로 위에서 생성한 애노테이션을 실행 조건으로 줍니다. 

단, 이번 예제처럼 함수에 파라미터로 애노테이션을 받았다면 해당 파라미터명으로 적어줍니다.

<script src="https://gist.github.com/sangwoobae/a79c8c43e4badf0985ea8624b0119cea.js"></script>

이제 핵심 기능을 개발하고 로깅이 필요하다면 생성한 `@LoggerMarker` 애노테이션을 달아주고 로그 레벨과 메시지를 지정합니다.

핵심 기능은 주문한 메뉴를 가져오는 메소드와 가격을 가져오는 메소드가 있습니다.

<script src="https://gist.github.com/sangwoobae/8171a6ddd39e931f71c26b525ed38749.js"></script>

이제 테스트를 작성하여 서비스 함수를 실행하여 로깅이 되는지 확인해보겠습니다.

<script src="https://gist.github.com/sangwoobae/337645f6094b2d744f819c3cf4d5838a.js"></script>

![](/assets/img/posts/2020-01-02-spring-core-aop/aop-test.png)
*테스트 실행 결과*

테스트를 실행하면 위와 같이 지정한 로그 레벨과 메시지가 정상적으로 실행되는 것을 볼 수 있습니다.

이렇게 `Aspect`의 `@Around` 조건으로 애노테이션을 사용한 단순한 스프링 AOP를 간단하게 구현해 보았습니다.

AOP에서 사용되는 여러 용어와 다양한 구현 방식이 존재하므로 위에서 말씀드린대로 많은 학습이 필요한 개념입니다. 
 
이 단순한 예제로 AOP를 이해하는데 조금이나마 도움이 되었길 바랍니다.

읽어주셔서 감사합니다 :)
