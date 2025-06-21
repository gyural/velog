<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/14c91e11-35cf-44c2-9eee-a41c9f3e9fa4/image.png" /></p>
<h1 id="배경">배경</h1>
<p>네00 면접 질문에서 가장 많이 사용한게 뭐냐 그리고 Java21, Java17이라고 했더니 각 특징에 대해서 물어봤지만 그에 대한 답을 하지 못했다… 따라서 공부해보고자 한다…!!</p>
<h1 id="추가">추가</h1>
<p>가장 영향력 있는 것 부터 정리해본다…!!</p>
<p>또한 LTS에서 처음 등장했기보다 LTS에서 제대로 알려지게된 것들을 말한다 </p>
<p>Ex) Java16에 프리뷰 → JAVA17(LTS) 공식 등장</p>
<hr />
<h1 id="java-17">Java 17</h1>
<p>1) 먼저 LTS이다. 또한 spring3.0버전 부터는 17버전 부터 지원한다..!!!</p>
<p>→ 그래서 이전 버전을 사용자들이 정착하기 좋은 버전인듯… ㅇㅇ</p>
<h3 id="내기준--가장-영향있는-특징-으로-정렬">내기준  가장 영향있는 특징 으로 정렬</h3>
<h3 id="0-textblock">0) TextBlock</h3>
<p><strong>Java 11</strong></p>
<p><code>Java 11</code> 버전에서는 <code>Json</code> 형식의 문자열을 다음과 같이 표현해야 했다.</p>
<p>한눈에 봐도 가독성이 매우 안좋은 것을 확인할 수 있다.</p>
<pre><code class="language-java">String jsonString = &quot;{\n&quot; +
    &quot;  \&quot;name\&quot;: \&quot;Jinny\&quot;,\n&quot; +
    &quot;  \&quot;age\&quot;: 20\n&quot; +
    &quot;}&quot;;</code></pre>
<p><strong>Java 17</strong></p>
<p><code>Java 17</code>에서는 텍스트 블록을 제공해서 3개의 큰 따옴표로 랩핑해서 표현할 수 있다.</p>
<pre><code class="language-java">String jsonString = &quot;&quot;&quot;
        {
          &quot;name&quot;: &quot;Jinny&quot;,
          &quot;age&quot;: 20
        }
        &quot;&quot;&quot;;</code></pre>
<hr />
<h3 id="1-pattern-matching-for-instanceof">1) <strong>Pattern Matching for instanceof</strong></h3>
<p>해당 instance검증 함수를 통해 Type캐스팅을 생략할 수 있다..</p>
<p>약간 TypeScript에서 사용하는 방식과 유사하다..!!</p>
<p><strong>Befroe</strong></p>
<pre><code class="language-java">if (obj instanceof String) {
    String s = (String) obj; // 형변환 필요
    ...
}</code></pre>
<p><strong>After</strong></p>
<pre><code class="language-java">if (obj instanceof String s) {
    ... // 여기서 s는 String 타입으로 안전하게 추론됨
}</code></pre>
<hr />
<h3 id="2-sealed-classes">2) <strong>Sealed Classes</strong></h3>
<p>상속에 대해 제한및 허용을 정해줄 수 있다..!!</p>
<p><strong>code</strong></p>
<pre><code class="language-java">public sealed class Shape permits Circle, Rectangle {}

final class Circle extends Shape {}
final class Rectangle extends Shape {}</code></pre>
<hr />
<h3 id="3-strong-encapsulation-of-jdk-internals-jep-403">3) Strong <strong>Encapsulation of JDK Internals (JEP 403)</strong></h3>
<p>JAVA내부의 JVM을 변화 시키는 비공식 API들을 차단함</p>
<p>따라서 기존 레거시들의 에러를 초래하긴 했지만 안정성을 챙기게됨</p>
<p>→ 물론 내부 설정 변경으로 비공식 기능들을 사용할 수 있다고 함</p>
<hr />
<h1 id="java-21">Java 21</h1>
<h3 id="1-virtual-thread-처리방식-변화">1) Virtual thread 처리방식 변화</h3>
<p>기존에 처리방식과 JAVA21이후 처리방식의 차이는 아래 그림 과 같다
<img alt="" src="https://velog.velcdn.com/images/gyural/post/d65dede6-aba9-4106-bbe3-9c50a006988e/image.png" />[출처 : 코규리 <a href="https://guti-coding.tistory.com/167%5D">https://guti-coding.tistory.com/167]</a></p>
<p>스레드에서 I/O작업 발생시에 Blocking이 일어나는데 이때 기존방식은 스레드를 직접 가져와서 사용하다보니 blocking을 마냥 기다려야했다.</p>
<p>하지만 After버전에서는 가상스레드가 Carrier스레드에 할당되어있고, 이 때문에 Blocking이 길어지면 해당 가상스레드말고 대기중인 스레드를 Carrier스레드에 할당한다.</p>
<p>실제 성능도 1.5배 상승했다고 한다.. (출처 :규리 <a href="https://guti-coding.tistory.com/167">https://guti-coding.tistory.com/167</a>)</p>
<hr />
<h3 id="2-text-block--보간">2) Text Block + 보간</h3>
<p>파이썬에서 가장 좋았던 경험중 하나는 F string이었다. 이경험을 JAVA21부터 누릴 수 있다.</p>
<pre><code class="language-java">String profile = F.&quot;&quot;&quot;
            이름: \{name}
            나이: \{age}세
            상태: \{age &gt;= 20 ? &quot;성인&quot; : &quot;미성년자&quot;}
            &quot;&quot;&quot;;</code></pre>
<p>—&gt; 코드 가독성, 작성난이도 획기적으로 변할 것 같다.</p>
<p>REF : </p>
<ul>
<li><a href="https://velog.io/@jinny-l/Java-17">https://velog.io/@jinny-l/Java-17</a></li>
<li><a href="https://techblog.gccompany.co.kr/%EC%9A%B0%EB%A6%AC%ED%8C%80%EC%9D%B4-jdk-17%EC%9D%84-%EB%8F%84%EC%9E%85%ED%95%9C-%EC%9D%B4%EC%9C%A0-ced2b754cd7">https://techblog.gccompany.co.kr/우리팀이-jdk-17을-도입한-이유-ced2b754cd7</a></li>
</ul>