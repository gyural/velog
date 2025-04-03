<h1 id="1-새로운-문제-발생">1. 새로운 문제 발생</h1>
<p>기존에는 entry_main_record라는 단일 테이블만 읽어오면 됐지만...!!</p>
<p>실제로는 이 entry로부터 시작하는 <code>결제정보</code> ＋ <code>출차정보</code> 를 불러올 수 있어야한다.</p>
<h1 id="2-플로우-수정">2. 플로우 수정</h1>
<h2 id="기존-플로우">기존 플로우</h2>
<p>Section1 새로운 데이터 동기화</p>
<p>1) Batch Size에 맞는 새로운 데이터 주차장 DB에서 입차 기록 READ 🔍
2) 위의 입차 데이터의 대응되는 출차기록 주차장 DB에서 READ 🔍
3) 대응 되는 짝끼리 DTO안에서 합 🤝
4) 해당 DTO리스트를 APP Server로 POST ✉️
5) 응답으로 유효한 데이터(회원 데이터)만 RESPONSE ✉️
6) 유효한 데이터 중 업데이트가능한 == 이후 출차가능한 데이터만 MongoDB에 WRITE📝</p>
<p>Section2 Updatable 데이터 동기화</p>
<p>1) MongoDB에서 업데이트가능한(출차가능) DATA 전체 READ 🔍
2) 해당 엔티티리스트의 ID리스트를 통해 MS DB(최신 데이터)를 READ 🔍
3) 이전 데이터(Mongo) 최신데이터(MS) 비교를 통해 업데이트 된 데이터 DETECT🕵️
4) DETECT된 데이터 리스트를 APP Server로 POST ✉️
5) 정상 동기화 완료후 저장된 ID 리스트를 RESPONSE ✉️
6) RESPONSE에 포함된 ID를 가지는 엔티티 MongoDB에서 DELETE 🗑️</p>
<hr />
<h2 id="새로운-플로우">새로운 플로우</h2>
<h3 id="section1-새로운-데이터-동기화">Section1) 새로운 데이터 동기화</h3>
<p>1) Batch Size에 맞는 새로운 데이터<code>Entry_main_record</code>테이블 READ 🔍
2) 위의 입차 데이터의 id를 외래키로 가지는대응되는 출차기록 <code>Exit_main_record</code> 에서 READ 🔍
3) 
3) 위의 입차 데이터의 id를 외래키로 가지는대응되는 결제기록 <code>Payment_main_record</code> 에서 READ 🔍
4) 해당 데이터 리스트를 조합한 DTO리스트를 APP Server로 POST ✉️
5) 응답으로 유효한 데이터(회원 데이터)만 RESPONSE ✉️
6) 유효한 데이터 중 업데이트가능한 == 이후 출차가능한 데이터만 MongoDB에 WRITE📝</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/dbd90cc3-2a0c-4583-86bd-fb787a7f99e2/image.png" /></p>
<h3 id="section2-새로운-데이터-동기화">Section2) 새로운 데이터 동기화</h3>
<p>1) MongoDB에서 업데이트가능한(출차가능) DATA 전체 READ 🔍
2) 해당 엔티티리스트의 ID리스트를 통해 MS DB(최신 데이터)를 READ 🔍
    -&gt; 이때 Pay_table Exit테이블 모두 읽어와야함
3) 이전 데이터(Mongo) 최신데이터(MS) 비교를 통해 업데이트 된 데이터 DETECT🕵️
4) DETECT된 데이터 리스트를 APP Server로 POST ✉️
5) 정상 동기화 완료후 저장된 ID 리스트를 RESPONSE ✉️
6) RESPONSE에 포함된 ID를 가지는 엔티티 MongoDB에서 DELETE 🗑️</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/7b9a3908-11eb-4da8-b1e9-6253571a411a/image.png" /></p>
<hr />
<h1 id="3-결론">3. 결론</h1>
<p>참고해야하는 테이블이 더 생겨서 플로우가 훨씬 어려울 것이라 예측했으나
막상 크게바뀐거는 없었고 코딩만 어려워진거 같다....</p>