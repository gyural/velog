<h3 id="키워드">키워드</h3>
<h4 id="디바운싱--프론트에서-무분별한-api호출을-막기-위해-디바운싱을-적용시켜야합니다">디바운싱 : 프론트에서 무분별한 api호출을 막기 위해 디바운싱을 적용시켜야합니다.</h4>
<h4 id="트라이-자료구조-">트라이 자료구조 :</h4>
<h1 id="1-ux">1. UX</h1>
<h3 id="어떤거를-사용자가-원할까">어떤거를 사용자가 원할까?</h3>
<p>need-1) 현재 위치에서 가까운 주차장 정보를 알고 싶을때
    (약수역 근처에 주차장이 있나??)
need-2) 특정 오브젝트를 찍고 그 근처에 주차장을 알고 싶을 때
    (약수역 횟집에서 친구들을 만나기로 했는데 그 횟집근처에 있나? 횟집을 검색해보자)
need-3) 주차장명 &quot;오제제%&quot; 이름을 가지는 주차장
    (아 그때 같던 주차장이름이 오제제 주차장인거 같은데 검색해볼까?</p>
<p>해당 사용자 need3가지를 만족할만한 UI는 아래와 같습니다...!!!</p>
<hr />
<h1 id="2-ui">2. UI</h1>
<p>아래 UI로 구현한다면</p>
<div style="display: flex;">
  <img alt="ㅎㅎ" src="https://velog.velcdn.com/images/gyural/post/5d1cdb05-b6b7-46dd-a2eb-61388286ad65/image.png" style="width: auto; margin-right: 16px;" />

  <div>
    <h3>need-1 충족</h2>
    <p>point처음 검색화면 접속 시 현재 위치기반 주차장을 정렬해서 보여줍니다.'오제제'이름을 포함한 주차장을 제일 위에 배치합니다.</p>
    <h3>need-2 충족</h2>
    <p>아래에 네이버 지도 검색 API를 통해 오브젝트 리스트를 확인할 수 있고 해당 리스트 클릭 시 오브젝트 위치기반 주차장 리스트가 검색됩니다.</p>
    <p>
    <h3>need-3 충족</h2>
    <p>그림과 같이 "오제제"라는 이름을 포함하는 주차장을 상위에 배치해 주차장을 쉽게 찾을 수 있게 합니다.</p>
    <p>
  </div>
</div>


<hr />
<h1 id="3-api-설계">3. API 설계</h1>
<h3 id="병목-발생-예상-가능">병목 발생 예상 가능</h3>
<p>한 API에서 검색 응답을 준다면 1)주차장 DB 탐색 2) 네이버 검색 API호출을 하기 때문에
1)번은 완료했지만 2)번은 완료가 아직 안되어 병목이 발생할 수 있습니다.</p>
<p>따라서
<em>API를 두개로 분리합니다!!!</em></p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/e208953d-b3b0-41dd-aa2b-2daa1375fa03/image.png" /></p>
<hr />
<h1 id="4-구현">4. 구현</h1>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/4bc03608-ff8e-4002-9cc6-f7bdcc817fbe/image.png" /></p>
<p>ref : <a href="https://developers.kakao.com/docs/latest/ko/getting-started/quota">https://developers.kakao.com/docs/latest/ko/getting-started/quota</a></p>
<p>일간 10만 쿼터 초과시 유로 요금 발생
최대 45개 데이터 습득 가능</p>