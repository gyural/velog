<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/6ef6572d-b963-43ef-96b7-c6f9cbe48b6a/image.png" /></p>
<p>ref : <a href="https://velog.io/@zerodin/Spring-Security-%EC%B6%94%EA%B0%80%EB%A1%9C-%EC%9D%B8%ED%95%9C-Test-Code-refactoring">Spring Security 추가로 인한 Test Code refactoring</a></p>
<h1 id="문제-제기">문제 제기</h1>
<p>이번 프로젝트에서 처음으로 Spring-security를 도입하면서...
Webmvc테스트 도중 401에러와 403에러가 무수히 발생했다...!!!
<img alt="" src="https://velog.velcdn.com/images/gyural/post/c5dc0168-6045-4ac4-b2f5-c1f7513551f8/image.png" /></p>
<blockquote>
<p>왜 이런 에러가 날까?</p>
</blockquote>
<blockquote>
<p>이유1. @Webmvc 테스트는 기본적으로 spring seucirty 관련 컴포넌트를 스캔하게 된다...!!</p>
</blockquote>
<h3 id="해결법">해결법</h3>
<p>따라서 해당 필터를 exclude해준다...!!</p>
<p><code>코드</code></p>
<pre><code class="language-java">@WebMvcTest(
        excludeFilters = {
                @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = SecurityConfig.class)
        }
)
@Import({Slf4jRestControllerAdvice.class, ControllerAdviceTest.TestController.class})
public class ControllerAdviceTest {
    ... 생략
}</code></pre>
<blockquote>
<p>이유2. csrf token검사가 기본값이라서 이를 해제하지 않으면 에러가 발생한다.</p>
</blockquote>
<h3 id="해결법-1">해결법</h3>
<p>TestConfiguration을 추가적으로 만들어준다.</p>
<p><code>코드</code></p>
<pre><code class="language-java">@WebMvcTest(
        excludeFilters = {
                @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = SecurityConfig.class)
        }
)
@Import({Slf4jRestControllerAdvice.class, ControllerAdviceTest.TestController.class})
public class ControllerAdviceTest {
    ... 생략

    @TestConfiguration
    static class SecurityConfig {
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            // CSRF 보호 비활성화
            http
                    .csrf((auth) -&gt; auth.disable());
            return http.build();
        }
    }
}</code></pre>
<h3 id="환경">환경</h3>
<p><code>Language</code> : JAVA 21, JDK21
<code>Framework</code> : spring-boot 3.4.1
<code>IDE</code> : intellij</p>