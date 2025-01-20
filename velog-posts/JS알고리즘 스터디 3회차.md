<h1 id="1-카드뭉치">1. 카드뭉치</h1>
<h2 id="문제">문제</h2>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/159994">https://school.programmers.co.kr/learn/courses/30/lessons/159994</a></p>
<h2 id="해결방법">해결방법</h2>
<p>1) goal의 한단어 한단어를 현재 꺼낼 수 있는 지 비교연산 해줌</p>
<p>2) <code>fivot</code>의 사용
fivot을 통해 병합 문장 만들기에 이용가능한 카드로 제한하였다.</p>
<h2 id="코드">코드</h2>
<pre><code class="language-js">function solution(cards1, cards2, goal) {
    let answer = 'No';
    let [fivot1, fivot2] = [0, 0]

    for(targetWord of goal){
        if (fivot1 &lt; cards1.length &amp;&amp; cards1[fivot1] === targetWord){
            fivot1 += 1
        }else if(fivot2 &lt; cards2.length &amp;&amp; cards2[fivot2] === targetWord){
            fivot2 += 1
        }   
    }
    answer  = (fivot1 + fivot2) === goal.length? &quot;Yes&quot; : &quot;No&quot; 
    return answer;
}</code></pre>
<h2 id="회고">회고</h2>
<p>1-1) 1등 풀이 코드를 보니 해결방식은 같았다. 하지만 fivot사용 안하고 메모리 효율을 높일 수 있었다.</p>
<p>1-2) 카드뭉치에서 사용한 카드를 제거하는 방식을 이용해서 위 방식을 구현할 수 있다.</p>
<p>1-3) 카드뭉치를 하나의 스택자료구조로 사용해주는 방식이다.</p>
<h2 id="걸린시간">걸린시간</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/eac5ea6b-2e17-4690-9e3e-0f3a16884357/image.png" /></p>
<hr />
<h1 id="2-덧칠하기">2. 덧칠하기</h1>
<h2 id="문제-1">문제</h2>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/161989">https://school.programmers.co.kr/learn/courses/30/lessons/161989</a></p>
<h2 id="코드-1">코드</h2>
<pre><code class="language-js">
function solution(n, m, section) {
    var answer = 1;
    let start = section[0]

    section.forEach((item)=&gt;{
        const diff = item - start + 1
        if(diff &gt; m){
            start = item
            answer += 1
        }
    })

    return answer;
}</code></pre>
<h2 id="해결-방법">해결 방법</h2>
<p>문제에서 Section이 오름차순 정렬되어있다는 것이 힌트였다.</p>
<blockquote>
<p>1) section을 순차적으로 순회하며 <code>Start</code>위치에서 현재 sectinon까지의 길이가 M을 넘어가면 <code>Start</code>를 갱신한다</p>
</blockquote>
<blockquote>
<p>2) <code>Start</code>를 갱신할때 다시 덧칠하므로 그 때마다 Counting을 해준다.</p>
</blockquote>
<h2 id="걸린-시간">걸린 시간</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/d3aa984f-2ad2-448a-917a-ac8626331d9f/image.png" /></p>
<h2 id="회고-1">회고</h2>
<p>처음에는 벽을 배열로 만들어 주고 순회하며 0이 보일 때 마다 칠했지만 시간초과가 되었다.
이 후 핵심 정보인 Section만을 순회하는 방식으로 구현했다.
--&gt; 완벽하게 푼 것 같다.</p>
<hr />
<h1 id="3-대충-만든-자판">3. 대충 만든 자판</h1>
<h2 id="문제-2">문제</h2>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/160586">https://school.programmers.co.kr/learn/courses/30/lessons/160586</a></p>
<h2 id="해결-방법-1">해결 방법</h2>
<blockquote>
<ol>
<li><code>Mainmap</code> 객체를 통해 각 알파벳이 최소 몇번에 나올 수 있는 지 저장</li>
</ol>
</blockquote>
<blockquote>
<ol start="2">
<li>이중 for문을 통해서 <code>keymap</code>을 순회하며 가장 최단 횟수의 알파벳 저장</li>
</ol>
</blockquote>
<h2 id="코드-2">코드</h2>
<pre><code class="language-js">function solution(keymap, targets) {
    var answer = [];
    const mainMap = {}

    keymap.forEach((key)=&gt;{
        key.split('').forEach((alpha, idx)=&gt;{
            if(alpha in mainMap === false){
                mainMap[alpha] = idx+1
            }
            if(idx+1 &lt; mainMap[alpha]){
                mainMap[alpha] = idx+1
            }
        })
    })
    targets.forEach((sentence)=&gt;{
        let tmp = 0
        for(alpha of sentence){
            if(alpha in mainMap === false){
                tmp = -1
                break
            }
            tmp += mainMap[alpha]
        }

        answer.push(tmp)
    })
    return answer;
}</code></pre>
<h2 id="걸린-시간-1">걸린 시간</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/3d8e0171-097b-4730-8f07-6a5590615a63/image.png" /></p>
<h2 id="회고-2">회고</h2>
<p><code>mainMap</code>에 해당 알파벳이 저장되어 있는지 확인하는 연산은 필요가 없었다. 삼항 연산자를 사용하면 조건문이 <code>undefined</code> 여도 else문으로 분기가 된다!!</p>
<hr />
<h1 id="4-석유-시추">4. 석유 시추</h1>
<h2 id="문제-3">문제</h2>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/250136">https://school.programmers.co.kr/learn/courses/30/lessons/250136</a></p>
<h2 id="해결-방법-2">해결 방법</h2>
<p>1)     land를 matrix라 생각하여 첫번째 라인에 대해서 시추를 해준다
2) 시추시에 석유를 만나면 BFS탐색을 하며 카운팅 해준다.
하지만...
<code>정확도 60/60 효율성 13/40</code>
물론 DP를 사용하면 효율성도 통과할 수 있어 보인다. 시추를 했던 곳을 기록해주면 되는데 에너지가 없어서 더이상 못하겠다.... (JS못해먹겠다. 코테한정)</p>
<h2 id="코드-3">코드</h2>
<pre><code class="language-js">const vector = [[-1,0], [1,0], [0,-1], [0,1]]
const BFS = (start, map, visit, cnt) =&gt;{
    let res = 0
    let Q = []
    visit[start[0]][start[1]] = 1
    Q.unshift(start)
    while(Q.length &gt; 0 ){
        const cur = Q.pop()
        const [curY, curX] = cur
        res +=1
        for(v of vector){
            const newY = curY + v[0]
            const newX = curX + v[1]
            // console.log(newY, newX)
            // if(newY === 1 &amp;&amp; newX === 5){console.log(visit[newY][newX])}
            if(newY &lt; map.length &amp;&amp; newY &gt;=0 &amp;&amp; newX &lt; map[0].length &amp;&amp; newX &gt;=0 ){
                if (map[newY][newX] === 1 &amp;&amp; visit[newY][newX] === 0){
                    visit[newY][newX] = 1

                    Q.unshift([newY, newX])
                }
            }
        }
    }
    return res
}
function solution(land) {
    var answer = 0;
    const columns = land.length
    const rows = land[0].length
    let count = 2
    let county = []

    for(w = 0; w &lt; rows; w++){
        let  visit = Array.from(new Array(columns), () =&gt; new Array(rows).fill(0))
        let tmp = 0
        for(h = 0; h &lt; columns; h++){
            if(land[h][w] === 1 &amp;&amp; visit[h][w] === 0){
                tmp += BFS([h,w], land, visit, count)

        }
        answer = Math.max(tmp, answer)
    }

        }


    return answer;
}</code></pre>
<h2 id="회고-3">회고</h2>
<p>JS로는 알고리즘 풀기가 힘든 것 같다. 파이썬의 리스트 컴프리 핸션이나 이차원 배열에 접근 초기화에 비해 너무 복잡해지고 메모리 할당도 이상하다.
--&gt; 이 부분에서 삽질을 20분 가까이 했다.</p>
<h2 id="걸린시간-1">걸린시간</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/ca2dd7f3-0f55-4d22-8099-90377d53af1d/image.png" /></p>