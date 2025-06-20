<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/f15f4af1-1f14-410c-89ac-50648d729f6c/image.png" /></p>
<h1 id="1-로드벨런싱-도입-배경">1. 로드벨런싱 도입 배경</h1>
<p>현재 나는 <strong>&quot;프리티어로 10000명의 사용자 감당하기&quot;</strong>에 도전중이다..!!!
이전 서버는 한개의 컨테이너 서버였고 약 <strong>900명의 사용자</strong>를 버티며 <strong>RPS는 450</strong> 으로 측정 되었다
가장 서버성능을 획기적으로 올리는 거는 서버를  막연히 늘리는 거라고 생각했다.
따라서 많이 사용하는 엔진엑스 로드밸런싱을 도전했고 그 과정에서 시행착오와 인상적인 부분을 정리해볼것이다.... </p>
<hr />
<h1 id="2-이전-단일-컨테이너-아키텍처">2. 이전 단일 컨테이너 아키텍처</h1>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/d9cf2c81-9e5a-4e02-933c-905e6e028874/image.png" /></p>
<h4 id="1-ec2--t2microcpucore1--mem-1gig--2gigswap">1. EC2 : T2Micro(Cpu(core1) + Mem 1Gig + 2Gig(Swap))</h4>
<h4 id="2-nginx--ec2-임베디드">2. NginX : Ec2 임베디드</h4>
<p>모든 인프라(DB, Redis, Server)가 도커 컨테이너 위에서 동작하고 엔진엑스만 Ec2 임베디드이다.</p>
<h3 id="프리티어로-여러개의-ec2를-어떻게-해">프리티어로 여러개의 Ec2를 어떻게 해??</h3>
<p>기본적으로 AWS는 2개이상의 Ec2부터 유료전환💵이다...</p>
<blockquote>
<p>따라서 프리티어 계정을 많이 만들어 ScaleOut + 엔진엑스 로드밸런싱을 해보려고 한다..!!</p>
</blockquote>
<hr />
<h1 id="3-nginx-docker화">3. NginX Docker화</h1>
<p>기존에 임베디드로 관리하는 거보다 유연한 Docker컨테이너로 NginX를 관리해보려고 한다.
(<a href="https://www.youtube.com/watch?v=J9jKKeV1XVE">참고 강의</a> -&gt; 매우 좋음 👍)</p>
<p>강의만 잘 따라 간다면 문제없이 https로 Ec2를 관리 및 docker-compose로도 가능할 것이다..!!</p>
<p>이후 로드벨런싱을 위해 <code>nginx.conf</code> 파일의 upstream속성만 넣어주면 된다..!!</p>
<p>내 <code>nginx.conf</code></p>
<pre><code class="language-js">worker_processes 1;

events {
  worker_connections 1024;
}

http {
  server_tokens off;
  charset utf-8;

  upstream honorsParkingBackend {
      server honorsParking-master:8080;
      #server honorsParking-slave:8081;
      (... ip값 있어서 제외!!)
    }

  server {
    listen 80 default_server;

    server_name _;

    location ~ /.well-known/acme-challenge/ {
          root /var/www/certbot;
        }

    return 301 https://$host$request_uri;
    }

    server {
      listen 443 ssl http2;

      ssl_certificate /etc/letsencrypt/live/gyural.shop/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/gyural.shop/privkey.pem;
      server_name gyural.shop;
      root /var/www/html;
      index index.php index.html index.htm;

      location / {
            proxy_pass http://honorsParkingBackend;
      }

      location ~ /.well-known/acme-challenge/ {
                root /var/www/certbot;
      }

    }



  }
