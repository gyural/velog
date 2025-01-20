<p>REF : </p>
<ul>
<li><a href="https://mangkyu.tistory.com/204">https://mangkyu.tistory.com/204</a></li>
</ul>
<h1 id="1-문제제기">1. 문제제기</h1>
<p>지난번에 ELK를 통해 로깅 파이프라인 정립에 성공했다.
이후 실제 사용중인 서비스에 적용을 위해 로깅을 해본결과</p>
<blockquote>
<p>dispatch Servlet에서 예외가 발생해 Controller Advice를 통과하지 않았다... ㅠㅠ</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/3cc4f70d-3d48-4466-a8a1-3f46f0254e64/image.png" /></p>
<p>윈인은 Controller &gt; Service에 메서드가
예외처리를 try-catch문으로 하고있었기 때문이다..!!
따라서 service -&gt; dispatchServlet으로 바로 전가가 되었기 때문이다...</p>
<h1 id="2-예외-처리-패턴">2. 예외 처리 패턴</h1>
<p>그렇다면 효과적인 로깅을 하기위한 예외처리는 어떻게 구조화해야될까??
예외처리 패턴을 공부하며 알아보자..!!</p>
<h3 id="스프링-요청-순서도">스프링 요청 순서도</h3>
<p><code>WAS(톰캣) -&gt; 필터 -&gt; 서블릿(디스패처 서블릿) -&gt; 인터셉터 -&gt; 컨트롤러</code></p>
<p>이 때 컨트롤러 이하에서 예외가 발생할 경우...
별도의 예외 처리를 하지 않으면 WAS까지 에러가 전달된다.
<code>컨트롤러(예외발생) -&gt; 인터셉터 -&gt; 서블릿(디스패처 서블릿) -&gt; 필터 -&gt; WAS(톰캣)</code></p>
<h3 id="꿀팁🍯">꿀팁🍯</h3>
<p>JAVA의 Exception에는 <code>checked exception</code>과 <code>unchecked exception</code> 으로 나뉘는데 <code>checked exception</code>을 던지는 경우 해당 메서드의 throws를 포함해야한다.</p>
<p><strong>예시코드</strong></p>
<pre><code class="language-java">public ResponseEntity&lt;?&gt; partyCreate(PartyCreateRequest partyCreateRequest) throws Exception {
    try {
        // 코드 내용
        // 예외 발생 시
        throw new Exception(&quot;test exception&quot;);
    } catch (Exception e) {
        // 예외 처리 로직
    }
}</code></pre>
<h1 id="3-예외처리시-controller-advice-exception-handler를-통과하지-않은-이슈">3. 예외처리시 controller advice exception handler를 통과하지 않은 이슈</h1>
<p>첫번째 문제제기처럼 service레이어에서 에러를 발생하고 상위로 안던지고 dispatcherServlet으로 발생하는 경우를 어떻게 해야 예방할 수 있을까?</p>
<p>사실 컨트롤러에서 발생하는 에러는 기본적으로 controller Advice를 통과하게 되어있다.</p>
<blockquote>
<p>그렇다면 내 경우에는 왜 해당 에러가 발생했을까?</p>
</blockquote>
<p>1) 내가 요청했던 URL을 자세히 본다면</p>
<p>GET <a href="http://localhost:8080/api/v1/party/%7Bid%7D">http://localhost:8080/api/v1/party/{id}</a>
인 API였고</p>
<p>ID자리에 create라는 string값을 넣었었다.</p>
<p><strong>의도했던 바는</strong>
POST <a href="http://localhost:8080/api/v1/party/create">http://localhost:8080/api/v1/party/create</a>
인 API에 GET요청을 보내서 method not allow에러를 강제로 발생시키려 했으나
위의 GET 요청에 걸렸던 것이다...!!!</p>
<blockquote>
<p>그런데 GET요청에대한 controllerAdvice로 에러가 던져져야하는 것 아니야?</p>
</blockquote>
<p>해당 서비스 코드를 자세히 읽어보았지만 아무리 서비스코드가 부실해도 결국 controllerAdvice를 거치게 되어있다고 한다...!!</p>
<blockquote>
<p>그러면 도대체 이유가 뭐야?</p>
</blockquote>
<p>컨트롤러 어드바이스 코드를 잠시 들여다보자</p>
<pre><code class="language-java">public class Slf4jRestControllerAdvice {

    private Logger logger = LoggerFactory.getLogger(Slf4jRestControllerAdvice.class);

    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public String exception(Exception exception) {
        logger.error(&quot;Internal Server Exception: {}&quot;, exception.getMessage());
        return exception.getMessage();
    }

    @ExceptionHandler(IllegalArgumentException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String illegalArgumentException(IllegalArgumentException exception) {
        logger.error(&quot;IllegalArgumentException: {}&quot;, exception.getMessage());
        return exception.getMessage();
    }
}</code></pre>
<blockquote>
<p>이상한 점을 찾았는가???</p>
</blockquote>
<p>바로 @RestControllerAdvice 어노테이션이 없었던 것이다...</p>
<p><strong>싱겁지만 어찌되었든 에러는 해결되었다!</strong></p>
<hr />
<h1 id="4-디테일한-에러로그를-확인하고-싶어요">4. 디테일한 에러로그를 확인하고 싶어요..</h1>
<p>디테일한 에러로그를 알고싶은 이유는 다음과 같다...</p>
<p>만약 서비스코드에서만 접근이 가능한 사항들에 대해서 로깅을 하고 싶다..
예를 들어 &quot;규랄&quot;이라는 상품을 구매하는 서비스 코드 동작중 에러가 발생한다면...</p>
<blockquote>
<p>상품 구매 실패 - UserId: 규랄유저, ProductId: 규랄 상품</p>
</blockquote>
<p>이러한 디테일한 비지니스로직을 확인한다면... 어떻게 코드를 짜야할까??</p>
<p>결론은 생각 보다 간단합니다..!!</p>
<blockquote>
<p>Service code에서 message에 해당 정보들을 모두 반영시키면 됩니다.</p>
</blockquote>
<p>예를 들어 상품구매 API 요청시 에러가나고 아래와같은 로그을 찍고 싶다면...</p>
<p>아래와 같이 서비스 코드를 짜볼 수 있습니다.</p>
<pre><code class="language-java"> public void purchaseProduct(String userId, String productId) {
        try {
            // 상품 구매 로직
            boolean success = processPurchase(userId, productId);
            if (true) { //test를 위해 강제로 True값으로 뒀습니다.
                // message에 상세 정보를 추가
                throw new IllegalArgumentException(String.format(
                        &quot;상품 구매 실패 - UserId: %s, ProductId: %s&quot;, userId, productId
                ));
            }
        } catch (Exception e) {
            // 추가 로깅 (필요 시)
            System.out.println(&quot;Exception in purchaseProduct: &quot; + e.getMessage());
            throw e; // 예외를 다시 던짐
        }</code></pre>
<h3 id="요청postman">요청(postman)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/6719371e-cb17-4b54-b91d-4c54b3df3024/image.png" /></p>
<h3 id="결과-키바나">결과 (키바나)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/87fc7c74-4564-4e42-a5d5-d87fedd23989/image.png" /></p>
<p><strong>성공적으로 디테일한 비지니스 로직도 반영할 수 있게 되었습니다!!</strong></p>
<h1 id="5-에러가-아닌-로그도-알고-싶다면">5. 에러가 아닌 로그도 알고 싶다면?</h1>
<p>루트레벨의 로그는 <code>INFO</code>레벨이다. 따라서 logger인스턴스를 생성 후 INFO레벨로 로깅을 하면된다..!!</p>
<pre><code class="language-java">public class PurchaseService {
    private static final Logger logger = LoggerFactory.getLogger(PurchaseService.class);

    public void purchaseProduct(String userId, String productId) {
        try {
            // 상품 구매 로직
            boolean success = processPurchase(userId, productId);
            if (!success) {
                throw new IllegalArgumentException(String.format(
                        &quot;상품 구매 실패 - UserId: %s, ProductId: %s&quot;, userId, productId
                ));
            }
            logger.info(&quot;Purchase successful - UserId: {}, ProductId: {}&quot;, userId, productId);
        } catch (Exception e) {
            // 예외 로그 기록
            logger.error(&quot;Exception during purchase - UserId: {}, ProductId: {}&quot;, userId, productId, e);
            throw e; // 예외를 다시 던짐
        }
    }

    private boolean processPurchase(String userId, String productId) {
        // 실제 구매 처리 로직
        return false; // 테스트를 위해 실패로 설정
    }
}</code></pre>
<p>이로써 최종적으로 어떻게 하면 비지니스로직을 반영하고 어떻게 하면 효과적이며 분명한 로깅을 해볼수 있는지 알게되었다..!!</p>
<p>해당 로깅지식들을 활용하여 현재 참여하고 잇는 프로젝트에 안정성을 높여줄 것이 기대된다.</p>