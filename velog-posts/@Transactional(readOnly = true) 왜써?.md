<p>REF : 
<a href="https://hungseong.tistory.com/74">빠군 : @Transactional(readOnly = true)를 왜 붙여야 하나요
</a></p>
<h1 id="1-transactionalreadonly--true-동작-과정">1. @Transactional(readOnly = true) 동작 과정</h1>
<pre><code class="language-java">@Transactional(readOnly = true)
    public Page&lt;StudioResponse&gt; getStudiosByConceptId(Long conceptId, int page) {
        Pageable pageable = PageUtils.createPageable(page);
        return conceptStudioRepository.findByConceptId(conceptId, pageable)
        .... 생략
    }</code></pre>
<p>*<em>1) 첫 Find 쿼리가 발동이 되면 해당 트랜잭션의 영속성 컨텍스트에 값이 저장된다.
*</em>
-&gt; 이를 통해 중복 조회시에 캐싱이 가능해진다.
-&gt; 영속성 context에서 값이 관리되기때문에 정상적인 Lazy-Loading이 가능해진다.
*<em>2) Dirth-check(병경감지) ❌ 일반 <code>@Transactional</code> 과 다름
*</em>
--&gt; 따라서 읽기 연산에 최적화되어있다.</p>
<hr />
<h1 id="2-그러면-그냥-트랜잭션-관리를-안해주면되는거-아니야">2. 그러면 그냥 트랜잭션 관리를 안해주면되는거 아니야?</h1>
<p>위 방식으로 사용해야하는 이유는 크게 2가지 이다.</p>
<p><strong>1) DB 확장성에 유리
**
대규모 서비스에 가까워질 수록 DB Replica를 통해 읽기 전용 DB를 확장에 부하를 줄여주곤한다. 이때 <code>@Transactional(readOnly=true)</code>를 사용해준다면, 자동으로 확장된 DB에서 값을 읽는다 
<img alt="" src="https://velog.velcdn.com/images/gyural/post/6b3c7733-e5b1-40e3-ad40-cd9a31a542b7/image.png" />
**2) 조회값 캐싱</strong>
non <code>@Transactional</code> 메서드와 달리 영속성 컨텍스트를 사용하기 때문에 초기 조회값을 캐싱이 가능해진다.</p>
<blockquote>
<p>이처럼 확장성 측면에서는 조회 메서드에서는 <code>@Transactional(readOnly=true)</code>를 쓰는것이 효과적이다고, 결론을 내릴 수 있다!</p>
</blockquote>
<h3 id="차이점-요약">차이점 요약</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/972e8147-5786-447d-a6d8-9ceb9e6a873c/image.png" /></p>