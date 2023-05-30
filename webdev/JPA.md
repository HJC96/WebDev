### JPA
- JPA는 자바로 영속 영역을 처리하는 API 이다.
- 상위 개념은 ORM이라는 패러다임



### Board 엔티티와 JpaRepository
- JPA를 이용할때는 테이블과 SQL을 다루는 것 (X) 
- 데이터에 해당하는 객체를 엔티티 객체로 다루는 것 (O)


- 개발
  1.  엔티티 클래스 정의
     
     
### 스프링 부트 최신버전 QBoard 설치

~~~.gradle
buildscript {
    ext{
        queryDslVersion = "5.0.0"
    }
}

plugins {
    id 'java'
    id 'org.springframework.boot' version '3.1.0'
    id 'io.spring.dependency-management' version '1.1.0'
}

group = 'org.zerock'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect:3.1.0'

    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'
    annotationProcessor 'org.projectlombok:lombok'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'

    implementation("com.querydsl:querydsl-core:${queryDslVersion}")
    implementation("com.querydsl:querydsl-jpa:${queryDslVersion}:jakarta")

    annotationProcessor("com.querydsl:querydsl-apt:${queryDslVersion}:jakarta",
            "jakarta.persistence:jakarta.persistence-api:3.1.0")

}

tasks.named('test') {
    useJUnitPlatform()
}


sourceSets {
    main {
        java {
            srcDirs = ["$projectDir/src/main/java"]
        }
    }
}
~~~


<img width="854" alt="image" src="https://github.com/HJC96/WebDev/assets/87226129/437402dd-0647-4ccd-98bf-383c10ec012d">
