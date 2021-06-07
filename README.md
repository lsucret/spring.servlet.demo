# spring.servlet.demo

# 서블릿 애플리케이션 개발

레거시 프로젝트를 생성해 봅니다.

앞으로 이 레거시 프로젝트에서부터 spring MVC 프로젝트로 변해갈것.

## 1. **플젝 생성**

![/Untitled.png](images/Untitled.png)

![/Untitled%201.png](images/Untitled%201.png)

![/Untitled%202.png](images/Untitled%202.png)

## 2. maven repository 들어가서 java servlet 검색해서 소스 copy, pom.xml 에 paste.

```java
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

    <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>      
    </dependency>

```

**scope**

이 의존성을 언제 어떻게 classpath에 넣고 쓸거냐

`provided` : 코딩하는 시점엔 쓸 수 있음. 런타임 시점, war 패키징할 땐 클래스패스에서 빠짐.

그럼 어디에서 제공되나? 컨테이너에서 제공됨.

`test` : 소스 클래스패스에선 사용 못함. 오로지 테스트를 실행할때만 쓸 수 있음

## 3. java 패키지 생성

src.main 밑에 java 패키지 생성한 후 프로젝트 스트럭쳐 - modules - Sources 들어가서 java 패키지를 sources로 mark

## 4. Java 파일 생성

java 패키지 밑에 me.hwpark 패키지 생성 후 HelloServlet 클래스 생성

```java
package me.hwpark;

// import 생략...

public class HelloServlet extends HttpServlet {
    // ctrl + o 눌러 override
    @Override
    public void init() throws ServletException {
        System.out.println("init");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
        resp.getWriter().println("<html>");
        resp.getWriter().println("<head>");
        resp.getWriter().println("<body>");
        resp.getWriter().println("<h1>test hwpark</h1>");
        resp.getWriter().println("</body>");
        resp.getWriter().println("</head>");
        resp.getWriter().println("</html>");
    }

    @Override
    public void destroy() {
        System.out.println("destroy");
    }

}
```

## 5. 서블릿 컨테이너에 배포

![/Untitled%203.png](images/Untitled%203.png)

우리가 독자적으로 서블릿을 실행할 수 있는 방법은 없음. 

실행은 톰캣이 필요하고, 서블릿 컨테이너에 배포하는 식으로 해야함. 

톰캣 다운 후 압축 풀면 됨.

그리고 오른쪽 하단의 빨간 느낌표 눌러서 war expploded 클릭.

**톰캣 사용 방법**

**윈도우** 라면 톰캣 압축 풀기만 하면 끝.

**mac** 이라면 톰캣 압축푼 파일 bin안에 들어가서 쉘들에게 권한 주기. 그럼 sh파일 색이 다르게 나올것

```java
chmod +x ./*.sh
```

**[Server 탭]**

이 어플리케이션을 실행할 수 있는 서버는 9.0.14 버전이고

**[Deployed 탭]**

**javaservletdemo:war exploded**

배포를 하는데 톰캣을 띄울때 자바서블릿데모란 애는 배포법이 2가지.
1. war로 묶어서 배포하는 방법
2. war를 압축을 풀어헤친 상태로 앱을 배포하는 방법도 있음. 지금은 후자(war exploded)

**Application context: /javaservletdemo_war_exploded**

애플리케이션이 뜨는 위치 지정. /만 남기고 삭제.

## 6. web.xml 내용 추가

```java
<web-app>
  <display-name>Archetype Created Web Application</display-name>
  <!-- 여기서부터 추가 -->
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>me.hwpark.HelloServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
</web-app>
```

## 6. 실행

**uri로 들어가면?**

