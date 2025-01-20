<h1 id="span-style--color--royalblue문제1-배열의-유사도span"><span style="color: royalblue;">문제1. 배열의 유사도</span></h1>
<h2 id="문제-설명-">문제 설명 :</h2>
<p>문자열 배열 s1과 s2가 주어질 때 이 <strong>두 배열에서 일치하는 원소의 개수</strong>를 출력하는 함수를 작성하라!!!</p>
<h2 id="해결-방법-">해결 방법 :</h2>
<p>2중 for문을 통해서 각원소를 각각 비교했다.</p>
<h2 id="해답-코드">해답 코드</h2>
<pre><code class="language-js">function solution(s1, s2) {
    var answer = 0;
    for (word1 of s2){
        for (word2 of s1){
            if (word1 == word2){
                answer += 1 
            }
        }
    }
    return answer;
}</code></pre>
<h1 id="span-style--color--royalblue문제2-다음에-올-숫자span"><span style="color: royalblue;">문제2. 다음에 올 숫자</span></h1>
<h2 id="문제설명-">문제설명 :</h2>
<p>등차수열 혹은 등비수열 common이 매개변수로 주어질 때, 마지막 원소 다음으로 올 숫자를 return 하도록 solution 함수를 완성해보세요.</p>
<h2 id="해결방법-">해결방법 :</h2>
<p>등비수열인지 판별해주는 함수 i<strong>sGeometricSequence</strong> 함수를 만들어주고 이를통해 다음에 올 수를 계산해 줬다.</p>
<h2 id="해답-코드-1">해답 코드</h2>
<pre><code class="language-js">const isGeometricSequence = (sequence) =&gt;{
    const len = sequence.length
    if (sequence[len-2] / sequence[len-1] === sequence[len-3] / sequence[len-2]){
        return true
    }else{
        return false
    }

}

function solution(common) {
    const isGeo =  isGeometricSequence(common)
    var answer = 0;
    const len = common.length
    if (isGeo === true){
//         등비수열일 때
        const diff = common[len-1] / common[len-2]
        answer = common[len-1] * diff
    }else{
//         등차수열일 때
        const diff = common[len-1] - common[len-2]
        answer = common[len-1] + diff
    }
    return answer;
}</code></pre>
<h1 id="span-style--color--royalblue문제-3-특이한-정렬span"><span style="color: royalblue;">문제 3. 특이한 정렬</span></h1>
<h2 id="문제설명--1">문제설명 :</h2>
<ol>
<li><p>정수 배열에서 특정수에 가까운 수를 기준으로 정렬해야한다.</p>
</li>
<li><p>특정수에 가까운 정도가 같다면 숫자가 큰 순으로 정렬해야한다.</p>
</li>
</ol>
<h2 id="해결방법--1">해결방법 :</h2>
<p>단계 1) 새로운 배열을 만들어줬다.
단계 2) 새로운 배열은 각 원소가 배열이며 <strong><span style="color: royalblue;">[가까운 정도, 숫자]</span></strong> 를 의미한다.
단계 3) 새로운 배열 NewList의 두번째원소로 오름차순으로 정렬 후 첫번째 원소로 내림차순으로 정렬한다.
단계 4) 조건에 맞게 정렬된 새로운 배열을 결과 출력값에 맞게 출력한다.</p>
<h2 id="해답-코드-2">해답 코드</h2>
<pre><code class="language-js">function solution(numlist, n) {
    var answer = [];

    let newList = numlist.map((el) =&gt; {
        return [Math.abs(el - n), el];
    });

    newList.sort((a,b) =&gt; b[1]-a[1])
    newList.sort((a,b) =&gt; a[0]-b[0])

    answer = newList.map((el)=&gt;{
        return el[1]
    })
    return answer;
}</code></pre>