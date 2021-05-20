# 서버

- 거의 모든 형태의 데이터를 전송할 수 있다.
    - HTML, TEXT
    - 이미지, 음성, 영상, 파일
    - JSON, XML
- 서버 간 데이터를 주고 받을 때에도 대부분 HTTP를 사용한다.

## 웹 서버

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.10.03.png)

- HTTP 기반으로 동작한다.
- 정적 리소스를 제공한다.
  - HTML, CSS, JS, 이미지, 영상 등
- nginx, apache

## 웹 애플리케이션 서버(WAS)

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.10.08.png)

- HTTP 기반으로 동작한다.
- 웹 서버 기능을 포함한다.
- 프로그램 코드를 실행해서 애플리케이션 로직을 수행한다.
  - 동적 HTML, HTTP API(JSON)
  - 서블릿, JSP, 스프링 MVC
- 톰캣 jetty, undertow

## 웹 서버와 웹 애플리케이션 서버의 차이

- 웹 서버는 정적 리소스, WAS는 애플리케이션 로직을 담당한다.
- 사실 둘의 용어와 경계는 모호하다.
  - 웹 서버도 프로그램을 실행하는 기능을 포함하기도 한다.
  - WAS가 웹 서버의 기능을 제공한다.
- 자바는 서블릿 컨테이너 기능을 제공하면 WAS가 된다.
  - 서블릿 없이 자바 코드를 실행하는 서버 프레임워크도 존재한다.
- WAS는 애플리케이션 코드를 실행하는 데에 더 특화되어있다고 생각하면 된다.

## 웹 시스템 구성
### WAS, DB

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.22.01.png)

- 아주 단순하게는 WAS와 DB만으로도 시스템 구성이 가능하다.
- WAS를 이용해 정적 리소스와 애플리케이션 로직을 모두 제공할 수 있다.

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.22.15.png)

- WAS 혼자 너무 많은 역할을 담당하기 때문에 서버 과부하가 생길 수 있다.
- 가장 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있다.
  - 정적 리소스는 그냥 파일을 내려주면 된다.
  - 애플리케이션 로직은 DB도 뒤지고 할 일이 많다.
- WAS 장애는 오류 화면 노출도 불가능하다.
  - WAS는 결국 개발자가 개발하는 애플리케이션 로직이 들어가므로 장애가 쉽게 난다.
  - 서버가 완전히 내려가버리면 오류 화면조차도 보여줄 수가 없다.

### WEB, WAS, DB

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.22.22.png)

- 정적 리소스는 웹 서버가 처리하게 한다.
- 웹 서버는 애플리케이션 로직 등 동적인 처리가 필요할 때 WAS에 요청을 위임한다.
- WAS는 중요한 애플리케이션 로직 처리를 전담한다.

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.22.29.png)

- 효율적으로 리소스를 관리할 수 있다.
  - 정적 리소스가 많이 사용되면 웹 서버를 증설한다.
  - 애플리케이션 리소스가 많이 사용되면 WAS를 증설한다.

![](../../.gitbook/assets/kimyounghan-spring-mvc/01/screenshot%202021-06-05%20오후%2010.22.34.png)

- 정적 리소스만 제공하는 웹 서버는 잘 죽지 않는다.
  - 단순히 파일만 읽어서 클라이언트에 제공하면 된다.
- 애플리케이션 로직이 동작하는 WAS 서버는 잘 죽는다.
- WAS, DB 장애가 발생하면 웹 서버가 오류 화면을 제공할 수 있다.