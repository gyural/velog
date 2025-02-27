<h1 id="1-요구사항">1. 요구사항</h1>
<p>✅ 사용자가 최종 결제를 위해 결제 페이지를 클릭한다
✅ 결제 페이지를 열고나서 5분동안 결제가 유효하다. (시간당 요금책정 방식 때문)</p>
<hr />
<h1 id="🚀-redis-기반-결제-세션-관리-전략">🚀 Redis 기반 결제 세션 관리 전략</h1>
<p>1) 현재 프로젝트는 레디스 메모리를 사용해 로그인 세션을 관리하고 있다. 따라서 해당 저장소에 결제 세션도 저장하려고 한다..!!</p>
<h3 id="💳결제를-위한-핵심-api-3가지">💳결제를 위한 핵심 API 3가지</h3>
<p>*<em>1) [GET] payment Info
*</em>👉 최종 결제 전 주문서를 보여주는 API.
👉 이 API 호출 시 5분짜리 세션을 생성함.</p>
<p>*<em>2) [POST] payment with PG
*</em>👉 계약된 PG사를 통해 프론트에서 결제 API를 호출.
👉 성공적으로 완료되면 특정 URL 값을 부여받음.</p>
<p>*<em>3) [POST] payment success
*</em>👉 특정 URL 값을 서버에 전달하여 최종 결제 완료 처리.</p>
<blockquote>
<p>이 때 첫번째 API 호출 시 5분의 세션을 만들 것이다.</p>
</blockquote>
<hr />
<h1 id="문제점">문제점</h1>
<p><strong>1) 만약 2번 API 호출에서 세션이 만료되었으면 해당 설계에서는,
5분의 시간이 초과했지만 PG사로 결제요청(무거운 요청)을 호출하게된다.</strong></p>
<p>이를 해결하기 위해</p>
<blockquote>
<p>API 하나를 또 추가한다.</p>
</blockquote>
<p><strong>2. 첫번째 API [GET] payment Info 에서 만든 API 내부 로직에서 결제세션이 무사히 생긴것을 어떻게 검증할 것이고, 예외처리는?</strong></p>
<p>이를 해결하기 위해
해당 API 마지막 로직으로 해당 세션을 다시 조회해본다.</p>
<hr />
<h1 id="최종-흐름">최종 흐름</h1>
<p>*<em>✅ 1단계: 결제 페이지 진입 시 (GET payment Info)
*</em>    1.    결제 정보를 조회하고 Redis에 5분짜리 세션 생성
    2.    세션 생성 후, 즉시 Redis.get(sessionKey)로 생성 검증
    🔹 실패 시: &quot;결제 세션 생성 실패&quot; 응답 → 프론트에서 재시도</p>
<p><strong>✅ 2단계: 결제 요청 전에 세션 검증 (POST payment/session/validate)</strong></p>
<ol>
<li>결제 버튼을 누르면, PG 호출 전에 이 API를 호출<ol start="2">
<li>Redis에서 세션 확인 후, 없으면 PG 호출 막음
🔹 세션 있음: &quot;세션 유효&quot; 응답 → 결제 진행
🔹 세션 없음: &quot;세션 만료, 다시 결제 페이지로 이동&quot;</li>
</ol>
</li>
</ol>
<p><strong>✅ 3단계: 결제 완료 후 (POST payment success)</strong></p>
<ol>
<li>PG 결제 성공 후, 결제 완료 처리<ol start="2">
<li>결제 성공 시 Redis 세션 삭제 (TTL 자동 만료 포함)</li>
</ol>
</li>
</ol>