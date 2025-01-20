<h1 id="span-stylecolor-royalblue문제-1span"><span style="color: royalblue;">문제 1</span></h1>
<hr />
<h2 id="문제-설명">문제 설명</h2>
<p>더해야할 분수가 분모/분자 각 4개의 정수로 구해졌을 때
더하고 기약분수로 반환해야 한다!!!</p>
<h2 id="해결-방법">해결 방법</h2>
<h2 id="구현해야할-핵심은-2가지이다">구현해야할 핵심은 2가지이다!!</h2>
<ol>
<li><p>분수 더하기
분모들 두개 곱해서 통분하여 구해줬다.</p>
</li>
<li><p>기약분수 구현
재귀함수를 통해 계속해서 나눠줬다.</p>
<h2 id="코드">코드</h2>
<pre><code class="language-js">const minimize = (numer1, denom1) =&gt;{
 //작은수를 target으로 해주기
 let res1 = numer1;
 let res2 = denom1;
 const target = Math.min(numer1, denom1)

 if (target === 1){
     return [res1, res2]
 }
 for (divi = 2; divi &lt;= target; divi++){
     if (numer1 % divi === 0 &amp;&amp;  denom1 % divi === 0){
         //minimize 가능할 때
         return minimize(numer1 / divi, denom1 / divi)
     }
 }
 return [res1, res2]
</code></pre>
</li>
</ol>
<p>}
function solution(numer1, denom1, numer2, denom2) {
    var answer = [];</p>
<pre><code>answer = [...minimize(numer1 * denom2 + numer2 * denom1, denom1 * denom2)]
return answer;</code></pre><p>}</p>
<pre><code># &lt;span style=&quot;color: royalblue&quot;&gt;문제 2&lt;/span&gt;
---
## 문제 설명
call 배열에 따라서 players를 SWAP(추월) 해주면된다.
## 해결방법
직관적으로 2중 FOR문으로 해결해줄 수 있다.
### 하지만!!!
callings 배열의 최대길이 100만
players 배열의 최대길이 5만
연산 횟수는 최대 500억번이 된다.
따라서 
### 시간복잡도 목표 O(NlogN)
players의 inder를 map자료구조에다가 넣고
꺼내기를 반복해서 시간복잡도 목표를 구현했다!!!
## 코드
```js
function solution(players, callings) {
    var answer = [...players];
    const playerMap = new Map();
    // players 배열을 Map에 저장
    for (let i = 0; i &lt; players.length; i++) {
        playerMap.set(players[i], i);
    }
    for (let target of callings){
        const idx = playerMap.get(target);
        //SWAP
        let tmp = answer[idx - 1]
        answer[idx-1] =  answer[idx]
        answer[idx] = tmp
        playerMap.set(answer[idx], idx);
        playerMap.set(answer[idx-1], idx-1);

    }
    return answer;
}</code></pre><h1 id="span-stylecolor-royalblue문제-3span"><span style="color: royalblue;">문제 3</span></h1>
<hr />
<h2 id="문제-설명-1">문제 설명</h2>
<p>문제 조건규칙에 맞게 암호를 출력해내면 된다.</p>
<h2 id="해결-방법-1">해결 방법</h2>
<p>1) skippedList를 따로 만둠
2) 비암호화된 문자열을 선형 탐색으로 매개변수의 숫자만큼 shift해준후
3) 암호화된 문자열을 만들어줌</p>
<h2 id="코드-1">코드</h2>
<pre><code class="language-js">function solution(s, skip, index) {
    var answer = '';
    let skippedList = []
    //skippedList 완성
    for(let letter = 'a'; letter &lt;= 'z'; letter = String.fromCharCode(letter.charCodeAt(0) + 1)){
        if (!skip.includes(letter)){
            skippedList.push(letter)
        }
    }
    [...s].forEach((el)=&gt;{
        let afterIndex = 0
        const befIndex = skippedList.indexOf(el)

        if(befIndex + index &gt;= skippedList.length){
            afterIndex = (befIndex + index) % skippedList.length
        }else{
            afterIndex = befIndex + index
        }
        answer += skippedList[afterIndex]
    })
    return answer;
}</code></pre>
<h1 id="span-stylecolor-royalblue문제-4span"><span style="color: royalblue;">문제 4</span></h1>
<hr />
<h2 id="문제-설명-2">문제 설명</h2>
<p>문제에 나와있는 7단계를 그대로 구현하면 다</p>
<h2 id="해결-방법-2">해결 방법</h2>
<p>하드코딩....</p>
<h2 id="코드-2">코드</h2>
<pre><code class="language-js">function solution(new_id) {
    let answer = '';
    const smallChar = []
    const largeChar = []
    for (let i = 0; i &lt;= 25; i++ ){
        smallChar.push(String.fromCharCode(97+i))
        largeChar.push(String.fromCharCode(65+i))
    }
    //유효 문자
    const validChar = [...smallChar, '-', '_', '.', '0','1'
                      ,'2','3','4','5','6','7','8','9']

    //1단계 대문자 =&gt; 소문자
    for(let i = 0; i &lt; new_id.length; i++){
        if (largeChar.includes(new_id[i])){
            const idx = largeChar.indexOf(new_id[i])
            // new_id[i] = smallChar[idx]
            answer += smallChar[idx]
        }else{
            answer += new_id[i]
        } 
    }
    new_id = answer
    answer = ''
    //2단계 유효문자 제외 제거
    const delteIdx = []
    for(let i = 0; i &lt; new_id.length; i++){
        if (validChar.includes(new_id[i])){
            answer += new_id[i]
        }
    }
    new_id = answer
    answer = ''
    // 3단계 '.' 두번이상시 제거
    for(let i = 0; i &lt; new_id.length-1; i++){
        if (new_id[i] === '.' &amp;&amp; new_id[i+1] === '.'){
            //None
        }else{
            answer += new_id[i]
        }
    }
    answer += new_id[new_id.length-1]
    console.log(new_id[new_id.length-1])
    new_id = answer
    answer = ''
    //4단계 처음이나 마지막에 .이 위치하면 제거
    for(let i = 0; i &lt; new_id.length; i++){
        if (new_id[i] === '.' &amp;&amp; (i === 0 || i === new_id.length-1)){
            //None
        }
        else{
            answer += new_id[i]
        }
    }
    new_id = answer
    answer = ''
    //5단계 빈문자열 a대입
    if (new_id === ''){
        new_id = 'a'
    }
    answer = new_id
    //6단계
    if(answer.length &gt; 15){
        answer = answer.substring(0,15)
    }
    if(answer.)

    //7단계
    console.log(answer)
    if(answer.length &lt;= 2){
        let pluschar = answer[answer.length-1]
        for (let i = 0; i &lt; 3- answer.length; i++){
            answer += pluschar
        }
    }
    return answer;
}</code></pre>
<h1 id="span-stylecolor-royalblue문제-4span-1"><span style="color: royalblue;">문제 4</span></h1>
<hr />
<h2 id="문제설명-">문제설명 :</h2>
<p>배열내의 다른 인덱스 값의 합으로 나올 수 있는 경우를 
오름차순으로 반환해야 한다.</p>
<h2 id="해결방법-">해결방법 :</h2>
<p>이중 for문을 통해 선형 순회로 해결해줬다</p>
<h2 id="코드-3">코드</h2>
<pre><code class="language-js">function solution(numbers) {
    var answer = [];

    for (let i = 0; i &lt; numbers.length ;i++){
        const num1 = numbers[i]
        for (let j = 0; j &lt; numbers.length ;j++){
            if(i !== j){
                const num2 = numbers[j]
                const sum = num1 + num2
                !answer.includes(sum) &amp;&amp; answer.push(sum)
            }
        }
    }
    answer.sort(function(a, b)  {
        return a - b;
    });

    return answer;
}</code></pre>