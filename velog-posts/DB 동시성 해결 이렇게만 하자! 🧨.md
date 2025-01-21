<h1 id="문제제기">문제제기</h1>
<p>저번 프로젝트에서 동시성 문제에대한 에러가 발생했고, 이는 학습하지 않은 분야여서, 가장 보수적인 방법인 비관적 락을 통해서 문제를 해결해줬다...</p>
<blockquote>
<p>하지만...</p>
</blockquote>
<p>주요 개념들을 모른 상태로 기술을 선택하는 것은 <strong>제대로된 엔지니어링이 아니라고 생각</strong>하기때문에!!!!
DB 락킹에대한 공부를 출발하고 <strong>이번 프로젝트의 표준까지 결론을 내려보려고한다.</strong></p>
<p>ref : <a href="https://velog.io/@blackarea/JPA-%EB%82%99%EA%B4%80%EC%A0%81-%EB%9D%BD%EA%B3%BC-Spring-AOP%EB%A5%BC-%ED%86%B5%ED%95%9C-DB-%EB%8F%99%EC%8B%9C%EC%84%B1-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0">black area[JPA] DB동시성 이슈와 비관적 락, 낙관적 락
</a></p>
<hr />
<h2 id="1-락lock이란">1. 락(Lock)이란?</h2>
<p>DB에 여러 트랙잭션이 접근하는 상황에서 데이터 무결성, 일관성을 지키기위해 하는 방법이며, 여러 트랜젝션이 한 데이터에 동시에 접근하는 것을 제한하는 방식이다.</p>
<hr />
<h2 id="2-트랜잭션-격리-수준">2. 트랜잭션 격리 수준</h2>
<p>트랜잭션 격리 수준이란...
한 트랜잭션이 데이터를 다룰 시에 또 다른 트랜잭션에서 해당 데이터의 조회, 수정, 추가 등의 연산을 허용할지 말지를 결정하는 것이며, 4가지로 나눌 수 있다.</p>
<h3 id="read-uncommitted">READ UNCOMMITTED</h3>
<ul>
<li>커밋하지 않은 데이터(트랜잭션의 작업이 끝나지 않은 데이터)를 읽을(READ) 수 있다.
  -&gt; Dirty Read발생 가능성 0</li>
</ul>
<h3 id="read-committed">READ COMMITTED</h3>
<ul>
<li>커밋한 데이터만 읽을 수 있다.
  -&gt; Dirt Read발생 가능성 X</li>
<li><strong>오라클 DBMS에서 표준으로 사용</strong>하고 있고 가장 많이 선택된다.</li>
<li>한 트랜잭션이 데이터를 2번 이상 읽을때 1번째 읽기 작업 후 다른트랜잭션이 수정/삭제 연산이 발생한다면 2번째 읽을 때 일관성이 보장되지않는 <strong>Nonrepetable Read</strong>가 발생한다!</li>
</ul>
<h3 id="repetable-read">REPETABLE READ</h3>
<ul>
<li><p>트랜잭션 동안 읽은 데이터는 다른 트랜잭션에서 수정하거나 삭제 불가.
  -&gt; Nonerepetable Read 발생 가능성 X</p>
</li>
<li><p>Phantom Read가 발생할 가능성 있음: A 트랜잭션이 같은 조건으로 데이터를 여러번 조회했을 때 B 트랜잭션이 테이터를 추가하거나 삭제해서 A 트랜잭션의 결과 값이 최종적으로 달라지는 현상을 말한다.
  <strong>예시)</strong>
  A트랜잭션-step1</p>
<pre><code class="language-sql">  SELECT * FROM employees WHERE salary &gt; 4000;</code></pre>
<p>  B트랜잭션-step2</p>
<pre><code class="language-sql">INSERT INTO employees (id, name, salary) VALUES (3, 'Charlie', 7000);
  COMMIT;</code></pre>
