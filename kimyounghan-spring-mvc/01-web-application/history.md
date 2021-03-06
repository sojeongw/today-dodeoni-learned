# 자바 백엔드 웹 기술 역사

## 과거

- 1997년 서블릿
    - 자바 코드로 짜야 하므로 HTML를 동적으로 생성하는 게 어려웠다.
- 1999년 JSP
    - HTML 생성은 편리하지만 비즈니스 로직까지 너무 많은 역할을 담당하게 된다.
- 서블릿, JSP를 조합한 MVC 패턴
    - 모델, 뷰 컨트롤러로 역할을 나누어 개발한다.
- 2000년 초 ~ 2010년 초 MVC 프레임워크 춘추 전국 시대
    - MVC 패턴을 자동화한다.
    - 복잡한 웹 기술을 편리하게 사용할 수 있는 다양한 기능을 지원한다.
    - ex) 스트럿츠, 웹워크, 스프링 MVC(과거 버전)

## 현재

- 애너테이션 기반의 스프링 MVC
    - @Controller
    - MVC 프레임워크의 춘추 전국 시대가 마무리된다.
- 스프링 부트의 등장
    - 서버를 내장한 스프링 부트가 등장한다.
    - 과거에는 서버에 WAS를 직접 설치하고 소스는 WAR 파일을 만들어 설치한 WAS에 배포했다.
    - 스프링 부트는 빌드 결과인 JAR에 WAS 서버가 포함되어 빌드가 단순화되었다.

## 최신

스프링 웹 기술이 분화되고 있다.

- Web Servlet
    - Spring MVC
- Web Reactive
    - Spring WebFlux

### WebFlux

- 비동기로 non blocking을 처리한다.
- 최소 스레드로 최대 성능을 낸다,
    - 스레드 컨텍스트 스위칭 비용을 효율화한다.
- 함수형 스타일로 개발할 수 있다.
    - 동시 처리 코드의 효율성을 높인다.
- 서블릿 기술을 사용하지 않는다.
    - netty를 이용한다.

하지만 웹 플럭스는 기술적인 난이도가 매우 높다. 아직 RDB에 대한 지원이 부족하다. 일반 MVC의 스레드 모델도 충분히 빨라서 실무에서 아직 많이 사용하지는 않는다.

## 자바 뷰 템플릿

HTML을 편리하게 생성하는 뷰 기능이다.

### JSP

속도가 느리고 기능이 부족하다.

### 프리마커, 벨로시티

속도 문제를 해결했으며 다양한 기능이 있다.

### 타임리프

내추럴 템플릿으로 HTML의 모양을 유지하면서 뷰 템플릿을 적용할 수 있다. 스프링 MVC와 기능이 통합되어 있어 최선의 선택이다. 단, 성능은 프리마커와 벨로시티가 더 빠르다.