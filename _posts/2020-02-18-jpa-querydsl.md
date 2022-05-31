---
title: "[JPA] Spring Data JPA와 Querydsl (feat. Spring MVC)"
author: Bae Sangwoo
date: 2020-02-18 00:18:00 +0900
categories: [JPA]
tags: [jpa, querydsl]
---

# EntityListener

```kotlin
@PrePersist
@PostPersist
@PreUpdate
@PostUpdate
@PreRemove
@PostRemove
@PostLoad
```


# AttributeConverter

```kotlin
@Converter
class SnackNutrientConverter : AttributeConverter<SnackNutrient, String> {

    private val mapper = jacksonObjectMapper()

    override fun convertToDatabaseColumn(attribute: SnackNutrient): String {
        return mapper.writeValueAsString(attribute)
    }

    override fun convertToEntityAttribute(dbData: String): SnackNutrient {
        return mapper.readValue(dbData)
    }
}
```


# Spring Data JPA

## Repository 커스텀, 확장

```kotlin
interface SnackRepositoryCustom {
    fun findBy(name: String): Snack?
}

@Repository
@Transactional
class SnackRepositoryCustomImpl(private val em: EntityManager) : SnackRepositoryCustom {

    override fun findBy(name: String): Snack? {
        return em.createQuery("SELECT s FROM Snack s WHERE s.name = :name", Snack::class.java)
            .setParameter("name", name)
            .resultList.firstOrNull()
    }
}
```

## 기본 Repository 커스텀

```kotlin
interface MyRepo<T, ID> : JpaRepository<T, ID>

class MyRepoImpl<T, ID>() : MyRepo<T, ID>, SimpleJpaRepository<T, ID>()

@EnableJpaRepositories(repositoryBaseClass = MyRepoImpl::class)
```


# Querydsl

[Querydsl 레퍼런스 문서](http://www.querydsl.com/static/querydsl/4.0.1/reference/ko-KR/html_single/){:target="_blank"}

## 의존성 및 어노테이션 프로세서 추가

```kotlin
implementation("com.querydsl:querydsl-jpa")
kapt("com.querydsl:querydsl-apt:4.2.1:jpa")
```

## QDomain 생성

```kotlin
./gradlew kaptKotlin -i -S
```

## 기본 사용법

```kotlin
val car = QCar.car
val query = JPAQuery<Car>(em)

val result = query.from(car)
    .where(car.name.eq("asd"))
    .fetch()
```

# Spring Data JPA와 Querydsl 함께 사용하기

```kotlin
@Transactional
class SnackRepositoryCustomImpl : SnackRepositoryCustom, QuerydslRepositorySupport(Snack::class.java) {

    override fun findBy(name: String): Snack? {
        return entityManager!!.createQuery("SELECT s FROM Snack s WHERE s.name = :name", Snack::class.java)
            .setParameter("name", name)
            .resultList.firstOrNull()
    }

    override fun findAllBy(name: String): List<Snack> {
        val snack = QSnack.snack

        return from(snack)
            .where(snack.name.eq(name))
            .fetch()
    }
}
```