<p>  A트랜잭션-step2</p>
<pre><code class="language-sql">  SELECT * FROM employees WHERE salary &gt; 4000;</code></pre>
<p>  첫번 째 조회값과 결과값이 달라지게된다...</p>
<h3 id="serializable">SERIALIZABLE</h3>
</li>
<li><p>모든 트랜잭션이 순차적으로 실행된다.</p>
</li>
<li><p>가장 엄격한 격리 수눈이며, 성능이 떨어질 수 있는 상황 발생 가능성 0</p>
</li>
</ul>
<hr />
<h2 id="3-낙관적-락과-비관적-락">3. 낙관적 락과 비관적 락</h2>
<h3 id="비관적-락">비관적 락</h3>
<p>트랜잭션끼리 충돌이 발생한다고 가정을 하고, 락을 거는 방법이다. DBMS의 락 기능을 사용하고, 데이터 수정시 트랜잭션 충돌여부를 확인할 수 있다.</p>
<h3 id="낙관적-락">낙관적 락</h3>
<p>트랜잭션 충돌할 가능성이 매우 낮은 상황에서 사용. DBMS가 제공하는 락 기능을 사용하지 않고, 어플리케이션 레벨에서 자체적으로 락과 유사한 동작 수행.
조회할 대 DB락을 사용하지 않기 때문에 성능이 좋다(충돌이 발생하지 않는 다는 경우)</p>
<hr />
<h2 id="4-라이트한-동시성-테스트-코드">4. 라이트한 동시성 테스트 코드</h2>
<p><code>엔티티와 서비스 코드</code></p>
<pre><code class="language-java">@Entity
public class Room{
    ...
    private int bedCount;
    ...
    public void increaseBed(){
        this.bedCount = this.bedCount+1;
    }
}
@Service
public class RoomServiceImpl implements RoomService {
    private final RoomRepository roomRepository;

    @Transactional
    public void increaseBedCount(long id){
        Room room = roomRepository.findById(id).orElseThrow();
        room.increaseBed();
    }
}</code></pre>
<p><code>테스트 코드</code></p>
<pre><code class="language-java">@Test
    @DisplayName(&quot;동시성 실패 테스트&quot;)
    public void concurrencyFailTest() throws InterruptedException {
        int count = 100;

        ExecutorService executorService = Executors.newFixedThreadPool(32);
        CountDownLatch latch = new CountDownLatch(count);
        for (int i = 0; i &lt; count; i++) {
            executorService.execute(() -&gt; {
                try{
                    roomService.increaseBedCount(3L);
                }catch (Exception e){
                    e.printStackTrace();
                }
                latch.countDown();
            });
        }
        latch.await();

        Room room = roomRepository.findById(3L).get();
        log.info(&quot;room.getBedCount() : &quot; + room.getBedCount());
        Assertions.assertThat(room.getBedCount()).isNotEqualTo(count);
    }</code></pre>
<p>동시성 테스트를 위해 <strong>스레드를 만들어서 테스팅</strong>을 해줄 수 있다.
기본적인 스프링 세팅에서는 이정도의 동시성에서는 에러가 발생하지 않는다..!!</p>
<h3 id="결과">결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/7fb7106c-e314-40ab-be64-5a01c6d34647/image.png" /></p>
<hr />
<h2 id="5-jpa에서-비관적-락">5. JPA에서 비관적 락</h2>
<p>*<em>1. PESSIMISTIC_READ
*</em>    </p>
<ul>
<li>의미: 데이터를 읽을 때 잠금을 걸어, 다른 트랜잭션이 수정하거나 삭제하지 못하도록 방지합니다. 하지만 읽기는 허용합니다.<ul>
<li>예시:
•    트랜잭션 A: 상품 재고 정보를 읽음.
•    트랜잭션 B: 재고를 수정하려고 하면, 트랜잭션 A가 끝날 때까지 기다립니다.
•    쉽게 이해하기:</li>
<li><ul>
<li>“내가 이 데이터를 보고 있는 동안 수정은 금지! 하지만 네가 읽는 건 괜찮아.”</li>
</ul>
</li>
<li><ul>
<li></li>
</ul>
</li>
</ul>
</li>
</ul>
<p>*<em>2. PESSIMISTIC_WRITE
*</em></p>
<ul>
<li>의미: 데이터를 수정하거나 삭제할 때 잠금을 걸어, 다른 트랜잭션이 읽기, 쓰기, 삭제를 모두 못하도록 방지합니다.</li>
<li>예시:
  •    트랜잭션 A: 상품 정보를 수정 중.
  •    트랜잭션 B: 상품 정보를 읽거나 수정하려고 하면, 트랜잭션 A가 끝날 때까지 기다립니다.
  •    쉽게 이해하기:</li>
