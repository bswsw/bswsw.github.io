---
title: "[JPA] 엔티티 작성 시 주의 사항 (feat. 스프링 부트,  코틀린)"
author: Bae Sangwoo
date: 2020-02-06 22:17:00 +0900
categories: [JPA]
tags: [jpa, entity]
---

단일 데이터 소스를 사용하고 특별한 조건이 없으면서 **스프링 부트**를 사용한다면 JPA를 설정하는 것은 매우 간편합니다.

그러나 기본적인 엔티티 작성 방법 외에 사용 시 주의해야 할 사항들이 있어 정리해봅니다.

개인적인 관점에서 필요하다 생각하는 주의 사항이므로 잘못된 방법이라면 댓글로 지적 부탁드립니다.

또는 추가적으로 주의 사항이 있다면 댓글로 정보를 주시면 감사드리겠습니다. 


# 엔티티 작성 가이드

- [@Entity](https://docs.oracle.com/javaee/7/api/javax/persistence/Entity.html){:target="_blank"} 애노테이션을 사용해야 합니다.
- `public` 혹은 `protected` 접근자의 기본 생성자가 필요합니다.
- 탑 레벨 클래스이어야 하고 `enum` 이나 `interface`는 엔티티가 될 수 없습니다.

> 참고 : https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#entity-pojo


# 엔티티 작성 시 주의 사항

## `equals()`, `hashCode()` 재정의

엔티티는 식별자(`@Id`)를 가지는 객체로써 `equals()`와 `hashCode()`를 재정의해주어야 동일성과 동등성이 보장될 수 있습니다.

재정의 시에 기본적으로 `@Id` 필드는 필수로 넣어주시면 좋습니다.

```kotlin
@Entity
class Blog {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null

    var title: String? = null

    var content: String? = null

    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Blog

        if (id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return id?.hashCode() ?: 0
    }
}
```

## `toString()` 재정의 시 연관관계 필드 제외

테스트 혹은 필요에 의해 자동으로 `toString()`을 재정의하는 경우 연관관계 필드들은 제외하고 정의하셔야 합니다.

특히 양방향 관계라면 더욱 조심하여야 합니다.

```kotlin
@Entity
class Parent {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null

    var name: String? = null

    @OneToMany(mappedBy = "parent")
    val children: MutableSet = mutableSetOf()

    override fun toString(): String {
        return "Parent(id=$id, name=$name, children=$children)"
    }
}

@Entity
class Child {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long? = null

    var name: String? = null

    @ManyToOne(fetch = FetchType.LAZY)
    var parent: Parent? = null

    // Convenient method
    fun matchParent(parent: Parent) {
        this.parent = parent
        parent.children.add(this)
    }

    override fun toString(): String {
        return "Child(id=$id, name=$name, parent=$parent)"
    }
}
```

위와 같은 두 엔티티가 양방향 관계를 맺고 `toString()`을 자동 생성하여 연관 관계 필드를 참조하고 

아래와 같이 루프를 돌면서 프린트를 찍으면 순환 참조가 계속 발생하여 스택 오버 플로우 익셉션이 발생하고 

애플리케이션이 종료되는 것을 볼 수 있습니다.

```kotlin
@Test
@DisplayName("toString() 순환 참조 테스트")
fun testRecursiveError() {
    childRepository.findAll().forEach(::println)
}
```

![](/assets/img/posts/2020-02-06-jpa-entity/recursive-error.png)
*엔티티에서 toString()이 잘못 사용된 예*


# Kotlin 기준 엔티티 작성 시 주의 사항

코틀린 기반 스프링을 사용하면서 JPA를 사용한다면 추가적으로 주의해야할 사항들이 있으니 꼭 체크해주세요.
 
## 코틀린 all open 플러그인 잘쓰기

```kotlin
// allopen 확장, @Component 등 특정 스프링 어노테이션에 대해 allopen 플러그인을 적용.
kotlin("plugin.spring") version kotlinVersion

// noarg 확장, @Entity, @Embeddable, @MappedSuperclass 어노테이션에 대해 noarg 플러그인 적용.
kotlin("plugin.jpa") version kotlinVersion
```

코틀린을 사용하여 스프링과 JPA를 개발할 때 보통 기본적으로 위와 같이 두 플러그인 설정을 하게 됩니다. 

주석에 달린 설명대로 간단하게 설명할 수 있으며 만약 두 플러그인에 대해 자세히 모르신다면 하며 

자세한 정보는 아래 두 링크를 참고해주세요.

- [Kotlin Spring Plugin](https://kotlinlang.org/docs/reference/compiler-plugins.html#spring-support){:target="_blank"}
- [Kotlin JPA Plugin](https://kotlinlang.org/docs/reference/compiler-plugins.html#jpa-support){:target="_blank"}

중요한 것은 코틀린으로 JPA의 지연 로딩을 제대로 사용하려면 all open 플러그인의 커스텀이 필요합니다. 

지연 로딩은 프록시 객체를 만들 수 있어야 하는데 그러려면 엔티티 클래스에 `final` 키워드가 없어야 합니다.

그러나 코틀린의 `class`와 프로퍼티(필드)는 기본값이 `final`이기 때문에 `open` 키워드를 클래스와 필드에 

사용해주어야 하지만 

매우 불편한 일이므로 아래와 같이 설정하여 지연 로딩이 가능하게 즉, 프록시 객체 생성이 가능하게 설정할 수 있습니다.

```kotlin
allOpen {
    annotation("javax.persistence.Entity")
    annotation("javax.persistence.MappedSuperclass")
    annotation("javax.persistence.Embeddable")
}
```

위와 같이 설정하여 `@Entity`, `@MappedSuperclass`, `@Embeddable`을 사용하는 클래스와 프로퍼티를 

`open` 시킬 수 있습니다.

이렇게 JPA에서 엔티티를 사용할 때 주의해야 할 점을 간단하게 알아봤습니다.

감사합니다.