</code></pre>
<hr />
<h1 id="4-아키텍처-1️⃣">4. 아키텍처 1️⃣</h1>
<p>첫번째 성능을 측정한 아키텍처이다. <strong>3개의 Server</strong>를 사용한 것이 특징이다.</p>
<h2 id="구조-상세">구조 상세</h2>
<h3 id="다이어-그램">다이어 그램</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/5c9e9db1-31e4-4cdb-8f1c-dd7ebcedefb0/image.png" /></p>
<h3 id="구성-요소">구성 요소</h3>
<p>** Ec2 1️⃣ : NginX + Spring-boot + Redis + MySQL
**
<strong>Ec2 2️⃣ : Spring-boot x 2</strong></p>
<h2 id="성능">성능</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/a8c30270-2609-4a15-a177-7092f34494d5/image.png" /></p>
<p><strong><code>User</code> : 1100
<code>RPS</code> : 500 ~ 510</strong></p>
<p>한개 컨테이너 서버보다 900 -&gt; 1100명 약 20% 성능이 증가하였다...
하지만 이상하지 않은가?
서버가 3개이면 900 -&gt; 900 * 3 == 2700이 되기를 기대했는데 그러지 못했다...!!</p>
<blockquote>
<p>결국 병목이 발생한다는 것이었다..!!!
따라서 병목을 찾아보겠다...!!</p>
</blockquote>
<h2 id="현재-병목에-여러가지-가설들">현재 병목에 여러가지 가설들...!!</h2>
<p><strong>1) 리소스 부족</strong>
모니터링 결과 NginX만 CPU점유율 70퍼 이상 차치하고 서버 컨테이너 1개도 70퍼 이상을 차지할 때가 있었다..!!
그러면 <code>T2-Micro</code> 스펙으로는 1개의 서버를 할당해주는게 적당해 보인다.</p>
<p>*<em>2) DB및 Redis 컨테이너 *</em>
현재 한 EC2에서 엔진엑스 + MySQL + Redis + Srping이 다 돌아간다..!!
즉 DB와 Redis를 바깥으로 뺀다면 서버나 엔진엑스의 리소스가 남게 될 것으로 예상된다.</p>
<h3 id="solution">Solution</h3>
<blockquote>
<p>두 가지 가설을 세우기 위해서의 전략은 2가지 이다..!!
<strong>1) Ec2인스턴스 하나당 서버 컨테이너 최대 1개로 둔다
2) DB 및 Redis를 외부에 위치시킨다.</strong></p>
</blockquote>
<hr />
<h1 id="5-아키텍처-2️⃣">5. 아키텍처 2️⃣</h1>
<h2 id="아키텍처">아키텍처</h2>
<h3 id="다이어그램">다이어그램</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/41e3d677-38df-4b0c-a736-e680eff8ab09/image.png" /></p>
<h3 id="구성-요소-1">구성 요소</h3>
<blockquote>
<p>Redis-Cloud : Redis
AWS RDS  : mySQL
Ec2 1️⃣ : NginX
Ec2 2️⃣ : Spring Boot</p>
</blockquote>
<h2 id="성능-1">성능 <img alt="" src="https://velog.velcdn.com/images/gyural/post/fcecd5d7-535c-4572-a1f1-c979c2f65330/image.png" /></h2>
<p><strong><code>User</code> : 1200
<code>RPS</code> : 650</strong></p>
<h3 id="의견">의견</h3>
<p>기존에 엔진엑스가 돌아가던 EC2에서 DB와 Redis를 각각 클라우드에서 실행시켰더니 단일 서버 컨테이너로 서버 성능이 압도적으로 상승했다.
_따라서 첫번째 병목은 엔진엑스 Ec2에서 다른 인프라사용으로 인한 엔진엑스 자원 부족이었던 것이다.
또 해결까지 해줬다..!!
_</p>
<hr />
<h1 id="6-아키텍처-3️⃣">6. 아키텍처 3️⃣</h1>
<p>단일 서버로도 1500명을 감당해서 2개의 서버로 한다면 엄청난 성능 상승을 기대하고 측정해본다.
서버 컨테이너 2개를 로드벨런싱을 해보겠다.</p>
<h2 id="아키텍처-1">아키텍처</h2>
<h3 id="구성-요소-2">구성 요소</h3>
<blockquote>
<p>Redis-Cloud :Redis
Ec2 1️⃣ : NginX
Ec2 2️⃣ : Spring Boot
Ec2 3️⃣ : Spring Boot
RDS : mySQL</p>
</blockquote>
<h2 id="성능-2">성능<img alt="" src="https://velog.velcdn.com/images/gyural/post/9857cdba-f46d-45c6-aff6-71247a0bc636/image.png" /></h2>
<h3 id="의견-1">의견</h3>
<p>컨테이너를 두개로 늘려도 성능이 좋아지지는 않았다. 따라서 엔진엑스의 자원사용량을 찾아봤더니
CPU사용율이 75% ~ 95%사이로 엔진엑스 EC2 컴퓨터에서 병목이 발생으로 예상된다...!!</p>
<p><em>이 말은 프리티어인 컴퓨터로는 로드벨런싱의 수혜를 보기는 어렵다는 뜻이다.</em></p>
<hr />
<h1 id="7-아키텍처-4️⃣">7. 아키텍처 4️⃣</h1>
<p>엔진엑스 병목을 해소하기 위해 엔진엑스 EC2를 스케일 업을 해보겠다..!!
이번에는 CPU 코어 수가 많은 T2.medium으로 로드밸런싱을 했고 결과는 좋았다..!!
이번에야말로 의미있는 로드밸런싱의 성능상승의 결과가 나왔다...!!!</p>
<h3 id="서버-2개-일-때-결과">서버 2개 일 때 결과<img alt="" src="https://velog.velcdn.com/images/gyural/post/391b412d-f290-42a9-ae38-6d5704a73c5e/image.png" /></h3>
<p><strong>RPS : 900 ~ 950
User : 2500</strong></p>
<p>사용자는 2000명 정도까지 버틸만 한거 같다..!!</p>
<h3 id="구성요소">구성요소</h3>
<blockquote>
<p>Ec2 1️⃣(T2.medium) --&gt; NginX 
Ec2  2️⃣ --&gt; Spring Boot
Ec2 3️⃣ --&gt; Spring Boot
Ec2 4️⃣ --&gt; Spring Boot</p>
</blockquote>
<p>이번에는 서버를 3개로 돌려보겠다..!!
하지만 결과는 2개린 아키텍처-3 보다 성능이 하나도 좋아지지 않았다..!!
여기서도 엔진엑스 병목이라고 판단해 vcpu가 4개인 마지막 스케일업한 아키텍처를 성능 테스트해보겠다..!!</p>
<h3 id="서버-3개-일-때-결과성과-x">서버 3개 일 때 결과(성과 X) <img alt="" src="https://velog.velcdn.com/images/gyural/post/03a758f0-5338-4e0c-aad0-efe62c2ac584/image.png" /></h3>
<hr />
<h1 id="10-아키텍처-5️⃣">10. 아키텍처 5️⃣</h1>
<p>엔진엑스 Ec2를 4vCpu인 T2.xlarge를 사용했는데 똑같은 문제인
2개 -&gt; 3개 서버를 늘렸을 때 성능 변화가 없었다...!!</p>
<h3 id="서버-2개">서버 2개</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/5f7ceb49-b649-4f98-8498-9fa881dcbeef/image.png" /></p>
<h3 id="서버-3개">서버 3개</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/bb2d00d3-fade-4d74-b5e8-db7a6ab44b8c/image.png" /></p>
<h3 id="의견-2">의견</h3>
<p>좌절감.. 이 느껴졌다..!! 비싼 T2 xLarge를 써도 문제가 해결 되지 않았다..!!!
결국 병목은 엔진엑스일 것이고 왜 엔진엑스 내부에서 병목이 발생할지를 고민해본 결과..!! 핵심 열쇠는 바로..!! <code>Keep-Alive</code></p>
<hr />
<h1 id="11-keep-alive">11. Keep-Alive</h1>
<p>keep-alive는 연결 지연으로 TCP 통신에서 연결 생성 종료에서 발생할 수 있는 병목을 해결해줄 수 있는 기술이다...!!
또한 엔진엑스에서 upstream으로 연결된 백엔드 서버는 기본적으로 keep-alive가 활성화되어있지 않다..!!</p>
<p>이제 정말 마지막 기대를 안고 keep-alive를 가진 아키텍처를 고안해보고자 한다..!!!</p>
<h1 id="12-아키텍처-6️⃣feat-keep-alive">12. 아키텍처 6️⃣(feat. Keep Alive)</h1>
<p>엔진엑스 Ec2는 T2.medium이다.</p>
<p>먼저 keep-alive의 부재로 인한 병목이 있다는 가설을 세운 후 실험을 해봤다 서버는 1-&gt;3개까지 차근차근 늘리면서 성능을 테스트 할 것이다.</p>
<h3 id="서버-1개-아키텍처-결과">서버 1개 아키텍처 결과<img alt="" src="https://velog.velcdn.com/images/gyural/post/f0b42c5d-1fae-4a38-bf3c-f3bc087da7f3/image.png" /></h3>
<p>RPS가 900이 넘었다 이전에는 1개의 서버만으로는 RPS가 최대 700까지밖에 측정이 안되었지만 1개에 서버에서도 효과가 나타났다..!!
최종적으로 3개의 로드밸런싱에서도 효과가 있기를 기대해본다...!!!</p>
<h3 id="서버-2개-아키텍처-결과">서버 2개 아키텍처 결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/1d350006-0e9a-47df-9cee-39ca1dbe7f98/image.png" /></p>
<p>하지만 기대는 무너졌다..!!
서버를 두개 돌렸는데도 RPS는 차이가 아예없었다..!! 따라서 Nginx.conf의 <code>worker_connections</code>를 1024 -&gt; 2048로 올렸더니..
<img alt="" src="https://velog.velcdn.com/images/gyural/post/329883a6-a05f-4808-914b-770783659875/image.png" /></p>
<p>*<em>RPS가 1000 -&gt; 1200으로 올랐다..!!
*</em></p>
<h3 id="서버-3개-아키텍처-결과">서버 3개 아키텍처 결과</h3>
<p>측정은 했지만 항상 서버 2개 -&gt; 3개 에서의 성능상승은 발생하지 않는다....
이 병목을 찾아보려고 열심히 했지만 모든 엔진엑스 파라메터도 수정하고 RDS 모니터링에도 흔적을 찾을 수는 없었다..</p>
<hr />
<h1 id="정리">정리</h1>
<h3 id="1-엔진엑스-로드밸런싱하면-성능-오른거야">1. 엔진엑스 로드밸런싱하면 성능 오른거야??</h3>
<p>T2.micro 엔진엑스는 서버를 2개로 확장해도 <strong>엔진엑스에서 병목이 발생함</strong>
T2.medium 엔진엑스는 서버를 2개로 확장하면 <strong>RPS가 750 -&gt; 1000으로 30% 상승함</strong>
서버를 2개 -&gt; 3개로 확장했을 때는 vCpu4인 T2.xlarge에서도 <strong>성능은 안올랐음</strong></p>
<p>-성능은 오른다는 거는 이론적으로 실험적으로 맞다.. 
--&gt; 하지만 서버를 확장할 때마다 <strong>병목이 발생하고 해당 병목을 해결하는 Skill이 필요하다!</strong></p>
<h3 id="2-이전-아키텍처별-성능-총정리">2. 이전 아키텍처별 성능 총정리</h3>
<p>먼저 서버 컴퓨터는 모두 프리티어인 Ec2(T2.micro)를 사용
엔진엑스 컴퓨터만 변화가 있었습니다...!!</p>
<table>
<thead>
<tr>
<th>아키텍처</th>
<th>EC2 1️⃣ (Nginx)</th>
<th>EC2 2️⃣ (SpringBoot)</th>
<th>EC2 3️⃣ (SpringBoot)</th>
<th>EC2 4️⃣ (SpringBoot)</th>
<th>RDS</th>
<th>Redis</th>
<th>성능 (Users → RPS)</th>
<th>특징</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>Nginx+Spring+Redis+MySQL(T2.medium)</td>
<td>T2.micro SpringBoot x2</td>
<td>-</td>
<td>-</td>
<td>EC2 내부</td>
<td>EC2 내부</td>
<td>1100 → 500~510</td>
<td>Nginx 자원 부족, 많은 인프라가 EC2-1에 집중됨</td>
</tr>
<tr>
<td>2</td>
<td>Nginx(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>-</td>
<td>-</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>1200 → 650</td>
<td>Nginx에 자원을 집중하자 성능 향상</td>
</tr>
<tr>
<td>3</td>
<td>Nginx(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>-</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>1600 → 680~700</td>
<td>서버 추가했지만 성능 상승 미미, Nginx CPU 90% 이상</td>
</tr>
<tr>
<td>4-1</td>
<td>Nginx (T2.medium)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>-</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>2500 → 900~950</td>
<td>Nginx 스케일 업으로 성능 상승</td>
</tr>
<tr>
<td>4-2</td>
<td>Nginx (T2.medium)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>2500 → 900~950</td>
<td>서버 한 대 더 추가했지만 성능 변화 없음</td>
</tr>
<tr>
<td>5</td>
<td>Nginx (T2.xlarge)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>2500 → 1200</td>
<td>vCPU 증가로 인한 Nginx 성능 20% 향상</td>
</tr>
<tr>
<td>6</td>
<td>Nginx (T2.medium)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>SpringBoot(T2.micro)</td>
<td>AWS RDS</td>
<td>RedisCloud</td>
<td>3000 → 1200</td>
<td>Nginx 설정 튜닝(worker_connections, keep-alive 등)으로 성능 개선</td>
</tr>
</tbody></table>
<h3 id="3-성능-상승에-의미-있었던-파라메터">3. 성능 상승에 의미 있었던 파라메터</h3>
<p><strong>1) Nginx EC2에는 Nginx만..
*<em>Nginx자체가 가벼운 연산을 하더라도 결국 여러개의 서버의 관제탑역할을 하기 때문에 자원을 모두 사용할 수 있게 해줘야한다.
*</em>2) Nginx Ec2에는 vCPU2이상 사용</strong>
vCPU가 2개 이상이어야지 2개이상 로드밸런싱에서 의미있는 성능상승이 나타났다.
<strong>3) worker_connections 및 keep-alive</strong>
Nginx EC2 모니터링 이후 부하가 걸리고 성능이 안오를 때 Mem의 공간의 여유가 있다면 두 파라매터를 최대한 올리는게 좋다..!!
-&gt; Nginx는 Mem를 많이 사용안하기 때문에 오버헤드도 발생 X</p>
<hr />
<h1 id="기타-부하테스트-도중-이슈">기타. 부하테스트 도중 이슈</h1>
<h3 id="1aws-기타-부대-비용-발생">1.AWS 기타 부대 비용 발생</h3>
<p><code>Prob1</code> RDS 퍼브릭 엑세스(IPv4 할당 비용)
<code>Prob2</code> Ec2간 통신(엔진엑스 - 서버) 1Gig초과당 요금 발생
<code>Prob3</code> 모든 EC2가 병목이 없는데도 성능이 좋아지지가 않음 가설로 로그인을 새로할때마다 세션이 증가하고 레디스 저장공간을 위협할 정도로 세션이 늘어남
<code>Prob3</code> 서버가 Failure가 발생하면 부하가 걸리는 상태에서는 계속해서 failure가 발생함 -&gt; 서버의 오류가 있다면 로그를 딸깍으로 확인할 수 있으면 좋을 거 같음</p>
<hr />
<h3 id="prob1-해결-방법"><code>Prob1</code> 해결 방법</h3>
<p>먼저 RDS 퍼블릭 엑세스 비용이 켜두기만 해도 시간단위로 발생한다.
-&gt; 부하테스트 시에만 사용하고 아키텍처 설정이나 비 개발 시에는 꺼두기로 한다
-&gt; 이를 쉽게 끄고 킬수있게 고안해내야한다
-&gt; 현재 아키텍쳐 <code>.env</code>파일 내부에 DB의 Private-url과 Public-url을 주석처리/비처리 해서 해결할 수 있다</p>
<p><code>예시 RDS (퍼블릭 활성화 시)</code></p>
<pre><code class="language-py">#DB_URL=jdbc:mysql://ec2-private.compute.amazonaws.com:3306/parking
DB_URL=jdbc:mysql://db-instance-public-samle.rds.amazonaws.com:3306/parking
#DB_USERNAME=user
DB_USERNAME=user</code></pre>
<p>이런식으로 주석을 전환하고 docker컨테이너를 껐다가 꺼준다.</p>
<h3 id="prob2-해결-방법"><code>Prob2</code> 해결 방법</h3>
<p>먼저 RDS 퍼블릭 엑세스 비용이 켜두기만 해도 시간단위로 발생한다.</p>
<p>30기가 초과시 2.7달러 발생</p>
<p>이런식으로 주석을 전환하고 docker컨테이너를 껐다가 꺼준다.</p>