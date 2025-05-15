<h1 id="한-스텝씩-신중히">한 스텝씩 신중히</h1>
<p>검색 기능은 사용자가 뭘 원하는지 부터 기능이 정의 되어야합니다.
따라서 UX설계 -&gt; UI설계 -&gt; API설계 -&gt; API구현 각 Step에서의 고민이 있었습니다.</p>
<h1 id="1-ux">1. UX</h1>
<h3 id="어떤거를-사용자가-원할까">어떤거를 사용자가 원할까?</h3>
<p>🔍 Need 1. 현재 위치에서 가까운 주차장을 알고 싶을 때
    <em>“약수역 근처에 주차장이 있나?”</em>
✅ 사용자는 지도를 중심으로 한 주변 탐색을 원함
✅ 검색 없이도 현재 위치 중심 반경 주차장 조회 기능 필요</p>
<hr />
<p>🔍 Need 2. 특정 장소 근처의 주차장을 알고 싶을 때
_    “약수역 횟집에서 친구들이랑 만나기로 했는데, 근처에 주차장 있나?”_
✅ 사용자는 가게/장소명을 입력하고 그 주변 주차장을 탐색하고자 함
✅ 장소 검색 → 해당 위치 중심으로 지도 이동 → 주변 주차장 표시
(밑의 UI를 확인하면 이해가 쉽다..!!)</p>
<hr />
<p>🔍 Need 3. 주차장명 &quot;오제제%&quot; 이름을 가지는 주차장
_    “그때 갔던 주차장이 ‘오제제 주차장’이었던 것 같은데…”_
✅ 사용자는 기억나는 이름 일부만으로 검색하고 싶어함
✅ 텍스트 기반 주차장명 키워드 검색 기능 제공 필요</p>
<hr />
<blockquote>
<p>이렇게 사용자의 Needs와 핵심 UX를 설계했고 해당 아래에 UI로 구현해 보겠습니다. </p>
</blockquote>
<h1 id="2-ui">2. UI</h1>
<p>아래 UI로 설계한 UX 구현이 가능해집니다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/4cba0565-b41b-43ad-9893-c7048ba339b6/image.png" /></p>
<hr />
<h1 id="3-api-설계">3. API 설계</h1>
<h3 id="1-병목-발생-예상-가능">1. 병목 발생 예상 가능</h3>
<p>키워드 검색 시 API에서 검색 응답을 준다면 1)<code>주차장 DB 탐색</code> 2)<code>카카오 로컬 검색 API호출</code>을 하게 됩니다. 
1)<code>주차장 DB 탐색</code> 완료했지만 2)<code>카카오 로컬 검색 API호출</code> 진행중인** 병목이 발생할 수 있습니다.**</p>
<h3 id="해결책">해결책</h3>
<p>API를 두개로 분리합니다!!!</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/e208953d-b3b0-41dd-aa2b-2daa1375fa03/image.png" /></p>
<h3 id="1-mathing된-키워드와-인덱스-표시">1. Mathing된 키워드와 인덱스 표시</h3>
<p>UI적으로 키워드와 매칭된 하이라이팅을 하게된다면 가독성지 좋아질 것입니다. </p>
<p><code>응답 JSON 일부</code></p>
<pre><code class="language-yaml">&quot;parkingZones&quot;: [
      {
              &quot;matchedInfo&quot;: [
                  {
                    &quot;field&quot;: &quot;zoneName&quot;,
                    &quot;value&quot;: &quot;오제제 주차장&quot;,
                    &quot;matchedText&quot;: &quot;오제제&quot;,
                    &quot;startIndex&quot;: 0,
                    &quot;endIndex&quot;: 2
                  },
                  {
                    &quot;field&quot;: &quot;cityName&quot;,
                    &quot;value&quot;: &quot;서울특별시&quot;,
                    &quot;matchedText&quot;: &quot;서울&quot;,
                    &quot;startIndex&quot;: 0,
                    &quot;endIndex&quot;: 1
                  }
                ]</code></pre>
<hr />
<h1 id="4-구현-밑-코드">4. 구현 밑 코드</h1>
<p>TDD방식의
RED -&gt; GREEN -&gt; REFACTORING
단계를 준수하며 구현해냈습니다.</p>
<h3 id="pr-링크">PR 링크</h3>
<p>1) 키워드 검색 코드 <a href="https://github.com/Team-Devmon-IN-KU/HonorsParking-BE/pull/80">https://github.com/Team-Devmon-IN-KU/HonorsParking-BE/pull/80</a>
2) 카카오 로컬 검색 코드 : (미완)</p>
<hr />
<h3 id="q1--카카오-검색을-넉넉하게-사용-가능한가">Q1 : 카카오 검색을 넉넉하게 사용 가능한가?</h3>
<p>*<em>1) 일간 10만 쿼터 초과시 유로 요금 발생
*</em>
<img alt="" src="https://velog.velcdn.com/images/gyural/post/4bc03608-ff8e-4002-9cc6-f7bdcc817fbe/image.png" /></p>
<p>ref : <a href="https://developers.kakao.com/docs/latest/ko/getting-started/quota">https://developers.kakao.com/docs/latest/ko/getting-started/quota</a></p>
<p><strong>2) 넉넉한 응답 개수</strong>
네이버 로컬 검색은 최대 5개밖에 응답을 받을 수 없었지만  최대 45개 데이터 습득 가능</p>
<h1 id="5-느낀점">5. 느낀점</h1>
<p>1) 본격적인 TDD개발</p>
<p>2) </p>