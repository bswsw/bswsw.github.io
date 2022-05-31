---
title: "[Spring] 스프링프레임워크 Bean 등록과 사용의 변천사"
author: Bae Sangwoo
date: 2020-01-02 23:37:00 +0900
categories: [Spring, Core]
tags: [spring, bean]
---

요즘엔 [스프링 부트](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/){:target="_blank" rel="noopener"}를 주로 사용하기 때문에 XML로 스프링 어플리케이션을 설정할 일이 전혀 없지만 

스프링을 처음으로 접하는 개발자에게 간단하게나마 설명을 하기 위해 간단히 샘플 코드를 작성해 보았습니다.

샘플 코드가 코틀린 기반이라 자바 기반 코드와 조금 다른 점이 있으니 참고하시게 되면 주의해주시기 바랍니다.

# XML 설정으로 Bean 등록과 사용

초창기 사용 방법으로 일일히 빈을 등록하는 XML 설정에 등록할 빈을 정의합니다.

클래스를 생성할 때 마다 빈으로 등록하기 위한 XML 작업이 많아지는 불편함이 있고 IDE 기능이 없다면 매우 사용하기 어렵습니다.

`resources/application-context.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Bean 등록 (id: Bean 이름, class: Bean으로 등록할 클래스의 풀패키지 경로 -->
    <bean id="teamService" class="com.baegoon.lunchstudy.DefaultTeamService"/>

    <bean id="memberService" class="com.baegoon.lunchstudy.DefaultMemberService">
        <!-- 생성자로 받는 Bean 주입 -->
        <constructor-arg name="teamService" ref="teamService"/>
    </bean>

</beans>
```

`Application.kt`

```kotlin
/**
 * 메인 메소드
 */
fun main(args: Array<String>) {
    // 작성한 XML 설정을 Application Context로 사용
    val context: ApplicationContext = ClassPathXmlApplicationContext("application-context.xml")

    // 팀 서비스 Bean 호출
    val teamService = context.getBean("teamService") as TeamService
    println(teamService.getTeam())

    // 멤버 서비스 Bean 호출
    val memberService = context.getBean("memberService", MemberService::class.java)
    println(memberService.getMember())
}

/**
 * 팀 서비스 인터페이스
 */
interface TeamService {
    fun getTeam(): String
}

/**
 * 팀 서비스 구현체
 */
class DefaultTeamService : TeamService {
    override fun getTeam(): String {
        return "테크컨버전스팀"
    }
}

/**
 * 멤버 서비스 인터페이스
 */
interface MemberService {
    fun getMember(): String
}

/**
 * 멤버 서비스 구현체
 * @property TeamService teamService 팀 서비스 빈 주입
 */
class DefaultMemberService(private val teamService: TeamService) : MemberService {
    override fun getMember(): String {
        return "${teamService.getTeam()} 배상우"
    }
}
```

# XML 설정으로 패키지를 검색하여 Bean 등록과 사용

스프링 2.5 부터 제공된 방식으로 XML 설정이 간단해지고 이 때부터 애노테이션 기반으로 Bean을 생성 합니다.

위 방법 보단 조금 나아진 방법이지만 현재는 사용하지 않습니다.

`resources/application-context.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- com.baegoon.lunchstudy 패키지부터 모든 빈을 스캔 -->
    <context:component-scan base-package="com.baegoon.lunchstudy"/>

</beans>
```

`Application.kt`

```kotlin
/**
 * 메인 메소드
 */
fun main(args: Array<String>) {
    // 작성한 XML 설정을 Application Context로 사용
    val context: ApplicationContext = ClassPathXmlApplicationContext("application-context.xml")

    // 팀 서비스 Bean 호출
    val teamService = context.getBean("defaultTeamService") as TeamService
    println(teamService.getTeam())

    // 멤버 서비스 Bean 호출
    val memberService = context.getBean("defaultMemberService", MemberService::class.java)
    println(memberService.getMember())
}

/**
 * 팀 서비스 인터페이스
 */
interface TeamService {
    fun getTeam(): String
}

/**
 * 팀 서비스 구현체
 */
@Service // 애노테이션으로 Bean 명시
class DefaultTeamService : TeamService {
    override fun getTeam(): String {
        return "테크컨버전스팀"
    }
}

/**
 * 멤버 서비스 인터페이스
 */
interface MemberService {
    fun getMember(): String
}

/**
 * 멤버 서비스 구현체
 * @property TeamService teamService 팀 서비스 빈 주입
 */
@Service // 애노테이션으로 Bean 명시
class DefaultMemberService(private val teamService: TeamService) : MemberService {
    override fun getMember(): String {
        return "${teamService.getTeam()} 배상우"
    }
}
```

# Configuration Class로 Bean 등록과 사용 (Java Config)

1번의 발전된 방법으로 XML 설정없이 클래스 파일로 설정을 만들어서 빈을 등록하고 사용할 수 있습니다.

