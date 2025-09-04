<h2 id="1----entity와-api의-강한-결합">1.    Entity와 API의 강한 결합</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/fc1c9fd0-5a39-4552-8a9d-f58cfe7ed0f8/image.png" /></p>
<p>Entity는 Persistence Layer에서 관리하는 클래스이다.
이를 컨트롤러에서 노출하는 구조라면 둘사이의<code>강한결합</code>이 생긴다.</p>
<p>최악의경우 DB 스키마가 수정된다면 Controller와 API스펙까지 수정해야되야할 수 있다.</p>
<h2 id="2-osivopen-session-in-view-설정-문제">2. OSIV(Open Session In View) 설정 문제</h2>
<p>OSIV란 요청이 끝날 때까지 영속성 컨텍스트와 DB 커넥션을 열어두는 것 따라서 프로덕션에서 이를 켜두면 요청 당 DB커넥션 풀의 점유시간이 길어지기 때문에 성능이슈가 발생한다.</p>
<p>프로덕션 환경에서는 성능 이슈와 불필요한 커넥션이 유지 때문에 OSIV=false로 두는 경우가 많다. 이 때 Controller에서 LayLoading인 필드에 접근을 한다면 <code>LazyInitializationException</code> 이 발생한다!! (매우 위험할듯)</p>
<h2 id="3-보안-문제">3. 보안 문제</h2>
<p>민감한 데이터가 노출될 수 있다. 비빌번호, SecretKey, 고유식별키 같은 정보가 클라이언트에 노출될 수 있다.</p>
<h2 id="4-이를-반영한-최종-아키텍처">4. 이를 반영한 최종 아키텍처</h2>
<p>1) 엔티티 DTO변환에서도 lazy에 의한 join 쿼리가 발생할 수 있으므로 DTO변환까지 Service레이어에서 담당한다. </p>
<pre><code class="language-java">public EntityDTO 서비스메서드() {}</code></pre>
<p>2) 컨트롤러에서 해당 <code>EntityDTO</code>를 반환하면 Web -&gt; Controller구조에서 Web -&gt; Service의존성이 생기기 때문에 <code>EntityDTO</code>를 <code>EntityResponse</code>로 변환하여 반환한다. 이때는 EntityDto변환시 모든필드를 DB에서 받아왔기 때문에 OSIV위험도 없다!!</p>
<pre><code class="language-java">@RestController(&quot;api/test&quot;)
public EntityResponse 컨트롤러 메서드() {}</code></pre>