<li><ul>
<li>“내가 이 데이터를 수정하는 중이니까 아무도 못 읽고 못 수정해!”</li>
</ul>
</li>
<li><ul>
<li></li>
</ul>
</li>
</ul>
<p>*<em>3. PESSIMISTIC_FORCE_INCREMENT
*</em></p>
<ul>
<li>의미: 데이터에 버전 정보를 강제로 증가시켜, 동시성 문제를 제어합니다. 주로 <strong>낙관적 락(Optimistic Lock)</strong>과 병행해서 사용됩니다.</li>
<li>예시:
  •    재고를 조회만 해도 버전이 증가하도록 설정해, 낙관적 락이 데이터를 잘못 처리하지 않도록 보완합니다.
  •    쉽게 이해하기:
  “내가 이 데이터를 조작한 건 아니지만, 버전은 올려서 누군가 수정하려고 하면 이를 완료하고 확인하도록 할게.”</li>
</ul>
<h3 id="추가-심화">추가 심화</h3>
<p>*<em>- PESSIMISTIC_READ와 WRITE는 성능 저하 우려
*</em>
비관적 락은 트랜잭션 간의 충돌을 방지하지만, 동시성 제어가 필요한 상황이 아니거나 데이터 갱신 빈도가 낮은 경우 성능 저하를 유발할 수 있습니다.</p>
<p>*<em>- PESSIMISTIC_FORCE_INCREMENT는 특정 시나리오에서만 유효
*</em>
조회만으로도 버전을 증가시키는 것은 일부 특별한 동시성 문제를 해결하는 데 유용하지만, 대부분의 일반적인 애플리케이션에는 필요하지 않습니다.</p>
<h2 id="6-jpa-비관적락-코드">6. JPA 비관적락 코드</h2>
<p><code>엔티티와 서비스 코드</code></p>
<pre><code class="language-java">public interface RoomRepository extends JpaRepository&lt;Room, Long&gt; {
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query(&quot;select r from Room r where r.id = :id&quot;)
    Optional&lt;Room&gt; findByIdPessimisticLock(@Param(&quot;id&quot;) Long id);
}
@Service
public class RoomServiceImpl implements RoomService {
    private final RoomRepository roomRepository;

    @Transactional
    public void increaseBedCountPessimisticLock(long id){
        Room room = roomRepository.findByIdPessimisticLock(id).orElseThrow();
        room.increaseBed();
    }
}</code></pre>
<p><code>테스트 코드</code></p>
<pre><code class="language-java">@Test
    @DisplayName(&quot;동시성 테스트 - 비관적락&quot;)
    public void concurrencyPessimisticLockTest() throws InterruptedException {
        int count = 100;

        ExecutorService executorService = Executors.newFixedThreadPool(32);
        CountDownLatch latch = new CountDownLatch(count);
        for (int i = 0; i &lt; count; i++) {
            executorService.execute(() -&gt; {
                try{
                    roomService.increaseBedCountPessimisticLock(3L);
                }catch (Exception e){
                    e.printStackTrace();
                }
                latch.countDown();
            });
        }
        latch.await();

        Room room = roomRepository.findById(3L).get();
        log.info(&quot;room.getBedCount() : &quot; + room.getBedCount());
        Assertions.assertThat(room.getBedCount()).isEqualTo(count);
    }</code></pre>
