<h1 id="문제-제기">문제 제기</h1>
<p> 저번에 로컬에서 ELK 파이프라인과 스프링서버 로깅과의 연결을 성공했습니다.</p>
<p> 링크 : <a href="https://velog.io/@gyural/ELK-%EC%9D%B4%EB%A0%87%EA%B2%8C%EB%A7%8C-%ED%95%98%EC%9E%90">ELK이렇게만하자 🧨</a></p>
<blockquote>
<p>하지만 실제 사용을 위해서는 인증서같은 배포환경에서 발생하는 이슈들을 해결해야했습니다.</p>
</blockquote>
<p> 따라서 공식문서에 따라서 차근차근 시도해봤습니다.
 <a href="https://www.elastic.co/kr/blog/getting-started-with-the-elastic-stack-and-docker-compose/">ELK with docker-compose</a></p>
<h1 id="1-setup컨테이너-es컨테이너-실행">1. setup컨테이너 es컨테이너 실행</h1>
<p> 아래 두 파일을 루트경로 바로 밑에 두었습니다.</p>
<p> <code>.env</code></p>
<pre><code> # Project namespace (defaults to the current folder name if not set)
#COMPOSE_PROJECT_NAME=logginStudy


# Password for the 'elastic' user (at least 6 characters)
ELASTIC_PASSWORD=changeme


# Password for the 'kibana_system' user (at least 6 characters)
KIBANA_PASSWORD=changeme


# Version of Elastic products
STACK_VERSION=8.7.1


# Set the cluster name
CLUSTER_NAME=docker-cluster


# Set to 'basic' or 'trial' to automatically start the 30-day trial
LICENSE=basic
#LICENSE=trial

# ES memory Limit


# Port to expose Elasticsearch HTTP API to the host
ES_PORT=9200


# Port to expose Kibana to the host
KIBANA_PORT=5601


# Increase or decrease based on the available host memory (in bytes)
ES_MEM_LIMIT=1073741824
KB_MEM_LIMIT=1073741824
LS_MEM_LIMIT=1073741824


# SAMPLE Predefined Key only to be used in POC environments
ENCRYPTION_KEY=c34d38b3a14956121ff2170e5030b471551370178f43e5626eec58b04a30fae2
</code></pre><p> <code>docker-compose.yml</code></p>
<blockquote>
<p>elasticsearch.memlist 는 문법에러가 발생해서 제외했습니다.</p>
</blockquote>
<pre><code class="language-yaml"> version: &quot;3.8&quot;

volumes:
  certs:
    driver: local
  esdata01:
    driver: local
  kibanadata:
    driver: local
  metricbeatdata01:
    driver: local
  filebeatdata01:
    driver: local
  logstashdata01:
    driver: local

networks:
  default:
    name: elastic
    external: false

