<h1 id="1-둘의-차이점">1. 둘의 차이점</h1>
<blockquote>
<p>사실상 차이는 <strong>@ResponseBody</strong>가 붙였나 안붙였냐에 차이밖에 없다!!</p>
</blockquote>
<h3 id="responsebody란">@ResponseBody란?</h3>
<p>이 녀석은 JAVA클래스를 JSON형태로 변환할 수 있는 HTTP BODY형태로 바꿔주는 역할을 한다.</p>
<hr />
<h1 id="2-controller동작-방식">2. @Controller동작 방식</h1>
<p>컨트롤러는 주로 View를 반환하기위해 사용된다. MVC Container에서의 동작방식을 알아보겠습니다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/365c3d96-0b43-4bde-b8ae-76b7a3855b58/image.png" /></p>
<p>여기서 모든번호를 보는게 아닌
4번, 5번, 6번, 7번, 8번, 9번만 봐주시면 됩니다.</p>
<h3 id="동작-상세">동작 상세</h3>
<p>먼저 상황은 컨트롤러에 요청이 들어온 상태입니다.</p>
<blockquote>
<p>④ : 컨트롤러에 요청이 들어옴
⑤ : 요청을 처리하여 Service레이어로 넘겨줌 
⑥ : <code>HandlerAdapter</code>로 응답 View Name을 넘겨줌
⑦ : <code>HandlerAdapter</code> 는 해당 ViewName을 <code>DispatcherServlet</code>에 넘겨줌
⑧, ⑨ : <code>DispatcherServlet</code>은 View Resolver를 통해 View객체를 가져옴</p>
</blockquote>
<hr />
<h1 id="2-restcontroller동작-방식">2. @RestController동작 방식</h1>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/257b3a44-0784-40a9-a868-8b186cd28d14/image.png" /></p>
<p>여기서도 모든번호를 보는게 아닌
4번, 5번, 6번, 7번, 8번만 봐주시면 됩니다.</p>
<h3 id="동작-상세-1">동작 상세</h3>
<p>여기서도 컨트롤러에 요청이 들어온 상태입니다.</p>
<blockquote>
<p>④ 컨트롤러에 요청이 들어옴
⑤ 요청 처리 후 Service에서 결과 반환
⑥ 컨트롤러가 객체 or <code>ResponseEntity</code> 반환
➡️ 여기서 <code>@ResponseBody</code> 체크 → <code>ViewResolver</code> 건너뛰고 <code>MessageConverter</code> 호출
⑦ <code>HandlerAdapter</code> → <code>HttpMessageConverter</code> 통해 객체 → JSON or 문자열 변환
⑧ 변환된 응답을 <code>DispatcherServlet</code> → 클라이언트 전송</p>
</blockquote>
<h1 id="ref">Ref</h1>
<ol>
<li><a href="https://mangkyu.tistory.com/49">망나니 개발자 : [Spring] @Controller와 @RestController 차이</a></li>
</ol>