<p>비관적락은 
<code>@Lock(LockModeType.PESSIMISTIC_WRITE)</code>
어노테이션을 통해 구현해줄 수 있다.</p>
<p>이때 생성되는 SQl쿼리는 아래와 같다.</p>
<pre><code class="language-sql">SELECT * FROM my_table WHERE id = 1 FOR UPDATE;</code></pre>
<p>뒤에 <code>FOR UPDATE</code> 가 붙는 것을 알 수 있는데...
해당 명령어를 수행하면 위 SQL문으로 수정한 타겟 ROW가 트랜잭션이 끝날 때까지 락이 걸리게 된다.</p>
<p>--&gt; 시간자체가 느려지며, 데이터 정합성이 최우선인 상황에서 사용하는 것이 유리하다.</p>
<hr />
<h2 id="7-jpa에서-낙관적-락">7. JPA에서 낙관적 락</h2>
<blockquote>
<p>먼저 버전을 어떻게 관리하는 것일까??</p>
</blockquote>
<p>*<em>1. 버전 컬럼의 역할
*</em></p>
<ul>
<li>JPA엔티티에 Version 어노테이션을 추가하면, JPA는 버전관리 칼럼을 사용한다.</li>
<li>데이터 조회, 수정시마다 현재 버전을 확인한다. </li>
<li>버전 충돌이 발생하면 예외(OptimisticLockException)발생</li>
</ul>
<p>*<em>2. 동작 상세
*</em></p>
<p>1) 트랜잭션 A가 상품 데이터를 조회</p>
<pre><code class="language-sql">SELECT * FROM product WHERE id = 1;</code></pre>
<p>조회 결과 해당 상품 레코드에 버전값이 1로 저장되어있습니다.
<code>{ id: 1, name: &quot;상품A&quot;, stock: 100, version: 1 }</code></p>
<p>2) 트랜잭션 A가 상품 정보중 stock값을 100에서 90으로 변경합니다.</p>
<pre><code class="language-sql">UPDATE product 
SET stock = 90, version = version + 1 
WHERE id = 1 AND version = 1;</code></pre>
<p>이 때 조건은
version = 1이 만족해야 업데이트가 성공합니다.
또한 version값을 1에서 2로 증가시키며 업데이트가 종료됩니다.</p>
<p>3) 트랜잭션 B: 데이터 수정 시도
트랜잭션 B는 처음 조회한 데이터의 version값이 1이었음을 알고 있습니다.
이때 stock값을 90에서 80으로 변경 시도합니다.</p>
<pre><code class="language-sql">UPDATE product 
SET stock = 80, version = version + 1 
WHERE id = 1 AND version = 1;</code></pre>
<p>문제 발생
연산 마지막에 version값 조회 결과 2인것으로 확인됨
따라서 조건에 만족하지 않아 업데이트 실패후  OptimisticLockException 예외를 발생시킵니다.</p>
<p>이때 version값들은 테이블 별로 세팅되는 것이 아니라 레코드 별로 세팅됩니다.
예시</p>
<pre><code>Product Table
+----+-------+-------+---------+
| ID | Name  | Stock | Version |
+----+-------+-------+---------+
| 1  | 상품A |   100 |       1 |
| 2  | 상품B |    50 |       2 |
+----+-------+-------+---------+</code></pre><hr />
<h3 id="jpa-낙관적-락-종류">JPA 낙관적 락 종류</h3>
<p>*<em>1. NONE
*</em></p>
<ul>
<li><p>동작: 엔티티에 @Version이 있으면 JPA가 기본적으로 사용하는 동작입니다.
  •    데이터를 수정했을 때만 버전 값이 증가합니다.
  •    커밋 시점에 조회했던 버전 값과 현재 버전 값이 다르면 예외 발생(버전 충돌).</p>