services:
  setup:
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    volumes:
      - certs:/usr/share/elasticsearch/config/certs
    user: &quot;0&quot;
    command: &gt;
      bash -c '
        if [ x${ELASTIC_PASSWORD} == x ]; then
          echo &quot;Set the ELASTIC_PASSWORD environment variable in the .env file&quot;;
          exit 1;
        elif [ x${KIBANA_PASSWORD} == x ]; then
          echo &quot;Set the KIBANA_PASSWORD environment variable in the .env file&quot;;
          exit 1;
        fi;
        if [ ! -f config/certs/ca.zip ]; then
          echo &quot;Creating CA&quot;;
          bin/elasticsearch-certutil ca --silent --pem -out config/certs/ca.zip;
          unzip config/certs/ca.zip -d config/certs;
        fi;
        if [ ! -f config/certs/certs.zip ]; then
          echo &quot;Creating certs&quot;;
          echo -ne \
          &quot;instances:\n&quot;\
          &quot;  - name: es01\n&quot;\
          &quot;    dns:\n&quot;\
          &quot;      - es01\n&quot;\
          &quot;      - localhost\n&quot;\
          &quot;    ip:\n&quot;\
          &quot;      - 127.0.0.1\n&quot;\
          &quot;  - name: kibana\n&quot;\
          &quot;    dns:\n&quot;\
          &quot;      - kibana\n&quot;\
          &quot;      - localhost\n&quot;\
          &quot;    ip:\n&quot;\
          &quot;      - 127.0.0.1\n&quot;\
          &gt; config/certs/instances.yml;
          bin/elasticsearch-certutil cert --silent --pem -out config/certs/certs.zip --in config/certs/instances.yml --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key;
          unzip config/certs/certs.zip -d config/certs;
        fi;
        echo &quot;Setting file permissions&quot;
        chown -R root:root config/certs;
        find . -type d -exec chmod 750 \{\} \;;
        find . -type f -exec chmod 640 \{\} \;;
        echo &quot;Waiting for Elasticsearch availability&quot;;
        until curl -s --cacert config/certs/ca/ca.crt https://es01:9200 | grep -q &quot;missing authentication credentials&quot;; do sleep 30; done;
        echo &quot;Setting kibana_system password&quot;;
        until curl -s -X POST --cacert config/certs/ca/ca.crt -u &quot;elastic:${ELASTIC_PASSWORD}&quot; -H &quot;Content-Type: application/json&quot; https://es01:9200/_security/user/kibana_system/_password -d &quot;{\&quot;password\&quot;:\&quot;${KIBANA_PASSWORD}\&quot;}&quot; | grep -q &quot;^{}&quot;; do sleep 10; done;
        echo &quot;All done!&quot;;
      '
    healthcheck:
      test: [&quot;CMD-SHELL&quot;, &quot;[ -f config/certs/es01/es01.crt ]&quot;]
      interval: 1s
      timeout: 5s
      retries: 120

  es01:
    depends_on:
      setup:
        condition: service_healthy
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    labels:
      co.elastic.logs/module: elasticsearch
    volumes:
      - certs:/usr/share/elasticsearch/config/certs
      - esdata01:/usr/share/elasticsearch/data
    ports:
      - ${ES_PORT}:9200
    environment:
      - node.name=es01
      - cluster.name=${CLUSTER_NAME}
      - discovery.type=single-node
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - bootstrap.memory_lock=true
      - xpack.security.enabled=true
      - xpack.security.http.ssl.enabled=false
      - xpack.security.http.ssl.key=certs/es01/es01.key
      - xpack.security.http.ssl.certificate=certs/es01/es01.crt
      - xpack.security.http.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.enabled=true
      - xpack.security.transport.ssl.key=certs/es01/es01.key
      - xpack.security.transport.ssl.certificate=certs/es01/es01.crt
      - xpack.security.transport.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.verification_mode=certificate
      - xpack.license.self_generated.type=${LICENSE}

#    memory limit 문법에러 발생으로 제외 시킴
#    mem_limit: ${ES_MEM_LIMIT}
    ulimits:
      memlock:
        soft: -1
        hard: -1
    healthcheck:
      test:
        [
          &quot;CMD-SHELL&quot;,
          &quot;curl -s --cacert config/certs/ca/ca.crt https://localhost:9200 | grep -q 'missing authentication credentials'&quot;,
        ]
      interval: 10s
      timeout: 10s
      retries: 120
</code></pre>
<h3 id="테스팅">테스팅</h3>
<p> 인증서 복사를 위해 경로 생성</p>
<pre><code class="language-shell"> mkdir -p ~/tmp/
 docker cp loggingstudy-es01-1:/usr/share/elasticsearch/config/certs/ca/ca.crt ~/tmp/  </code></pre>
