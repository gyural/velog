<h1 id="1-문제제기">1. 문제제기</h1>
<p>현재 프로젝트에서 ELK를 통한 로깅을 사용하려고 합니다.</p>
<p>그렇게된다면 한 서버에서 docker-continer는     5개가 동시에 실행되게되고, 이에따라 서버에 성능에 영향을 미칠것은 분명해보입니다.</p>
<blockquote>
<p>따라서 직접 성능을 분석하고, 이에따른 결론을 내보겠습니다.</p>
</blockquote>
<h1 id="2-환경-세팅">2. 환경 세팅</h1>
<p>로컬환경을 그대로 사용해줄 것입니다.</p>
<ul>
<li>CPU : Apple silicon M3 - 8core</li>
<li>memory : 6gb</li>
<li>Virtual Machine</li>
<li>locust</li>
</ul>
<p>*<em>실행중인 docker container컨테이너
*</em>
*<em>with ELK
*</em></p>
<p><code>elastic-search</code> :  docker.elastic.co/elasticsearch/elasticsearch::8.7.1
<code>logstash</code> : docker.elastic.co/logstash/logstash
<code>kibana</code> : docker.elastic.co/kibana/kibana:8.7.1
<code>backend</code> : build with jdk21 spring-boot-app jar file
<code>DB</code> : postgres:15</p>
<p>*<em>without ELK
*</em>
<code>backend</code> : build with jdk21 spring-boot-app jar file
<code>DB</code> : postgres:15</p>
<hr />
<p>*<em>locust-setting
*</em>
<code>peak</code>: 100 <code>ramp-up</code> : 10</p>
<p>성능 측정용 API는 <a href="https://github.com/shipleaf/moyeobwayo-front">저번 프로젝트</a>에서 발생하는 병목지점 API 2개를 이용할 것입니다.</p>
<h1 id="3-결과">3. 결과</h1>
<h3 id="without-elk">without ELK</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/723b4ad1-9b12-4743-b19f-85951f47ad53/image.png" />
<img alt="" src="https://velog.velcdn.com/images/gyural/post/d8911bd2-e098-4f6a-a947-898143a3b585/image.png" /></p>
<p>RPS 50이며 95%요청이 0.1초내로 응답이 왔다</p>
<h3 id="with-elk">with ELK</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/a8359e33-d40e-4129-85e4-50db440bd3fa/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/401cd877-720b-4988-95b5-9e44d2828ed0/image.png" /></p>
<p>RPS는 유사하게 수렴하나 95%요청이 7~8초로 찍힌다...!! 90%는 220ms이므로 90%유저는 원할하게 이용하나 10%유저가 원할하지 못한 운영이 이루어지는 것을 알 수 있었다.</p>
<h1 id="4-결론">4. 결론</h1>
<p>일단 elastic-search의 최소 메모리 요구량은 2GB를 경험적으로 습득했다.
따라서 해당 ELK와 서버 DB를 돌릴려면
메모리 요구량은 약 8GB가 되어야하고,
결국 AWS 어드밴스 티어를 활용해야한다는 것이다.
<strong>비용은 한달 약 10만원이 EC2비용으로만 발생한다.</strong></p>
<p>--&gt; 따라서 초기 서버에서는 오버헤드라고 판단해 다른 방식으로 접근을 심화공부 해볼것이다.</p>
<blockquote>
<p>여러 조사를 해본결과</p>
</blockquote>
<p><code>S3 appender</code>와 <code>lambda function</code>을 사용해 fatal에러같은 치명적 에러가 발생했을 때 핸들링을 해볼 수 있을 것이다.(알림 받기 등..)
그렇다면 AWS프리티어 내에서 문제를 해결해볼 수 있을것이다.</p>