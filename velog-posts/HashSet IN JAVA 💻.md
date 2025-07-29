<h3 id="hashset의-내부-동작-방식과-중복-제거-메커니즘을-설명하고-hashset이-효율적인-중복-체크를-할-수-있는-이유를-설명해주세요">HashSet의 내부 동작 방식과 중복 제거 메커니즘을 설명하고, HashSet이 효율적인 중복 체크를 할 수 있는 이유를 설명해주세요.</h3>
<p>_(Java17기준으로 설명됩니다....)
_</p>
<h1 id="1-hashset의-동작-원리">1. HashSet의 동작 원리</h1>
<h3 id="1-메인-재료">1. 메인 재료</h3>
<p>HashSet의 재료는 두가지 입니다.</p>
<p>1) <code>HashMap</code>
2) <code>PRESENT (Object 인스턴스)</code></p>
<p>실제 코드의 멤버 변수로도 이 둘이 사용됩니다.
그렇다면... 이 재료로 어떻게 HashSet을 구현할까요??</p>
<h3 id="2-add-연산">2. Add 연산</h3>
<p>알다시피 HashMap의 Key는 중복이 될 수가 없습니다.
Add 연산에서는 이러한 성질을 사용합니다.
하나의 원소가 HashMap의 Key가 됩니다.</p>
<blockquote>
<p>그렇다면 재료 HashMap의 각 Key의 Value는❓</p>
</blockquote>
<p>이때 쓰이는 것이 바로 <strong>PRESENT</strong>입니다.
즉 더미값의 value를 넣어주고 Key를 추가해 Set(집합)의 원소를 중복없이 추가가 가능해집니다.</p>
<p>*<em>실제 코드에 설명된 주석
*</em></p>
<pre><code class="language-java">// Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();</code></pre>
<h3 id="3-remove-연산">3. Remove 연산</h3>
<p>위의 재료로 어떻게 활용 하는지 이해하셨을 것입니다.
그렇다면 제거 역시 단순히 해당 Key를 재료 HashMap에서 제거를 해주면 그만입니다..!!</p>
<h3 id="4-hashset이-효율적인-중복체크가-가능한이유">4. HashSet이 효율적인 중복체크가 가능한이유?</h3>
<p>먼저 중복체크는 <code>O(1)</code> 의 시간복잡도 입니다. 왜냐하면 <strong>HashSet</strong>내부 <strong>HashMap</strong>에 key형태로 원소가 저장중이기에 HashMap의 find연산과 같은 시간복잡도입니다. (Hash에 대한 설명은 생략합니다...) 그렇기 때문에 중복체크에서 매우 빠르기에 효율적인 중복체크가 가능합니다.</p>
<hr />
<h1 id="정리">정리</h1>
<p>지금까지 HashSet의 동작원리를 이해했습니다.
HashSet은 사실 HashMap의 변형이었어요... 그렇기 때문에 HashMap을 이해했다면 아주 쉽게 이해할 수 있었을 것입니다.</p>
<p>감사합니다.</p>