</li>
<li><p>예시:</p>
</li>
<li><ul>
<li><ol>
<li>트랜잭션 A: 상품 정보를 수정하면서 version 값을 확인.</li>
</ol>
</li>
</ul>
</li>
<li><ul>
<li>•    조회 당시 version은 1.
•    수정 후 version을 2로 증가.</li>
</ul>
</li>
<li><ul>
<li><ol start="2">
<li>트랜잭션 B: 같은 상품 정보를 수정하려고 시도.</li>
</ol>
</li>
</ul>
</li>
<li><ul>
<li>•    하지만 트랜잭션 A가 이미 version을 2로 변경했기 때문에 버전 충돌 예외 발생.</li>
</ul>
</li>
<li><p>쉽게 이해하기:</p>
</li>
<li><p>*“내가 수정할 때, 누군가 내 뒤에서 몰래 수정했으면 알려줘!”</p>
</li>
<li><p>*</p>
</li>
</ul>
<ol start="2">
<li>OPTIMISTIC</li>
</ol>
<ul>
<li><p>동작: 조회만 해도 버전 값을 증가시킵니다.
  •    데이터를 단순히 읽는 동작도 변경 기록을 남겨, 다른 트랜잭션이 알 수 있도록 합니다.</p>
</li>
<li><p>예시:</p>
</li>
<li><ul>
<li><ol>
<li>트랜잭션 A: 상품 정보를 조회.</li>
</ol>
</li>
</ul>
</li>
<li><ul>
<li>•    조회 후, version이 1에서 2로 증가.</li>
</ul>
</li>
<li><ul>
<li><ol start="2">
<li>트랜잭션 B: 상품 정보를 수정하려고 시도.</li>
</ol>
</li>
</ul>
</li>
<li><ul>
<li>•    수정하려는 순간, 트랜잭션 A가 조회로 version을 증가시켰으므로 버전 충돌 예외 발생.</li>
</ul>
</li>
<li><p>쉽게 이해하기:</p>
</li>
<li><p>*“내가 데이터만 살짝 들여다봐도, 누군가 수정하려고 하면 충돌을 알려줄게!”</p>
</li>
<li><p>*</p>
</li>
<li><p>*3. OPTIMISTIC_FORCE_INCREMENT</p>
</li>
<li><ul>
<li></li>
</ul>
</li>
<li><p>동작: 낙관적 락을 사용하면서 버전 정보를 강제로 증가시킵니다.
  •    데이터 수정 여부와 관계없이, 조회만으로도 버전 값 증가.
  •    이 옵션은 낙관적 락의 보조 역할로, 데이터 처리의 동시성을 더 강하게 제어합니다.</p>
</li>
<li><p>예시:</p>
<ol>
<li>트랜잭션 A: 상품 정보를 조회.
•    조회 후, version 값이 1에서 2로 강제로 증가.</li>
<li>트랜잭션 B: 상품 정보를 수정하려고 시도.
•    트랜잭션 A가 조회 시 강제로 version을 올려놨기 때문에 버전 충돌 예외 발생.</li>
</ol>
</li>
</ul>
<ul>
<li>쉽게 이해하기:</li>
<li>*“내가 데이터만 보고 있어도, 강제로 버전을 올려서 누군가 수정하려 하면 미리 막아줄게!”</li>
<li>*</li>
</ul>
<blockquote>
<p>2번 3번의 차이를 모르겠다. 강제로 버전을 올린다는 것의 의미는 어떤것일까??</p>
</blockquote>
<p>2번은 조회하면 버전이 증가할 수 있지만, 기본적으로 &quot;수정하려는 시도&quot;가 있어야 한다..!!!!</p>
<hr />
<h2 id="8-jpa-낙관적락-코드">8. JPA 낙관적락 코드</h2>
<p><code>엔티티와 서비스</code></p>
<pre><code class="language-java">@Entity
public class Room{
    ...

    @Version
    private Long version;
}
public interface RoomRepository extends JpaRepository&lt;Room, Long&gt; {
    @Lock(LockModeType.OPTIMISTIC)
    @Query(&quot;select r from Room r where r.id = :id&quot;)
    Optional&lt;Room&gt; findByIdWithLock(@Param(&quot;id&quot;) Long id);
}
@Service
public class RoomServiceImpl implements RoomService {
    private final RoomRepository roomRepository;

