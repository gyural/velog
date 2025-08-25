<h1 id="1-soap---rest-전환🌀">1. Soap -&gt; REST 전환🌀</h1>
<p>레거시 프로젝트에서는 HTTP프로토콜을 사용할 때 Soap을 사용하는 경우가 있다. 하지만 현재는 REST가 주류를 이룬다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/d5634ef2-bd53-468f-bf40-052b12d74934/image.png" /></p>
<blockquote>
<p>그 이유를 알아보자!!</p>
</blockquote>
<hr />
<h3 id="soap의-단점-유연성이-떨어짐">SOAP의 단점 유연성이 떨어짐</h3>
<p>유연성이 떨어진다는 것은 웹에서 치명적일 것이다. 예를 들어 <strong>전국민이 사용하는 API를 사용한다 했을 때</strong>
전국민은, 스마트폰, 컴퓨터, 노트북, 라디오, 등등 여러 기기로 접속을 할 수 있을 것이다(비유입니다.)</p>
<blockquote>
<p>하지만 유연성이 떨어진다면 각각의 기기에 맞는 API를 짜줘야 할 것입니다.</p>
</blockquote>
<h3 id="soap이-유연성이-떨어지는-이유">SOAP이 유연성이 떨어지는 이유???</h3>
<p><strong>1) SOAP은 확장 할때마다 WSDL(Web Service Description Language) 수정이 필요</strong>
주로 <code>서버</code>에서의 API명세서를 WSDL라고 생각하면 되고 SOAP API를 이용할 때 <code>클라이언트</code>에서 stub이라는 네트워크 자동 생성체를 통해 통신을 합니다.
이 때 stub은 WSDL을 보고 자동으로 통신 규약을 생성해줍니다.
-&gt; 즉 서버와 클라이언트 코드간의 결합도가 높습니다.(강한결합)</p>
<p><strong>2) 무거운 메시지 구조이기 때문에 IoT, 모바일에 적합하지 않음</strong>
SOAP은 보수적이며 보안이 뛰어난 API입니다. 따라서 <strong>통신 시 헤더나 토큰 같은 부가적인 정보가 무거워질 수 밖에 없습니다.</strong> 그렇기 때문에 물리적인 처리량의 한계가 있는 IoT, 모바일 장치에는 무거운 메시지는 버거울 수 있습니다.</p>
<h3 id="rest를-쓴다면">REST를 쓴다면??</h3>
<p>유연성이 매우 뛰어납니다. WSDL같은 문서에 의존할 수 없고 HTTP프로토콜의 status-code, Method, URI를 러닝커브도 낮습니다. 또한 헤더, 토큰, 쿠키같은 정보가 옵셔널이기 때문에 가벼운 메시지 무거운 메시지의 확장에도 다양합니다...!!</p>
<p>따라서 확장에 특화되어있는 현대의 서버 아키텍처(MSA, K8S) 등에 적합하다고 할 수 있습니다.</p>
<hr />
<h1 id="2-restcontroller의-요청-처리">2. @RestController의 요청 처리</h1>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/257b3a44-0784-40a9-a868-8b186cd28d14/image.png" /></p>
<p>모든번호를 보는게 아닌
4번, 5번, 6번, 7번, 8번만 봐주시면 됩니다.</p>
<h3 id="동작-상세">동작 상세</h3>
<p>여기서 컨트롤러에 요청이 들어온 상태입니다.</p>
<blockquote>
<p>④ 컨트롤러에 요청이 들어옴
⑤ 요청 처리 후 Service에서 결과 반환
⑥ 컨트롤러가 객체 or <code>ResponseEntity</code> 반환
➡️ 여기서 <code>@ResponseBody</code> 체크 → <code>ViewResolver</code> 건너뛰고 <code>MessageConverter</code> 호출
⑦ <code>HandlerAdapter</code> → <code>HttpMessageConverter</code> 통해 객체 → JSON or 문자열 변환
⑧ 변환된 응답을 <code>DispatcherServlet</code> → 클라이언트 전송</p>
</blockquote>
<hr />
<h3 id="ref">Ref</h3>
<p>1) <a href="https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest">https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest</a>
2) <a href="https://aws.amazon.com/ko/compare/the-difference-between-soap-rest/">https://aws.amazon.com/ko/compare/the-difference-between-soap-rest/</a>
3) <a href="https://jiwondev.tistory.com/266">https://jiwondev.tistory.com/266</a></p>