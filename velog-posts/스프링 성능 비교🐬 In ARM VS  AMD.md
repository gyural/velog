<h1 id="1-jvm-에러">1. JVM 에러</h1>
<p>최근 arm환경(Mac M3)에서 다음과 같은 플로우로 빌드를 했지만 에러가 났었다....</p>
<p><strong>명령어</strong></p>
<pre><code class="language-bash">./gradlew clean build
docker compose up -d</code></pre>
<p><strong>docker file</strong></p>
<pre><code class="language-docker">FROM openjdk:21

ARG JAR_FILE=build/libs/*.jar

COPY ${JAR_FILE} app.jar

ENTRYPOINT [&quot;java&quot;, &quot;-jar&quot;, &quot;app.jar&quot;]</code></pre>
<p><strong>docker compose 일부</strong></p>
<pre><code class="language-yaml">  backend:
    container_name: honorsParking
    depends_on:
      - db
      - redis
    platform: linux/amd64

    ...</code></pre>
<p>하지만 이렇게 실행했더니...!!! <em>JVM 에러가 발생한다..!!</em>
확실한 포인트를 찾지는 못했지만 docker image platform을 ARM으로 바꿨더니 해당에러가 한번도 발생안했다....
고로 한가지 가설을 세울 수 있었다.</p>
<blockquote>
<p>이미 빌드한 이미지를 amd로 바꿔서 만들게 된다면 Docker-DeskTop의 에뮬레이터에서 돌아간다.
따라서 멀티 플랫폼(AMD, ARM)에서 빌드한 후 동작하게 하는것이 안전하다.</p>
</blockquote>
<h3 id="buildx를-활용한-멀티-플랫폼-빌드">buildX를 활용한 멀티 플랫폼 빌드</h3>
<p>buildX를 사용하면 기존 이미지 빌드와는 다르게 ARM, AMD 멀티 플랫폼에서 빌드가 완료된다..!!
이 때 <code>buildX</code>라는 기술을 사용해줄 수 있다.
흐름은 아래와 같다.</p>
<p>1) buildX 활성화</p>
<pre><code>docker buildx create --use</code></pre><p>2) Task 실행</p>
<pre><code>./gradlew dockerBuildxMultiPlatform</code></pre><p>build.gradle task부분</p>
<pre><code class="language-java">tasks.register(&quot;dockerBuildxMultiPlatform&quot;, Exec) {
    dependsOn bootJar

    group = &quot;docker&quot;
    description = &quot;Build and push multi-platform docker image using buildx&quot;

    def imageName = &quot;gyural/honors-parking-buildx:latest&quot;

    commandLine &quot;docker&quot;, &quot;buildx&quot;, &quot;build&quot;,
            &quot;--platform&quot;, &quot;linux/amd64,linux/arm64&quot;,
            &quot;-t&quot;, imageName,
            &quot;--push&quot;,
            &quot;.&quot;

    doFirst {
        println &quot;Building multi-platform Docker image: ${imageName}&quot;
    }
}</code></pre>
<p>3) 이때 docker compose 파일에 platform이 명시되어야 속도가 증가함!!</p>
<pre><code class="language-yaml">platform : linux/arm64</code></pre>
<hr />
<h1 id="arm-spring-vs-amd-spring-성능-비교">arm Spring VS amd Spring 성능 비교</h1>
<p>arm이 훨씬 효율이 좋은 최근 아키텍처라고 한다..!! 따라서 두가지 플랫폼중 어떤 플랫폼에서 성능이 우세할까 의문이 생겼고 따라서 비교 실험을 해보고자 한다..!!</p>
<h2 id="0-공통">0) 공통</h2>
<ul>
<li>docker container</li>
<li>Redis container</li>
<li>MySql contianer</li>
<li>Spring boot 3.x.x JAVA 21</li>
</ul>
<hr />
<h2 id="1--amd-환경">1)  AMD 환경</h2>
<ul>
<li>T2 Micro + Ubuntu 22.04 LTS</li>
<li>Mem 1 + 2(Swap)</li>
<li>Vcpu :1</li>
</ul>
<h3 id="결과-1-병목-api-포함">결과 1) 병목 API 포함</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/c8a5dd74-c920-4945-a20d-d65b466950a6/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/0cf4b62f-ea18-4553-8592-fbfe02fb1384/image.png" /></p>
<p>400명 유저가 있을 때 1초 내외로 99%유저에게 보장하지만... 비밀번호 변경 API에서 다른 API보다 2배의 latency가 발생 따라서 해당 API를 제외하고 다시 측정 해보겠다..!!</p>
<h3 id="결과-2-병목-api-1개-제외">결과 2) 병목 API 1개 제외</h3>
<p>API 1개만 제외했더니 1000명의 유저에 대해서 버틸 수(1초 이내의 99%유저 응답)가 있었다...!!!
해당 부하에서는 유별나게 Response Time이 길었던 API는 보이지 않는다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/900c8817-00f5-46fe-9fba-371184f6e536/image.png" />
<img alt="" src="https://velog.velcdn.com/images/gyural/post/bf2250d5-91c6-47f9-897f-e0adac5c3ca4/image.png" /></p>
<hr />
<h2 id="2-arm">2. ARM</h2>
<ul>
<li>T4g Micro + Ubuntu 22.04 LTS</li>
<li>Mem 1 + 2(Swap)</li>
<li>Vcpu :2</li>
</ul>
<h3 id="결과">결과</h3>
<p>약 1100명 정도까지 99%유저에게 1초 이하의 응답을 보장했음
약 10%의 유저를 더 잘 버틴다고 볼 수 있음...!!</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/4aae66b3-7d3c-45f1-bf96-98cc6f090498/image.png" />
<img alt="" src="https://velog.velcdn.com/images/gyural/post/270692a7-e25e-4a13-a0b3-8b62b2f3d83b/image.png" /></p>
<h2 id="amd-arm-결론">AMD ARM 결론</h2>
<p>두개의 칩이 메모리는 같지만 CPU스펙이 다릅니다.
AMD : vCpu 1, ARM : vCpu 2
그런점에서는.... ARM이 좀더 성능이 우세했다고 보기는 힘든 것 같습니다.
AMD칩에서도 동일한 스펙인 인스턴스가 있다면 명확했겠지만 
해당인스턴스는 AWS내에 존재를 하지 않기 때문에 비교가 불가능함...</p>
<h2 id="결국">결국...</h2>
<p>ARM은 일단 프리티어가 적용이 안되기때문에 성능의 큰 차이가 없으며 프리티어가 가능한 AMD 환경을 쓰는것이 더 좋아보임..!!</p>
<hr />
<h2 id="추가공부-필요한점">추가공부 필요한점</h2>
<p>1) SerVer에 웜업이 필요한 이유
2) 웜업에 대해서는 어떻게 측정을 해야하나??
    -&gt; 도메인을 고려? 아니면 어떻게??</p>