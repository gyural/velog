<h1 id="1-stream-map">1. Stream Map</h1>
<p><code>Stream&lt;InputClass&gt;</code> -&gt; <code>Stream&lt;OutPutClass&gt;</code>
단순히 stream의 타입을 바꿔주는 연산을 해준다.</p>
<p>예를 들어 회원 Stream(배열)이 있다고 하고 이 때 회원의 나이의 배열로 변환하고 싶어한다 그렇다면
<code>Stream&lt;Member&gt;</code> -&gt; <code>Stream&lt;Integer&gt;</code>
로 변환이 될 것이다.</p>
<h3 id="코드">코드</h3>
<pre><code class="language-java">List&lt;Member&gt; members = Arrays.asList(
            new Member(&quot;Alice&quot;, 25),
            new Member(&quot;Bob&quot;, 30),
            new Member(&quot;Charlie&quot;, 28)
        );

        List&lt;Integer&gt; ages = members.stream()
                                    .map(Member::getAge)  // 또는 .map(m -&gt; m.getAge())
                                    .collect(Collectors.toList());

        System.out.println(ages);  // [25, 30, 28]</code></pre>
<p>이때 <code>map.distinct()</code> <code>map.sorted</code>같은 메서드로 정렬 및 중복제거도 가능하다.</p>
<pre><code class="language-java">List&lt;Member&gt; members = Arrays.asList(
            new Member(&quot;Alice&quot;, 25),
            new Member(&quot;Bob&quot;, 30),
            new Member(&quot;Charlie&quot;, 28),
            new Member(&quot;David&quot;, 30)   // 중복된 나이
        );

        // 나이만 추출해서 중복 제거 + 오름차순 정렬
        List&lt;Integer&gt; sortedDistinctAges = members.stream()
                .map(Member::getAge)         // Stream&lt;Integer&gt;
                .distinct()                  // 중복 제거
                .sorted()                    // 오름차순 정렬
                .collect(Collectors.toList());

        System.out.println(sortedDistinctAges);  // 출력: [25, 28, 30]</code></pre>
<h1 id="2-flat-map">2. Flat Map</h1>
<p><code>Stream&lt;Stream&lt;OutPutClass&gt;&gt;</code> → <code>Stream&lt;OutPutClass&gt;</code>
중첩된 스트림 구조를 평탄화(Flatten)하여 하나의 스트림으로 만든다.</p>
<p>즉, <code>List&lt;List&lt;String&gt;&gt;</code> → <code>List&lt;String&gt;</code>
<code>Stream&lt;List&lt;String&gt;&gt;</code> → <code>Stream&lt;String&gt;</code>
이런 구조를 펼칠 때 사용된다.</p>
<h3 id=""></h3>
<pre><code class="language-java">st&lt;Member&gt; members = Arrays.asList(
            new Member(&quot;Alice&quot;, Arrays.asList(&quot;Reading&quot;, &quot;Traveling&quot;)),
            new Member(&quot;Bob&quot;, Arrays.asList(&quot;Cooking&quot;, &quot;Gaming&quot;)),
            new Member(&quot;Charlie&quot;, Arrays.asList(&quot;Traveling&quot;, &quot;Music&quot;))
        );

        // 모든 회원의 취미를 하나의 스트림으로 평탄화
        List&lt;String&gt; allHobbies = members.stream()
                .flatMap(member -&gt; member.getHobbies().stream())  // Stream&lt;String&gt;
                .distinct()     // 중복 제거
                .sorted()       // 정렬
                .collect(Collectors.toList());

        System.out.println(allHobbies);  // 출력: [Cooking, Gaming, Music, Reading, Traveling]</code></pre>
<h3 id="핵심-요약">핵심 요약</h3>
<table>
<thead>
<tr>
<th>메서드</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>map()</code></td>
<td>요소를 변환 (1:1 매핑)</td>
</tr>
<tr>
<td><code>flatMap()</code></td>
<td>중첩된 구조를 펼쳐서 평탄화 (1:N → 1:1 매핑)</td>
</tr>
</tbody></table>