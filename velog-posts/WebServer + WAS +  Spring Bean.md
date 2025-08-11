<h1 id="web-server와-was">Web Server와 WAS</h1>
<p>먼저 이름에서도 알다시피 공통점으로는 둘 다 서버라는 점이다.
둘 다 클라이언트에 요청이 오면 해당 요청에 맞는 응답을 준다는 점에서 공통적인 부분을 가지고 있다.</p>
<blockquote>
<p>그렇다면 차이점은?</p>
</blockquote>
<h3 id="차이점">차이점</h3>
<p>_정적/동적 컨텐츠를 제공할 수 있느냐에 따른 차이가 있다. 
_</p>
<blockquote>
<p>대표적인 정적 컨텐츠 : <code>HTML</code>, <code>CSS</code>, <code>이미지</code>, <code>동영상</code>
대표적인 동적 컨텐츠 : 유튜브 댓글, 좋아요 처럼 실시간으로 변하는 컨텐츠</p>
</blockquote>
<p><code>WebServer</code> 는 정적 컨텐츠를 제공한다. 정해진 url이나 컨텐츠의 저장 경로를 통해서 빠르게 찾아 낼 수 있다.
<code>WAS</code> 는 동적 컨텐츠를 제공한다. SQL문이나 모델에 저장된 내용을 요청이 올때마다 조회하여 동적으로 컨텐츠가 변경될 수 있다.</p>
<h3 id="대표-예시">대표 예시</h3>
<p><code>WebServer</code> : NginX, Apach
<code>WAS</code> : Tomcat, JBoss</p>
<p>_Spring Boot는 내장 Tomcat을 포함해서 하나의 실행 JAR로 동작
_→ Tomcat이 WAS 역할을 하므로 별도의 외부 WAS 설치가 필요 없음
→ 애플리케이션 실행 = WAS 실행</p>
<hr />
<h1 id="spring-bean등록방법">Spring Bean등록방법</h1>
<h3 id="1-어노테이션-기반-등록">1) 어노테이션 기반 등록</h3>
<p>일반적으로 많이 사용하는 방법이다.
<code>@Component, @Service, @Repository, @Controller</code> 같은 어노테이션으로 등록할 수 있으며, 간결한 문법으로 사용할 수 있고, DI를 적용하기가 쉬워진다.</p>
<p>동작원리로는 SpringBootApplication이 실행되면 해당 프로젝트 단위의 패키지와 클래스의 이름으로 <strong>컴포넌트 스캔</strong>이 이루어지고 스캔을 한 모든 빈에서 SpringCotainer가 DI를 고려해클래스를 생성하고 빈으로 등록한다.</p>
<p>단점으로는 <code>ObjectMapper</code>같은 외부 라이브러리는 위 방식으로 빈등록이 불가능하다는 한계가있다.</p>
<pre><code class="language-java">// 외부 라이브러리 내부 (코드 수정 불가)
public class ObjectMapper {
}</code></pre>
<h3 id="2-configuration--bean-java-config">2) Configuration + @Bean (Java Config)</h3>
<p>위에 등록방법의 한계를 해결해줄 수 있다. 해당 방식은 컴포넌트 스캔 방식으로 등록하는 것이 아니다.</p>
<pre><code class="language-java">@Configuration
public class AppConfig {
    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper(); // 내가 new 해서 만듦
    }
}</code></pre>
<p>위 코드에서는 먼저 SpringApplication이 실행되면 <code>@Configuration</code>클래스를 Bean으로 등록한다. 이후 <code>@Bean</code> 메서드를 호출하는데 이 때 메서드에서 리턴한 객체를 그대로 Bean으로 등록하기 때문에 외부 라입러리 클래스도 등록할 수가 있다.</p>