<blockquote>
<p>정상 복사 완료!</p>
</blockquote>
<pre><code class="language-bash"> curl --cacert ~/tmp/ca.crt -u elastic:changeme https://localhost:9200</code></pre>
<p> 이후 es단으로 쿼리 호출</p>
<blockquote>
<p>성공적으로 연결 완료</p>
</blockquote>
<p> <img alt="" src="https://velog.velcdn.com/images/gyural/post/dd49dd58-e9d9-4d89-a487-9602d8b03a3c/image.png" /></p>
<hr />
<h1 id="2--kibana">2 . Kibana</h1>
<h3 id="1-docker-compse-kibana-컨테이너-부분-세팅">1) docker-compse kibana 컨테이너 부분 세팅</h3>
<p><code>docker-compose.yml</code></p>
<pre><code>''' 위 부분 생량
  kibana:
    depends_on:
      es01:
        condition: service_healthy
    image: docker.elastic.co/kibana/kibana:${STACK_VERSION}
    labels:
      co.elastic.logs/module: kibana
    volumes:
      - certs:/usr/share/kibana/config/certs
      - kibanadata:/usr/share/kibana/data
    ports:
      - ${KIBANA_PORT}:5601
    environment:
      - SERVERNAME=kibana
      - ELASTICSEARCH_HOSTS=https://es01:9200
      - ELASTICSEARCH_USERNAME=kibana_system
      - ELASTICSEARCH_PASSWORD=${KIBANA_PASSWORD}
      - ELASTICSEARCH_SSL_CERTIFICATEAUTHORITIES=config/certs/ca/ca.crt
      - XPACK_SECURITY_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_REPORTING_ENCRYPTIONKEY=${ENCRYPTION_KEY}
    healthcheck:
      test:
        [
          &quot;CMD-SHELL&quot;,
          &quot;curl -s -I http://localhost:5601 | grep -q 'HTTP/1.1 302 Found'&quot;,
        ]
      interval: 10s
      timeout: 10s
      retries: 120</code></pre><h3 id="2-localhost5601-로-접속">2) localhost:5601/ 로 접속</h3>
<p>kibana-container가 잘 실행중이라면 위 주소로 접속하면 kibana가 잘 실행중인 것을 확인할 수 있다.</p>
<p> <img alt="" src="https://velog.velcdn.com/images/gyural/post/023834b0-2757-48aa-9e07-e575448153a2/image.png" /></p>
<hr />
<h1 id="3-metricbeat-filebeat생략">3. Metricbeat Filebeat생략</h1>
<p>리소스의 오버헤드때문에 선택적으로 제외 가능한 Metricbeat, Filebeat스택은 제외하기로 했습니다.</p>
<blockquote>
<p>구체적인 이유는 아래와 같습니다..!!</p>
</blockquote>
<p>Spring Boot 앱에서 logback-logstash-appender를 사용해 로그를 직접 Logstash로 전송하는 경우, Filebeat가 중복되어 작동할 수 있습니다. 왜냐하면 logback-logstash-appender는 이미 애플리케이션의 로그를 Logstash로 보내기 때문에 Filebeat는 추가적인 수집 과정 없이 필요하지 않을 수 있습니다.</p>
<h1 id="4-logstash">4. Logstash</h1>
<p><code>docker-comopose yml</code></p>
<pre><code class="language-yaml">version: &quot;3.8&quot;

volumes:
  certs:
    driver: local
  esdata01:
    driver: local
  kibanadata:
    driver: local
  metricbeatdata01:
    driver: local
  filebeatdata01:
    driver: local
  logstashdata01:
    driver: local

networks:
  default:
    name: elastic
    external: false

services:
  setup:
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    volumes:
      - certs:/usr/share/elasticsearch/config/certs
    user: &quot;0&quot;
    command: &gt;
      bash -c '
        if [ x${ELASTIC_PASSWORD} == x ]; then
          echo &quot;Set the ELASTIC_PASSWORD environment variable in the .env file&quot;;
          exit 1;
        elif [ x${KIBANA_PASSWORD} == x ]; then
          echo &quot;Set the KIBANA_PASSWORD environment variable in the .env file&quot;;
          exit 1;
        fi;
        if [ ! -f config/certs/ca.zip ]; then
          echo &quot;Creating CA&quot;;
          bin/elasticsearch-certutil ca --silent --pem -out config/certs/ca.zip;
          unzip config/certs/ca.zip -d config/certs;
        fi;
        if [ ! -f config/certs/certs.zip ]; then
          echo &quot;Creating certs&quot;;
          echo -ne \
          &quot;instances:\n&quot;\
          &quot;  - name: es01\n&quot;\
          &quot;    dns:\n&quot;\
          &quot;      - es01\n&quot;\
          &quot;      - localhost\n&quot;\
          &quot;    ip:\n&quot;\
          &quot;      - 127.0.0.1\n&quot;\
          &quot;  - name: kibana\n&quot;\
          &quot;    dns:\n&quot;\
          &quot;      - kibana\n&quot;\
          &quot;      - localhost\n&quot;\
          &quot;    ip:\n&quot;\
          &quot;      - 127.0.0.1\n&quot;\
          &gt; config/certs/instances.yml;
          bin/elasticsearch-certutil cert --silent --pem -out config/certs/certs.zip --in config/certs/instances.yml --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key;
          unzip config/certs/certs.zip -d config/certs;
        fi;

        echo &quot;Setting file permissions&quot;
        chown -R root:root config/certs;
        find . -type d -exec chmod 750 \{\} \;;
        find . -type f -exec chmod 640 \{\} \;;

        echo &quot;Waiting for Elasticsearch availability&quot;;
        until curl -s --cacert config/certs/ca/ca.crt https://es01:9200 | grep -q &quot;missing authentication credentials&quot;; do sleep 30; done;

        echo &quot;Setting kibana_system password&quot;;
        until curl -s -X POST --cacert config/certs/ca/ca.crt -u &quot;elastic:${ELASTIC_PASSWORD}&quot; -H &quot;Content-Type: application/json&quot; https://es01:9200/_security/user/kibana_system/_password -d &quot;{\&quot;password\&quot;:\&quot;${KIBANA_PASSWORD}\&quot;}&quot; | grep -q &quot;^{}&quot;; do sleep 10; done;

        echo &quot;All done!&quot;;
      '
    healthcheck:
      test: [&quot;CMD-SHELL&quot;, &quot;[ -f config/certs/es01/es01.crt ]&quot;]
      interval: 1s
      timeout: 5s
      retries: 120

  es01:
    depends_on:
      setup:
        condition: service_healthy
    image: docker.elastic.co/elasticsearch/elasticsearch:${STACK_VERSION}
    labels:
      co.elastic.logs/module: elasticsearch
    volumes:
      - certs:/usr/share/elasticsearch/config/certs
      - esdata01:/usr/share/elasticsearch/data
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf  # logstash.conf 파일 마운트
    ports:
      - ${ES_PORT}:9200
    environment:
      - node.name=es01
      - cluster.name=${CLUSTER_NAME}
      - discovery.type=single-node
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - bootstrap.memory_lock=true
      - xpack.security.enabled=true
      - xpack.security.http.ssl.enabled=true
      - xpack.security.http.ssl.key=certs/es01/es01.key
      - xpack.security.http.ssl.certificate=certs/es01/es01.crt
      - xpack.security.http.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.enabled=true
      - xpack.security.transport.ssl.key=certs/es01/es01.key
      - xpack.security.transport.ssl.certificate=certs/es01/es01.crt
      - xpack.security.transport.ssl.certificate_authorities=certs/ca/ca.crt
      - xpack.security.transport.ssl.verification_mode=certificate
      - xpack.license.self_generated.type=${LICENSE}
      - ES_JAVA_OPTS=-Xms2g -Xmx2g

#    memory limit 문법에러 발생으로 제외 시킴
#    mem_limit: ${ES_MEM_LIMIT}
    ulimits:
      memlock:
        soft: -1
        hard: -1
    healthcheck:
      test:
        [
          &quot;CMD-SHELL&quot;,
          &quot;curl -s --cacert config/certs/ca/ca.crt https://localhost:9200 | grep -q 'missing authentication credentials'&quot;,
        ]
      interval: 5s
      timeout: 5s
      retries: 60
  kibana:
    depends_on:
      es01:
        condition: service_healthy
    image: docker.elastic.co/kibana/kibana:${STACK_VERSION}
    labels:
      co.elastic.logs/module: kibana
    volumes:
      - certs:/usr/share/kibana/config/certs
      - kibanadata:/usr/share/kibana/data
    ports:
      - ${KIBANA_PORT}:5601
    environment:
      - SERVERNAME=kibana
      - ELASTICSEARCH_HOSTS=https://es01:9200
      - ELASTICSEARCH_USERNAME=kibana_system
      - ELASTICSEARCH_PASSWORD=${KIBANA_PASSWORD}
      - ES_JAVA_OPTS=-Xms1g -Xmx1g
      - ELASTICSEARCH_SSL_CERTIFICATEAUTHORITIES=config/certs/ca/ca.crt
      - XPACK_SECURITY_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=${ENCRYPTION_KEY}
      - XPACK_REPORTING_ENCRYPTIONKEY=${ENCRYPTION_KEY}
    healthcheck:
      test:
        [
          &quot;CMD-SHELL&quot;,
          &quot;curl -s -I http://localhost:5601 | grep -q 'HTTP/1.1 302 Found'&quot;,
        ]
      interval: 10s
      timeout: 10s
      retries: 120
  logstash01:
    hostname: host.docker.internal
    depends_on:
      es01:
        condition: service_healthy
      kibana:
        condition: service_healthy
    image: docker.elastic.co/logstash/logstash:${STACK_VERSION}
    labels:
      co.elastic.logs/module: logstash
    user: root
    volumes:
      - certs:/usr/share/logstash/certs
      - logstashdata01:/usr/share/logstash/data
      - &quot;./logstash_ingest_data/:/usr/share/logstash/ingest_data/&quot;
      - &quot;./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro&quot;
    ports:
      - ${LOGSTASH_PORT}:5001
    environment:
      - xpack.monitoring.enabled=false
      - ELASTIC_USER=elastic
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - ELASTIC_HOSTS=https://es01:9200
#    healthcheck:
#      test: curl --fail http://localhost:5001 || exit 1
#      interval: 10s
#      timeout: 5s
#      retries: 3
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: example
      MYSQL_USER: example
      MYSQL_PASSWORD: example
    ports:
      - &quot;3306:3306&quot;
    healthcheck:
      test: [ &quot;CMD&quot;, &quot;mysqladmin&quot;, &quot;ping&quot;, &quot;-h&quot;, &quot;localhost&quot;, &quot;-pexample&quot; ]
      interval: 10s
      timeout: 5s
      retries: 3
  backend:
    container_name: logingStudy_BE
    depends_on:
      db:
        condition: service_healthy
    build: .
    ports:
      - &quot;8080:8080&quot;
    environment:
      SPRING_PROFILES_ACTIVE: prod
      LOGSTASH_HOST: logstash01
      LOGSTASH_PORT: 5001
      db-url: jdbc:mysql://db:3306/example # 역할 확인
      db-username: example
      db-password: example</code></pre>
<p>굉장히 깁니다...</p>
<p><code>./logstash.conf</code></p>
<pre><code class="language-py">input {
  tcp {
    port =&gt; 5001
  }
}

filter {
  # 필터가 비어 있는 경우 그대로 둘 수 있습니다.
}

output {
  elasticsearch {
    index =&gt; &quot;logstash-%{+YYYY.MM.dd}&quot;
    hosts =&gt; &quot;${ELASTIC_HOSTS}&quot;
    user =&gt; &quot;${ELASTIC_USER}&quot;
    password =&gt; &quot;${ELASTIC_PASSWORD}&quot;
    cacert =&gt; &quot;certs/ca/ca.crt&quot;
  }
}</code></pre>
<p>2개 파일 세팅 이후에 docker-compose를 실행해준다.</p>
<blockquote>
<p>docker compose up -d</p>
</blockquote>
<h3 id="결과">결과</h3>
<p>*<em>1) setup완료후 각 스택 컨테이너가 실행됨
*</em><img alt="" src="https://velog.velcdn.com/images/gyural/post/cf57a28c-d0bd-4f8b-bf1e-eaa7c3b9b0a5/image.png" /></p>
<p>*<em>2) 스프링 서버 실행 후 kibana에서 로그들 확인가능
*</em></p>
<pre><code>localhost:5601로 접속 후

username : elastic
password : changeme </code></pre><p><img alt="" src="https://velog.velcdn.com/images/gyural/post/6f89b49e-2588-4c28-8e4d-5fec4a6ae832/image.png" /></p>
<hr />
<h1 id="ec2에-배포하기">EC2에 배포하기</h1>
<h3 id="배포-전-마주한-문제들">배포 전 마주한 문제들...</h3>
<p>1) Elastic-search Container가 자꾸 꺼짐
로그에도 뭔가가 발견이 안되어서 메모리 부족시 자동으로 꺼진다고 함</p>
<blockquote>
<p>8GB 메모리가 있는 시스템에서는 2GB 정도로 설정하면 적합합니다.</p>
</blockquote>
<p>라고 합니다...</p>
<p>2) docker-compose build 캐싱문제</p>
<p>에러문 부터 보자</p>
<pre><code class="language-bash">elastic_log_docker-app-1 | 14:15:45,959 |-WARN in net.logstash.logback.appender.LogstashTcpSocketAppender[logstash] - Log destination localhost/&lt;unresolved&gt;:5000: connection
</code></pre>
<p>localhost/:5000:
  --&gt; logstash 호스트 포트를 못찾는다는 뜻이었다..!!!
  이문제를 해결하려고 모든 파일을 한번씩 바꿔가면서 <strong>이틀가까이 걸렸지만...</strong></p>
<p>  결국 문제는 그거였다..!!</p>
<blockquote>
<p>docker-compose service였던 backend는 Dockerfile에 의해 빌드된다</p>
</blockquote>
<ul>
<li><p>docker-compose.yml (backend부분)</p>
<pre><code class="language-yaml">backend:
container_name: logingStudy_BE
depends_on:
  db:
    condition: service_healthy
build: .
ports:
  - &quot;8080:8080&quot;
environment:
  SPRING_PROFILES_ACTIVE: prod
  LOGSTASH_HOST: logstash01
  LOGSTASH_PORT: 5003
  db-url: jdbc:mysql://db:3306/example # 역할 확인
  db-username: example
  db-password: example</code></pre>
</li>
<li><p>Dockerfile</p>
<pre><code class="language-docker">
  FROM openjdk:21
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT [&quot;java&quot;, &quot;-jar&quot;, &quot;app.jar&quot;]
</code></pre>
</li>
</ul>
<p>이론상이라면.... build &gt; libs &gt; ....**.jar파일을 통해 빌드가 발생해야하는데...</p>
<p>혹시 몰라 해당 jar파일을 모두 삭제하고 docker-compose up을 해봤지만...
그래도 정상적으로 실행되었다.</p>
<blockquote>
<p>굉장히 이상한 점이다 빌드파일이 없는데 서버가 어떻게 실행되었을까?</p>
</blockquote>
<p>docker-compose up을 하게되면 할 때마다 빌드를 진행한다는 것이 아니었다.</p>
<p>따라서 추가적으로 backend service를 빌드해줬다.</p>
<pre><code>  docker compose build backend</code></pre><p>이후 최종적으로 docker-compose.yml에서만 실행이 성공했다..!!</p>
<hr />
<h3 id="결과-1">결과</h3>
<p>  <img alt="업로드중.." src="blob:https://velog.io/8fb53076-eb6c-48ea-8c20-fb2caba2733a" /></p>