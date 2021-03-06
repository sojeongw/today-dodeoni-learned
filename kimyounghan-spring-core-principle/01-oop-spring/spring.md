# 스프링의 탄생

2002년, 로드 존슨이 EJB의 문제점을 지적하고 EJB 없이 개발하는 방법을 책으로 낸다. 여기서 나온 대부분이 지금의 스프링에 녹아들어 있다. 유겐 휠러의 제안으로 함께
스프링을 개발하게 되었고 특히 유겐 힐러는 스프링의 핵심 코드 대부분을 개발했다.

전통적인 J2EE(EJB)라는 겨울을 넘어 새로운 시작이라는 의미로 `스프링`이라는 이름으로 탄생했다.

## 스프링이란?

![](../../.gitbook/assets/kimyounghan-spring-core-principle/01/screenshot%202021-03-20%20오후%207.01.45.png)

스프링은 여러 기술의 모음이다.

## 스프링 프레임워크

- 핵심 기술
    - 스프링 DI 컨테이너, AOP, 이벤트, 기타
- 웹 기술
    - 스프링 MVC, 스프링 WebFlux
- 데이터 접근 기술
    - 트랜잭션, JDBC, ORM 지원, XML 지원
- 기술 통합
    - 캐시, 이메일, 원격접근, 스케줄링
- 테스트
    - 스프링 기반 테스트 지원
- 언어
    - 코틀린, 그루비

최근에는 스프링 부트를 통해서 스프링 프레임워크의 기술들을 편리하게 사용할 수 있다.

## 스프링 부트

스프링을 편리하게 사용할 수 있도록 지원한다. 

- 최근에는 스프링 부트를 기본으로 사용한다. 
- 단독으로 실행할 수 있는 스프링 애플리케이션을 쉽게 생성할 수 있다. 
- Tomcat 같은 웹 서버를 내장해서 별도의 웹 서버를 설치하지 않아도 된다.
- starter 종속성을 제공해서 필요한 것을 모두 준비해주고 빌드 환경을 손쉽게 구성할 수 있다.
- 스프링과 3rd party(외부) 라이브러리의 버전 등을 자동으로 구성해준다.
- 메트릭, 상태 확인, 외부 구성 같은 프로덕션 준비 기능을 제공한다. 
- 관례에 의해 간결하게 설정할 수 있다.