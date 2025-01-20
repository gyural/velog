<h2 id="환경-세팅">환경 세팅!!</h2>
<p><a href="https://start.spring.io/">https://start.spring.io/</a></p>
<p>—&gt; 해당 사이트에서 스프링 프로젝트 생성하기</p>
<p><strong>선택 사항들</strong>
<code>Project</code></p>
<p><strong>Maven VS Gradle</strong>
Maven : 과거에 많이 쓰임
Gradle : 요즘 추세에 많이 쓰임</p>
<p><code>Spring Boot</code></p>
<p>M1은 정식출시가 안되었다는 뜻
SNAPSHOT은 아직 완성이 안되었다는 뜻이다.
<strong>2.3.1을 사용</strong></p>
<p><code>Project Metadata</code></p>
<p><strong>Group :</strong> 보통 회사의 도메인을 사용함
—&gt; 그냥 hello로 작성하고 공부
<strong>Artifact :</strong> 빌드 될때 결과물을 기입한다.</p>
<p><code>Dependencies</code>
—&gt; 어떤 라이브러리를 사용할 것인지를 말한다.</p>
<p><strong>추가할 것</strong>
<strong><code>Spring Web</code></strong>
<strong><code>thymeleaf</code> :</strong> html을 만들어주는 템플릿 엔진
—&gt; 모두 선택한 뒤에 다운 받고 압축 해제후 IntelliJ에서 build.gradle Open!!!</p>
<hr />
<h3 id="폴더-구조">폴더 구조</h3>
<p><code>.gradle</code>  <code>.idea</code>는 ide와 spring의 기본설정이므로 생략한다.</p>
<p><code>src&gt; main</code>은 실제 코드 <code>src&gt;test</code>는 testing코드들이 저장된다.</p>
<h3 id="실행하기">실행하기</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/f3fd05ce-4417-4fa8-a707-6385fa941f2b/image.png" /></p>
<p>main클래스 옆에 실행버튼을 눌러준다!!!</p>
<p><strong>결과</strong>
<img alt="" src="https://velog.velcdn.com/images/gyural/post/5a671c0f-af2a-41f7-b70d-2f577a1589f9/image.png" /></p>
<p>로컬 8080포트로 들어간다면</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/06ac2c03-f77c-4d29-a090-3aec45f29f9c/image.png" /></p>
<p>404 에러 페이지가 뜬다!!!</p>
<h3 id="build설정">Build설정</h3>
<p><code>Build and run using</code>   <code>Run tests using</code> 설정을 둘다
IntelliJ IDEA로 바꿔준다!!!
—&gt; Gradle을 통해 Run을 해주는데 이를 방지하여 속도개선 가능하다</p>
<hr />
<h2 id="라이브러리-살펴보기">라이브러리 살펴보기</h2>
<p>엄청 많은 외부 라이브러리가 있는데…
이는 <strong>의존성 라이브러리를 Gradle이 알아서 가져온다!!!</strong></p>
<h3 id="핵심-라이브러리">핵심 라이브러리</h3>
<p><strong>스프링 부트 라이브러리</strong></p>
<blockquote>
<p>Spring-boot-starte-web
-spring-boot-starte-tomcat: 톰캣(웹서버)
-spring-webmvc: 스프링 웹 MVC</p>
</blockquote>
<blockquote>
<p>spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)</p>
</blockquote>
<blockquote>
<p>spring-boot-starte(공통): 스프링 부트 + 스프링 코어 + 로깅
-spring-boot
—spring-core
-spring-boot-starte-logging
—logback, slf4j</p>
</blockquote>
<p><strong>테스트 라이브러리</strong></p>
<blockquote>
<p>-spring-boot-starter-test
-junit: 테스트 프레임워크
-mockito: </p>
</blockquote>
<hr />
<h2 id="view-환경설정">View 환경설정</h2>
<p>src &gt; main &gt; resources &gt; static
경로에 <code>index.html</code>을 생성 후 8080포트로 접속하게 된다면…
<strong>Welcome Page</strong>를 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/2e157157-d19a-48f7-9a49-f3e6b12a8c97/image.png" /></p>
<p>—&gt; 이는 Spring에서 제공해주는 WelcomPage기능이다!!</p>
<h3 id="controller">Controller</h3>
<p><strong>1) Controller 작성</strong>
Main&gt;java&gt;프로젝트&gt; 에 <strong>Controller</strong>라는 Package를 만든 후 아래파일 작성</p>
<p><strong>HelloController.java</strong></p>
<pre><code class="language-jsx">package com.example.demo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {
    @GetMapping(&quot;hello&quot;)
    public String hello(Model model){
        model.addAttribute(&quot;data&quot;, &quot;hello!!&quot;);
        return &quot;hello&quot;;
    }
}</code></pre>
<p>2) hello 페이지 작성
Main &gt; Resources &gt; templetes에 아래 파일 작성</p>
<p><strong>hello.html</strong></p>
<pre><code class="language-html">&lt;!doctype html&gt;
&lt;html xmlns:th=&quot;http://www.thymeleaf.org&quot;&gt;
&lt;head&gt;
    &lt;title&gt;Hello&lt;/title&gt;
    &lt;meta http-equiv=&quot;Content-Type&quot; content=&quot;text/html; charset=UTF-8&quot;&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;p th:text=&quot;'안녕하세요.'+${data}&quot;&gt;&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<h3 id="작동-원리">작동 원리!!!</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/4504c90b-54e5-449f-b82f-7eacd7e8c5f0/image.png" /></p>
<p>1) 웹에서 <code>/hello</code> 주소로 GET요청을 보냄
2)  <code>@GetMapping(&quot;hello&quot;)</code> 를 통해 요청에 대한mapping이 된다.
3) hello.java로직에 의해 hello.html이 응답으로 간다.</p>
<p><strong>hello.java</strong></p>
<pre><code class="language-java">public class HelloController {
    @GetMapping(&quot;hello&quot;) //get요청 매핑
    public String hello(Model model){
        model.addAttribute(&quot;data&quot;, &quot;hello!!&quot;);
        return &quot;hello&quot;;
    }
}</code></pre>
<p>여기서 <strong>return “hello”</strong>는 String을 리턴하는 것이 아니라…
<strong>viewResolver</strong>가
resources &gt; templete&gt; hello.html을 찾아서 Model값을 넘기며 동작한다.</p>
<hr />
<h2 id="빌드">빌드</h2>
<p><strong>0) 터미널에서 프로젝트 디렉토리로 이동</strong>
<strong>1) 다음 명령어로 빌드하기</strong></p>
<pre><code class="language-powershell">// Window
.\gradlew.bat build
// MAC
gradlew build</code></pre>
<p><strong>결과</strong>
<img alt="" src="https://velog.velcdn.com/images/gyural/post/7f7cbf5c-c9c0-40f9-8be2-adcb1602462a/image.png" /></p>
<p><strong>2) build폴더로 디렉토리 이동 후 다음 명령어 입력</strong></p>
<pre><code class="language-powershell">cd build\lib
java -jar &lt;SNAPSHOT.jar 파일명&gt;
</code></pre>
<p><strong>결과</strong>
<img alt="" src="https://velog.velcdn.com/images/gyural/post/9f4b71d9-97e4-4d5d-8871-9d6753884c74/image.png" /></p>
<hr />