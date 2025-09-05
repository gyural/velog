<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/47c36772-79c8-44e0-b870-21b6653a343d/image.png" /></p>
<h1 id="1-오프셋-페이지네이션">1. 오프셋 페이지네이션</h1>
<p>기본적인 페이지네이션이다. SQL의 LIMIT과 OFFSET을 활용한다.</p>
<pre><code class="language-sql">select *
from post
order by create_at desc
limit [페이지사이즈] 
offset [페이지번호];</code></pre>
<h3 id="장점">장점</h3>
<p>직관적이고 구현하기 편하다 - 특히 JPA에서 Pagable인자만 추가해서 쉽게 구현할 수 있다.</p>
<h3 id="단점">단점</h3>
<p>1)  조회시에 offset만큼 READ를 하기 때문에 offset이 100만개 처럼 매우 커지면 성능이 안좋아진다.</p>
<p>예를 들어 offset 10000, limit 20이라면 20개를 읽기 위해 10020개를 읽어야한다.</p>
<p>2) 중복 데이터 발생이 가능하다 WRITE가 빈번한 테이블은 다음페이지를 요청해도 같은 데이터가 넘어올 수 있다. </p>
<hr />
<h1 id="2-커서-기반-페이지-네이션">2. 커서 기반 페이지 네이션</h1>
<p><code>Cursor</code> 라는 개념을 사용하여 offset을 사용하지 않고 Cursor를 기준으로 다음 N개의 데이터를 반환해주는 방식이다.
이때 Cursor가 될 수 있는 컬럼은 Unique하거나, 정렬가능(Orderable), 불변(Immutable)한 컬럼을 선택해야한다.</p>
<pre><code class="language-sql">SELECT *
FROM post
WHERE 조건문
    AND id &lt; 마지막조회ID # 직전 조회 결과의 마지막 id
ORDER BY id DESC
LIMIT 페이지사이즈</code></pre>
<h3 id="채팅방-message-created_at를-기준으로-하는-예제">채팅방 message created_at를 기준으로 하는 예제</h3>
<p><code>sql</code></p>
<pre><code class="language-sql"># 첫 페이지 진입시 발생 쿼리
select *
from message 
order by created_at desc
limit 10; 

# 이후 페이지 요청시 발생 쿼리
select *
from message
where created_at &lt; lastSeenCreatedAt # ex) cursor값이 10인 경우
limit 10;
</code></pre>
<p><code>Repository</code></p>
<pre><code class="language-java">public interface MessageRepository extends JpaRepository&lt;Message, UUID&gt; {

    @Query(&quot;&quot;&quot;
        SELECT m
        FROM Message m
        WHERE m.channel.id = :channelId
        AND (:lastSeenCreatedAt IS NULL OR m.createdAt &lt; :lastSeenCreatedAt)
        ORDER BY m.createdAt DESC
        &quot;&quot;&quot;)
    List&lt;Message&gt; findNextMessages(
        @Param(&quot;channelId&quot;) UUID channelId,
        @Param(&quot;lastSeenCreatedAt&quot;) Instant lastSeenCreatedAt,
        Pageable pageable
    );
}</code></pre>
<p><code>service</code></p>
<pre><code class="language-java">// 처음 조회 (cursor 없음)
List&lt;Message&gt; firstPage = repo.findNextMessages(channelId, null, PageRequest.of(0, 20));

// 다음 조회 (마지막 메시지 createdAt 전달)
Instant lastCursor = firstPage.get(firstPage.size() - 1).getCreatedAt();
List&lt;Message&gt; nextPage = repo.findNextMessages(channelId, lastCursor, PageRequest.of(0, 20));</code></pre>
<hr />
<h3 id="ref">REF</h3>
<p><a href="https://0soo.tistory.com/130">커서 기반 페이지네이션</a></p>