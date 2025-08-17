<h1 id="1-spirng-aop의-필요성">1. Spirng AOP의 필요성</h1>
<h3 id="aop란">AOP란.....</h3>
<blockquote>
<p>스프링 내부의 흩어진 관심사들을 Aspect라는 것으로 모듈화해 재사용하겠다는 목정의 프로그래밍이다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/afc1da45-b4b7-40be-8203-7595a88d941d/image.png" /></p>
<p>이 그림을 보면 각 클래스를 3개로 분리되었지만
공통된 관심사는 3개씩 있고 이를 AOP모듈화를 한다면 같은코드 반복을
*<em>7번 -&gt; 3번으로 줄일 수 있다.
*</em></p>
<p>이처럼 프로젝트 전체에 걸쳐 재사용될 수 있는 모듈들 혹은 코드들은 <strong>AOP 프로그래밍을 통해 코드 재사용을 방지</strong>할 수 있다!!</p>
<hr />
<h1 id="2-controlleradvice">2. @ControllerAdvice</h1>
<h3 id="1-controlleradvice란">1. @ControllerAdvice란?</h3>
<ul>
<li><p>Spring MVC에서 전역적으로 컨트롤러에 적용되는 기능을 분리할 수 있도록 도와주는 어노테이션입니다.</p>
</li>
<li><p>주로 @ExceptionHandler, @ModelAttribute, @InitBinder와 함께 사용되어, 모든 컨트롤러에 공통적으로 적용됩니다.
<code>코드</code></p>
<pre><code class="language-java">@ControllerAdvice
public class GlobalExceptionHandler {

@ExceptionHandler(Exception.class)
public ResponseEntity&lt;String&gt; handleException(Exception e) {
   return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                        .body(&quot;에러 발생: &quot; + e.getMessage());
}
}
</code></pre>
<h3 id="2-왜-aop-사례인가">2. 왜 AOP 사례인가?</h3>
</li>
</ul>
<ul>
<li>컨트롤러마다 예외 처리 코드를 중복해서 작성하지 않고, 공통된 로직을 하나의 모듈로 분리해 제공합니다.</li>
<li>실제 컨트롤러의 비즈니스 로직에는 예외 처리 코드가 사라지고 본연의 역할에만 집중할 수 있습니다.</li>
<li>이는 <strong>횡단 관심사(예외 처리)</strong>를 한 곳에서 모듈화하여 여러 지점(컨트롤러)에서 공통적으로 적용한다는 점에서 AOP의 전형적인 사례라고 볼 수 있습니다.</li>
</ul>
<h3 id="ref">REF</h3>
<ol>
<li><a href="https://engkimbs.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81AOP">새로비 : [Spring] 스프링 AOP (Spring AOP) 총정리 : 개념, 프록시 기반 AOP, @AOP
출처: https://engkimbs.tistory.com/entry/스프링AOP</a></li>
</ol>