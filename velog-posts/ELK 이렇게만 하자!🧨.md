<h1 id="1-문제-제기">1. 문제 제기</h1>
<p>백엔드 프로젝트 진행중에 로깅을 저장과 효과적시각화를 위해 ELK 스택을 채택하려고한다.</p>
<hr />
<h1 id="2-elk가-뭐야">2. ELK가 뭐야?</h1>
<blockquote>
<p><strong>E</strong>(elastic-search)<strong>L</strong>(logstash)<strong>K</strong>(kibana)</p>
</blockquote>
<p>로깅을 외부 파일로 저장하는 것이 아닌 <strong><code>elastic-seacrh</code></strong>를 통해 저장 후 <strong><code>kibana</code></strong>를 통해 시각화를 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/d7248a19-045d-4292-b448-0ddebf5f16ff/image.png" /></p>
<hr />
<h1 id="3-아키텍처">3. 아키텍처</h1>
<p><strong>도커 이미지 기반</strong>으로 사용할 것이며
각 이미지는 아래와 같다.</p>
<ul>
<li><code>Server</code> : spring-boot + java21 </li>
<li><code>Elastic-Search</code> : elasticsearch:7.17.16</li>
<li><code>logstach</code> : docker.elastic.co/logstash/logstash:7.17.16</li>
<li><code>kibana</code> : kibana:7.17.16</li>
</ul>
<hr />
<h1 id="4-docker-compose세팅">4. docker-compose세팅</h1>
<p>각 envirionment세팅은 docker-hub에 없었다. ㅠㅠㅠ</p>
<ol>
<li><strong><code>docker-compose.yml</code></strong><pre><code class="language-yml">networks:
elk:
 driver: bridge
</code></pre>
</li>
</ol>
<p>services:</p>
<p>  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: example
      MYSQL_USER: example
      MYSQL_PASSWORD: example
    ports:
      - &quot;3306:3306&quot;
    # Elastic search 설정
  elasticsearch:
    container_name: elasticsearch
    image: elasticsearch:7.17.16
    ports:
      - &quot;9200:9200&quot;
      - &quot;9300:9300&quot;
    volumes:
      - ./elasticsearch:/usr/share/elasticsearch/data
    # Elastic search 계정정보
    environment:
      discovery.type: single-node
      xpack.security.enabled: false
      xpack.security.transport.ssl.enabled: false
      xpack.ml.use_auto_machine_memory_percent: true</p>
<h1 id="elastic_password-elastic123">ELASTIC_PASSWORD: &quot;elastic123!@#&quot;</h1>
<pre><code>networks:
  - elk</code></pre><h1 id="logstash-설정">Logstash 설정</h1>
<p>  logstash:
    container_name: logstash
    image: docker.elastic.co/logstash/logstash:7.17.16
    ports:
      - &quot;5001:5001&quot;
      - &quot;9600:9600&quot;
    volumes:
      - ./logstash/pipeline/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    depends_on:
      - elasticsearch
    networks:
      - elk</p>
<h1 id="kibana-설정">Kibana 설정</h1>
<p>  kibana:
    container_name: kibana
    image: kibana:7.17.16
    ports:
      - &quot;5601:5601&quot;
    depends_on:
      - elasticsearch
    volumes:
      - ./kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml
    networks:
      - elk</p>
<pre><code>&gt; 다음 config파일들은 docker-compose와 같은레벨에 디렉토리를 위치해준다.

