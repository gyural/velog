<h1 id="1-문제제기">1. 문제제기</h1>
<p>이전에 ELK로 로깅과 모니터링을 하기 위해서는 리소스 요구량 때문에 보류했었다...
이번에는 리소스 부담이 없고 기본 제공해주는 Spring admin을 공부하며,
로깅과 실시간 모니터링시 장단점을 분석해보겠다.</p>
<p>ref : <a href="https://www.youtube.com/watch?v=2i3KHs6YhWk&amp;t=718s">IMQA세미나 : Spring Boot Admin을 사용한 Spring Boot Application 모니터링
</a></p>
<h1 id="2-서버-모니터링-왜-필요할까">2. 서버 모니터링 왜 필요할까?</h1>
<p><code>1) 실시간 모니터링</code>
Spring Boot Actuator를 통해 확인할 수 있는 정보들을 실시간 대시보드 형태로 모니터링</p>
<p><code>2) 서버 장애 발생 시 알림</code>
Notification기능을 통해 서버 장애 발생 시 알림</p>
<p><code>3) 사전 분석</code>
사전 분석을 통해 성능 개선점을 찾거나 사전에 크래시 트래킹 가능</p>
<p>크래시 트래킹이란...
앱 실행중 발생하는 오류를 <strong>크래시라하며,</strong>
예) 런타임 오류, 메모리 부족, NullPointerException등..
이를 추적할 수 있는 기능을 말한다.</p>
<h3 id="1-실시간-모니터링">1. 실시간 모니터링</h3>
<p>주로 관심사 지표를 기준으로 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/661d3838-6e7f-45ca-ba7b-28a4d4a69e01/image.png" /></p>
<h3 id="2-서버-장애-발생-시-알림">2. 서버 장애 발생 시 알림</h3>
<p>Notification 기능을 사용해 서버가 Down되거나 상태가 변경되었을 때 알림을 받아 장애 대응이 가능해진다.
Mail, Slack, Telegram등을 지원한다.</p>
<h3 id="3-사전-분석">3. 사전 분석</h3>
<p>스레드 덤프, 데드락등 사전에 발생하는 문제를 감지할 수 있다.</p>
<h2 id="2-확인할-수-있는-지표">2. 확인할 수 있는 지표</h2>
<h3 id="1-프로젝트-빌드-정보-및-해당-git정보등-확인-가능">1. 프로젝트 빌드 정보 및 해당 git정보등 확인 가능</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/c680cd7f-d842-4f92-b675-10a1a406898e/image.png" /></p>
<h3 id="2-디스크-및-인프라-사용-상태-확인-가능">2. 디스크 및 인프라 사용 상태 확인 가능</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/fd77f37d-faf4-4480-8bda-75e26e42dd1e/image.png" /></p>
<h3 id="3-스레드-증가-및-활성화-상태-확인-가능">3. 스레드 증가 및 활성화 상태 확인 가능</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/2278990f-ae5a-4876-8c38-0fa2aa0e6305/image.png" />
활성화된 스레드 개수가 최대 활성 스레드 개수와 같은지 확인해볼 수 있다..!!</p>
<h3 id="4-성능-지표-확인-예시">4. 성능 지표 확인 예시</h3>
<p>특정 시간 대역에서 스레드가 증가/스레드가 반납되지 않는 경우...
특정스레드의 Lock이 걸려있는채로 유지가 되는 상황인 것이고,
주로 원인으로는 DB쿼리속도 저하, Dis I/O이다.
현상 분석을 위해 스레드 덤프를 확인
<img alt="" src="https://velog.velcdn.com/images/gyural/post/4213ca15-38ec-47e1-a326-3e9444bb9965/image.png" /></p>
<h3 id="5-cpu-지표확인">5. CPU 지표확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/61008ef7-6208-403d-b8e4-ba24979481f5/image.png" /></p>
<p>가비지 컬렉션이란..???
특정 메모리공간에서 필요없는 객체 할당을 해제해주는 것을 말한다.
아래 지표에서 GC(가비지 컬렉션)를 확인할 수 있다.
일반적으로 GC가 커질 수록 CPU에 부하를 주게된다.</p>
<h3 id="6-heap메모리-지표확인">6. Heap메모리 지표확인</h3>
<p>Spring앱에서 가장 중요한 지표가 될 수 있다..!!!
<img alt="" src="https://velog.velcdn.com/images/gyural/post/c4313df9-53ea-431c-aca1-96026c567c0b/image.png" /></p>
<p><code>실제 예시</code>
<strong>1) GC가 정상적으로 발생했을 때
**
**heap메모리가 할당과 해제를 반복</strong>해 <strong>뾰족한 그래프형태</strong>를 보여준다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/b59f49ea-cd39-4a74-8bd5-920dd323ca31/image.png" />
*<em>2) GC가 정상적으로 발생하지 않았을 때
*</em>
그래프가 큰 변화가 없고 증가하는 형태를 보인다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/382ce60b-18e1-46fe-bf28-cf5437dacbdd/image.png" /></p>
<h3 id="7-non-heap메모리-지표확인">7. Non-Heap메모리 지표확인</h3>
<p>해당 영역은 크게 Stack영역과 Metaspace영역으로 나누어져있다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/cc559fdf-f13b-4cef-8765-6b5d9ae7a100/image.png" /></p>
<h3 id="8-jvm-thread">8. JVM thread</h3>
<p>활성화 스레드 최대스레드등 스레드의 상태별 갯수를 파악 가능하다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/b3a88e40-36fe-42ea-86c1-1a8282beb875/image.png" /></p>
<h3 id="9-로그파일-지표">9. 로그파일 지표</h3>
<p>실시간 로그를 파악이 가능하며, 로그레벨에 따른 로그 파악도 가능해진다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/78935566-7a27-4a4a-a169-5229a799e21e/image.png" /></p>
<hr />
<h2 id="3-스레드-힙-분석이-왜-필요한-것일까">3. 스레드 힙 분석이 왜 필요한 것일까?</h2>
<p><code>일반 대응방식</code>
기존방식으로 로그만으로 시스템 성능을 확인한다면, 일시적인 프로세스 죽이기와 서버 스케일로 만 해결할 수 있다. 이렇게 된다면 특정 병목지점에대한 해결방안이 아니기때문에 해당 지점에 대한 성능이슈는 또 발생한다.
<code>스레드 분석을 통한 대응방식</code>
스레드를 분석하여 속도저하에 명확한 근거를 판단할 수 있다.</p>
<h2 id="4-spring-boot-admin-분석">4. Spring-boot Admin 분석</h2>
<p><code>1) 손쉬운 구성</code>
다른 모니터링 툴에 비해 구성이 간편하다
<code>2) 가볍지만 강력한 기능들을 제공</code>
다른 모니터링 툴에 비해 가벼운 편이지만, 유료 모니터링 툴과 비교할 정도로 강력한 기능들을 제공
<code>3) 다양한 시스템 모니터링 지원 X</code>
Redis mySqL등의 시스템의 모니터링같은 부분은 지원을 하지 않는다.</p>