[http://localhost:8081/hello](http://localhost:8081/hello)
콘솔에 init, doGet 출력.

요청을 받고 서블릿이 처음 사용이 됨. 

서블릿 인스턴스를 만들고 라이프사이클에 따라 init 메서드가 호출됨. 그래서 콘솔이 찍힌것.

그다음엔 get요청도 처리해야하니 doGet이 호출되고 html이 보인것.

**한번더 uri를 새로고침하면?**

콘솔에 doGet만 출력

서블릿은 이미 한번 메모리에 로딩되면서 초기화가 됐기 때문에 다시는 init을 호출하지 않음.

**서블릿 컨테이너를 종료하면**

(tomcat) destroy가 호출됨.

---

# 정리

우리가 학습할 spring mvc는 httpServlet 기반으로 만들어졌다.

- 어노테이션으로 어떻게 처리를 했을까.
- 어떻게 web.xml을 만들지도 않고 웹앱을 잘 띄울 수 있었을까.
- spring mvc도 서블릿이었고 그런데 왜web.xml에 등록하지 않아도 됐을까

앞으로 이런 궁금증을 해결해 줄것


---
## servlet spec 번역 정리글

([https://javaee.github.io/servlet-spec/downloads/servlet-3.1/Final/servlet-3_1-final.pdf](https://javaee.github.io/servlet-spec/downloads/servlet-3.1/Final/servlet-3_1-final.pdf))

## 서블릿이란?

- Java 기반 Web Component 이며 동적 컨텐츠를 생산
- Platform independent한 Java Class
- Container도 서블릿 기능을 제공하는 웹서버의 확장판이다. 그래서 서블릿 엔진이라고도 불린다.
- Web Client와 Servlet Container로 구현된 요청/응답 패러다임으로 상호소통한다.

**Communication Paradigm**  
Request/Response Paradigm은 Communication Paradigm의 한 종류
다른 패러다임은 dracle docs 참고바람
([https://docs.oracle.com/cd/E13203_01/tuxedo/tux71/html/pgint3.htm](https://docs.oracle.com/cd/E13203_01/tuxedo/tux71/html/pgint3.htm))

## 서블릿 컨테이너란?

웹서버 or WAS의 일부분으로 요청과 응답 사이에서 MIME 기반의 요청을 복호화하고 MIME기반의 응답을 포맷하는 네트워크 서비스를 제공한다. 또한 서블릿을 담고있어 서블릿의 생애주기동안 관리한다.

서블릿 컨테이너는 웹 서버에 의해 만들어지거나 애드온 컴포넌트로서 인스톨된다. Web-enabled 어플리케이션 서버들에서도 인스톨되거나 built될 수 있다.

모든 서블릿 컨테이너는 리퀘스트, 리스폰스 를 위한 HTTP를 지원해야 한다. 그러나 추가적으로 HTTPS 또한 지원될 수도 있다. 컨테이너는 HTTP 명세에서 1.1, 1.1을 필수로 구현해야 한다. 컨테이너가 (RFC2616, HTTP/1.1)캐싱 메카니즘을 가지고 있어야 할 수도 있기 때문이다. 이 RFC2616은 클라이언트에서 서블릿으로 가기 전에 요청을 수정할 수도 있고, 서블릿에서 클라이언트로 전송되기 전에 보내는 응답이 수정될 수도 있고, 아예 요청에 대한 응답을 서블릿에 전송하지 않고도 RFC2616의 준수 하에 받을 수도 있기 때문이다.

**MIME types (Multipurpose Internet Mail Extensions)**  
전송된 문서의 다양성을 알려주기 위한 메커니즘. 브라우저가 리소스를 내려받았을 때 해야 할 기본 동작이 무엇인지를 결정하기 위해 사용함.
type/subtype 형태의 구조로 구성된다.
예시로는 application/json, text/plain, text/html 등이 있다.
(https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

## An Example

다음이 전형적인 이벤트의 순서이다.

1. 클라이언트가 Web server에 접근해서 HTTP 요청을 생성한다.
2. 웹서버가 요청을 받고 서블릿 컨테이너로 핸들한다.
서블릿 컨테이너는 특정 호스트 웹서버에서 동일한 프로세스를 실행할 수도, 다른 프로세스를 실행할 수도 있고, 또는 서로다른 호스트에서 서로 다른 프로세스를 실행할 수도 있다.
3. 서블릿 컨테이너는 서블릿의 설정을 기반으로 '어떤 서블릿을 invoke'할지, 그리고 '그 서블릿에게 요청과 응답 객체와 함께 호출'할지를 결정한다.
4. 서블릿은 요청 객체를 사용해 원격 user는 누군지, HTTP POST 파라미터는 무언지, 다른 연관 데이터는 뭐가 있는지를 알아낸다. 로직이나 프로그래밍이 어떻든간에 서블릿은 프로그램되어있다 클라이언트한테 보낼 데이터를 생성한다. 서블릿은 데이터를 응답 객체를 통해 데이터를 클라이언트에게 전송한다.
5. 서블릿이 요청 프로세스를 종료했다면, 서블릿 컨테이너는 응답이 적절하게 flush 됐는지 확인하고 컨트롤을 호스트 웹 서버로 되돌려준다.