`2. kibana &gt; config &gt; kibana.yml`
```yml
# 키바나 설정파일
server.name: kibana
server.host: &quot;0&quot;

# Elasticsearch 서버 정보
elasticsearch.hosts: http://elasticsearch:9200</code></pre><p><code>3. logstash &gt; pipeline &gt; logstash.conf</code></p>
<pre><code class="language-js">input {
  tcp {
    port =&gt; 5001
    codec =&gt; json_lines
  }
}

output {
  elasticsearch {
    hosts =&gt; [&quot;http://elasticsearch:9200&quot;]
    index =&gt; &quot;logstash-%{+YYYY.MM.dd}&quot;
  }
}</code></pre>
<hr />
<h1 id="5-spring-log관련-세팅">5. spring log관련 세팅</h1>
<h3 id="의존성">의존성</h3>
<ul>
<li><code>slf4j</code> : 2.0.16</li>
<li><code>logback-classic</code> : 1.5.6</li>
<li><code>logstash-logback-encoder</code> : 7.4</li>
</ul>
<h3 id="코드">코드</h3>
<p><code>build.gradle</code></p>
<pre><code class="language-java">dependencies {
    ...생략
    implementation 'org.slf4j:slf4j-api:2.0.16'
    implementation ('ch.qos.logback:logback-classic:1.5.6'){
    }
    implementation 'net.logstash.logback:logstash-logback-encoder:7.4'

}</code></pre>
<h3 id="config-파일들">config 파일들</h3>
<p>경로 : *<em>resourece &gt; abc.xml *</em> </p>
<p><code>logback-srping.xml</code></p>
<pre><code class="language-xml">&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;

&lt;configuration&gt;
    &lt;timestamp key=&quot;BY_DATE&quot; datePattern=&quot;yyyy-MM-dd&quot;/&gt;
    &lt;property name=&quot;LOG_PATTERN&quot;
              value=&quot;[%d{yyyy-MM-dd HH:mm:ss}:%-4relative] %green([%thread]) %highlight(%-5level) %boldWhite([%C.%M:%yellow(%L)]) - %msg%n&quot;/&gt;

    &lt;springProfile name=&quot;!prod&quot;&gt;
        &lt;include resource=&quot;console-appender.xml&quot;/&gt;

        &lt;root level=&quot;INFO&quot;&gt;
            &lt;appender-ref ref=&quot;CONSOLE&quot;/&gt;
        &lt;/root&gt;
    &lt;/springProfile&gt;

    &lt;springProfile name=&quot;prod&quot;&gt;
        &lt;include resource=&quot;logstash-append.xml&quot;&gt;&lt;/include&gt;

        &lt;root level=&quot;INFO&quot;&gt;
            &lt;appender-ref ref=&quot;LOGSTASH&quot; /&gt;
        &lt;/root&gt;
    &lt;/springProfile&gt;
&lt;/configuration&gt;</code></pre>
<p><code>logstash-append.xml</code></p>
<pre><code class="language-xml">&lt;included&gt;
    &lt;appender name=&quot;LOGSTASH&quot; class=&quot;net.logstash.logback.appender.LogstashTcpSocketAppender&quot;&gt;
        &lt;!-- Logstash 서버의 호스트 및 포트 --&gt;
        &lt;destination&gt;127.0.0.1:5001&lt;/destination&gt;

        &lt;!-- JSON 로그 포맷 설정 --&gt;
        &lt;encoder class=&quot;net.logstash.logback.encoder.LogstashEncoder&quot;&gt;
            &lt;customFields&gt;{&quot;application&quot;:&quot;MyApp&quot;, &quot;environment&quot;:&quot;production&quot;}&lt;/customFields&gt;
        &lt;/encoder&gt;

        &lt;!-- 재연결 정책 --&gt;
        &lt;reconnectionDelay&gt;5001&lt;/reconnectionDelay&gt; &lt;!-- 5초마다 재시도 --&gt;
    &lt;/appender&gt;
&lt;/included&gt;</code></pre>
<h1 id="실습">실습</h1>
<h3 id="1-docker-image를-통한-elk-실행">1. docker image를 통한 elk 실행</h3>
<p>*<em>1) docker compose up 하기
*</em></p>
<pre><code>docker compose up -d</code></pre><p>*<em>2) kibana 들어가서 index 패턴 설정
*</em>
사이드 탭 &gt; management &gt; indexmanagement &gt; create index pattern</p>
<p>logstash-*로 시작하는 인텍스 패턴을 설정완료한다.</p>
<p>*<em>3) spring boot 서버 실행 후 임의로 에러 메시지 보내기 *</em></p>
<p>intellij환경에서 spring-boot를 실행해줬고 이때 profile은 <strong><code>prod</code></strong>로 설정</p>
<p>저는 허용되지 않은 메서드를 통해 예외 로그가 발생하게했습니다. </p>
<h3 id="결과">결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/b2970820-d574-4ffc-aa8c-5c7ebde982db/image.png" /></p>
<p><strong>-&gt; GET Method Not allowed가 잘 시각화 되는 것을 확인할 수 있다.</strong></p>
<h3 id="ref">Ref</h3>
<p> <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html?utm_source=chatgpt.com#docker-wolfi-hardened-image">docker envirionment 참고 </a></p>
<p><a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html?utm_source=chatgpt.com#docker-wolfi-hardened-image">elastic search 강의 </a></p>
<p><a href="https://ggparkitbank.tistory.com/190">logback-logstash 연결 블로그</a></p>