코틀린 없던 시절 [Java Config](https://www.google.com/search?q=spring+java+config&oq=spring+java+config&aqs=chrome..69i57j0l4j69i60l3.2717j0j7&sourceid=chrome&ie=UTF-8){:target="_blank" rel="noopener"} 라고 많이 불렸습니다.

단, 1번과 동일한 문제인 빈을 일일히 등록해줘야하는 번거로움이 존재합니다.

`Application.kt`

```kotlin
/**
 * 메인 메소드
 */
fun main(args: Array<String>) {
    // 작성한 Configuration 클래스를 Application Context로 사용
    val context: ApplicationContext = AnnotationConfigApplicationContext(ApplicationConfiguration::class.java)

    // 팀 서비스 Bean 호출
    val teamService = context.getBean("teamService") as TeamService
    println(teamService.getTeam())

    // 멤버 서비스 Bean 호출
    val memberService = context.getBean("memberService", MemberService::class.java)
    println(memberService.getMember())
}

/**
 * 팀 서비스 인터페이스
 */
interface TeamService {
    fun getTeam(): String
}

/**
 * 팀 서비스 구현체
 */
class DefaultTeamService : TeamService {
    override fun getTeam(): String {
        return "테크컨버전스팀"
    }
}

/**
 * 멤버 서비스 인터페이스
 */
interface MemberService {
    fun getMember(): String
}

/**
 * 멤버 서비스 구현체
 * @property TeamService teamService 팀 서비스 빈 주입
 */
class DefaultMemberService(private val teamService: TeamService) : MemberService {
    override fun getMember(): String {
        return "${teamService.getTeam()} 배상우"
    }
}

/**
 * Bean 설정 클래스
 */
@Configuration // 설정 클래스임을 의미
class ApplicationConfiguration {

    /**
     * 팀 서비스 Bean 등록
     * 함수명이 Bean 이름임
     */
    @Bean
    fun teamService(): TeamService {
        return DefaultTeamService()
    }

    /**
     * 멤버 서비스 Bean 등록
     * 함수명이 Bean 이름임
     * @param teamService [TeamService] 팀 서비스 Bean 주입
     */
    @Bean
    fun memberService(teamService: TeamService): MemberService {
        return DefaultMemberService(teamService)
    }
}
```

# Configuration Class로 패키지를 검색하여 Bean 등록과 사용

설정 클래스에 [@ComponentScan](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html){:target="_blank" rel="noopener"} 이라는 애노테이션을 붙이고 `basePackagesClass` 옵션을 주어 Bean을 스캔하고 등록 합니다.

2번의 발전된 방식으로 매우 간편해졌습다. 

1, 2번 처럼 XML로 작성하여 Type-safety 하지 못하거나 불편함이 없고 3번 처럼 일일히 Bean을 등록하는 코드가 들어갈 필요 없이 매우 간결합니다.

스프링 부트가 나오기 직전까지 사용되던 방식입니다.

`Application.kt`

```kotlin
/**
 * 메인 메소드
 */
fun main(args: Array<String>) {
    // 작성한 Configuration 클래스를 Application Context로 사용
    val context: ApplicationContext = AnnotationConfigApplicationContext(ApplicationConfiguration::class.java)

    // 팀 서비스 Bean 호출
    val teamService = context.getBean("teamService") as TeamService
    println(teamService.getTeam())

    // 멤버 서비스 Bean 호출
    val memberService = context.getBean("memberService", MemberService::class.java)
    println(memberService.getMember())
}

/**
 * 팀 서비스 인터페이스
 */
interface TeamService {
    fun getTeam(): String
}

/**
 * 팀 서비스 구현체
 */
@Service("teamService") // teamService로 Bean 이름 지정
class DefaultTeamService : TeamService {
    override fun getTeam(): String {
        return "테크컨버전스팀"
    }
}

/**
 * 멤버 서비스 인터페이스
 */
interface MemberService {
    fun getMember(): String
}

/**
 * 멤버 서비스 구현체
 * @property TeamService teamService 팀 서비스 빈 주입
 */
@Service("memberService")
class DefaultMemberService(private val teamService: TeamService) : MemberService {
    override fun getMember(): String {
        return "${teamService.getTeam()} 배상우"
    }
}

/**
 * Bean 설정 클래스
 */
@Configuration // 애노테이션이 붙어있으면 설정 클래스임을 의미
@ComponentScan(basePackageClasses = [LunchStudyApplication::class]) // 애노테이션을 사용하여 Bean을 검색할 패키지를 지정
class ApplicationConfiguration
```

# 스프링 부트 등장!

선 코드!

`Application.kt`

```kotlin
/**
 * 스프링 부트 어플리케이션
 */
@SpringBootApplication
class LunchStudyApplication

/**
 * 메인 메소드
 */
fun main(args: Array<String>) {
    // 스프링 부트 어플리케이션 실행
    runApplication<LunchStudyApplication>(*args)
}

/**
 * 팀 서비스 인터페이스
 */
interface TeamService {
    fun getTeam(): String
}

/**
 * 팀 서비스 구현체
 */
@Service
class DefaultTeamService : TeamService {
    override fun getTeam(): String {
        return "테크컨버전스팀"
    }
}

/**
 * 멤버 서비스 인터페이스
 */
interface MemberService {
    fun getMember(): String
}

/**
 * 멤버 서비스 구현체
 * @property TeamService teamService 팀 서비스 빈 주입
 */
@Service
class DefaultMemberService(private val teamService: TeamService) : MemberService {
    override fun getMember(): String {
        return "${teamService.getTeam()} 배상우"
    }
}
```

스프링 부트가 자동 설정 기능을 제공하여 Bean을 등록하고 사용하는데 획기적인 변화가 있었습니다.

메인 어플리케이션 클래스에 있는 [@SpringBootApplication](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/SpringBootApplication.html){:target="_blank" rel="noopener"} 애노테이션이 핵심적인 역할을 하는데

`@SpringBootApplication` 애노테이션 자체가 [@Configuration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html){:target="_blank" rel="noopener"}과 [@ComponentScan](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html){:target="_blank" rel="noopener"} 애노테이션을 모두 포함하기 때문에 가능한 일입니다.

물론 더 많은 일들을 하고 있으니 레퍼런스를 참고해보시면 좋습니다.