    @Transactional
    public void increaseBedCountOptimisticLock(long id){
        Room room = roomRepository.findByIdWithLock(id).orElseThrow();
        room.increaseBed();
    }
}</code></pre>
<p><code>테스트 코드</code></p>
<pre><code class="language-java">@Test
    @DisplayName(&quot;동시성 테스트 - 낙관적락&quot;)
    public void concurrencyOptimisticLockTest() throws InterruptedException {
        int count = 100;

        ExecutorService executorService = Executors.newFixedThreadPool(32);
        CountDownLatch latch = new CountDownLatch(count);
        for (int i = 0; i &lt; count; i++) {
            executorService.execute(() -&gt; {
                try{
                    roomService.increaseBedCountOptimisticLock(3L);
                }catch (Exception e){
                    e.printStackTrace();
                }
                latch.countDown();
            });
        }
        latch.await();

        Room room = roomRepository.findById(3L).get();
        log.info(&quot;room.getBedCount() : &quot; + room.getBedCount());
        Assertions.assertThat(room.getBedCount()).isEqualTo(count);
    }</code></pre>
<p>낙관적락은 락이 없기때문에... 테스트 결과 당연히 실패하게 된다.
ObjectOptimisticLockingFailureException이 발생한다.</p>
<p>이를 해결하기 위해 Spring AOP를 사용해줄 수 있다.</p>
<hr />
<h3 id="spring-aop를-통한-트랜잭션-retry횟수-증가">spring AOP를 통한 트랜잭션 Retry횟수 증가</h3>
<p><strong>코드</strong></p>
<pre><code class="language-java">@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Retry {
    int value() default 20;
}
@Log4j2
@Order(Ordered.LOWEST_PRECEDENCE - 1)    //@Transactional annotation보다 먼저 실행되어야하므로.
@Aspect
public class RetryAspect {

    @Around(&quot;@annotation(retry)&quot;)
    public Object doRetry(ProceedingJoinPoint joinPoint, Retry retry) throws Throwable {
        int maxRetry = retry.value();

        Exception exceptionHolder = null;
        for (int retryCount = 1; retryCount &lt;= maxRetry; retryCount++) {
            try{
                return joinPoint.proceed();
            }catch (Exception e){
                log.error(&quot;[retry] try count ={}/{}&quot;, retryCount, maxRetry);
                exceptionHolder = e;
            }
        }
        throw exceptionHolder;
    }
}
@Service
public class RoomServiceImpl implements RoomService {
    private final RoomRepository roomRepository;

    @Retry
    @Transactional
    public void increaseBedCountOptimisticLock(long id){
        Room room = roomRepository.findByIdWithLock(id).orElseThrow();
        room.increaseBed();
    }
}</code></pre>
<p>retry횟수를 강제로 20으로 늘려주니 100개 요청에 대해서 문제없이 테스트는 통과했다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/b2540050-56a5-4b63-baac-e3ebabe05d46/image.png" /></p>
<p>하지만 시간자체도 지연이 되면서 합리적인 방법이 맞을지 의문이든다.</p>
<hr />
<h3 id="retry횟수를-늘리면-데이터-정합성을-보증하는가">retry횟수를 늘리면 데이터 정합성을 보증하는가?</h3>
<p>retry는 실패했던 트랜잭션을 단순히 롤백후 재실행하기 때문에 자연스럽게 동시성 발생시 테스트 통과확률을 높였다.
하지만 그렇다고 해서 실패확률이 0인 것은 아니다.</p>
<blockquote>
<p>따라서 retry횟수를 높이는 것은 실패확률을 줄이나.. 실패가 안한다는 것은 아니다.</p>
</blockquote>
<h2 id="9-다음으로">9. 다음으로...</h2>
<p>지금까지 락킹에대한 디테일에 대한 학습을 하고 정리를 해봤다.
다음에는 이전 프로젝트에서 무작정 비관적락으로 해결했던 동시성문제를 해당 이론을 응용해 제대로된 설계와 코딩을 해보겠다.</p>