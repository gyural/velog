<h3 id="문제-1-가장-많이-받은-선물">문제-1 가장 많이 받은 선물</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/258712">https://school.programmers.co.kr/learn/courses/30/lessons/258712</a></p>
<h3 id="해결-방법">해결 방법</h3>
<blockquote>
<ol>
<li>선물지수 Counting함수로 모듈화</li>
</ol>
</blockquote>
<blockquote>
<ol start="2">
<li>각 친구묶음 이중 for문으로 전체탐색</li>
</ol>
</blockquote>
<h3 id="코드">코드</h3>
<pre><code class="language-js">const getGiftDegree = (gifts, giftDegree) =&gt;{
    for (gLog of gifts){
        [a, b] = gLog.split(' ') 
        giftDegree[a] = giftDegree[a]+1 
        giftDegree[b] = giftDegree[b]-1 
    }

}
function solution(friends, gifts) {
    var answer = 0;

    let giftDegree = {}
    let nextMonth = {}
    for (n of friends){
        giftDegree[n] = 0
        nextMonth[n] = 0
    }

    getGiftDegree(gifts, giftDegree)
    console.log(giftDegree)
    for (i = 0; i &lt; friends.length-1; i++){
        for (j= i+1; j &lt; friends.length; j++){
//          gift순회하며 현재 쌍에 대해 i와 j중 횟수 비교
            let first = 0
            let second = 0
            let first_f = friends[i]
            let second_f = friends[j]
//          두명이서 받은 기록 확인
            for (gLog of gifts){
                [a, b] = gLog.split(' ')
                if (a === first_f &amp;&amp; b === second_f){
                    first +=1
                }else if(a === second_f &amp;&amp; b === first_f){
                    second +=1
                }
            }
//          NextMonth Counting
            if(first &gt; second){
                nextMonth[first_f] =  nextMonth[first_f]+1
            }else if(first &lt; second){
                nextMonth[second_f] =  nextMonth[second_f]+1
            }else if(first === second){
//              선물지수 비교
                if (giftDegree[first_f] &gt; giftDegree[second_f]){
                    nextMonth[first_f] =  nextMonth[first_f]+1
                }else if(giftDegree[second_f] &gt; giftDegree[first_f]){
                    nextMonth[second_f] = nextMonth[second_f]+1
                }
            }
        }
    }

    for (f of friends){
        if(nextMonth[f] &gt; answer){
           answer = nextMonth[f]  
        }  
    }

    return answer;
}</code></pre>
<h3 id="걸린시간">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/5d457325-adcc-4197-b0a8-42485d6d8b09/image.png" /></p>
<h3 id="회고">회고</h3>
<p>1) 문제를 다 읽지 않고 풀어서 Gift_record를 이차원 배열로 저장하는 것을 생각하지 못했다ㅜㅜ
2) 다른 객체 말고 그냥 인덱스와 Name을 매핑해주는 객체 하나만으로도 충분했다.</p>
<hr />
<h3 id="문제-2-붕대감기">문제 2 붕대감기</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/250137">https://school.programmers.co.kr/learn/courses/30/lessons/250137</a></p>
<h3 id="해결방법">해결방법</h3>
<blockquote>
<ol>
<li>healing 함수를 통해 간단한 체력회복 로직을 함수(모듈)화 함 </li>
</ol>
</blockquote>
<blockquote>
<ol start="2">
<li>1초 단위씩 for문을 순회하며 해당 시간에 대한 로직을 처리</li>
</ol>
</blockquote>
<h3 id="코드-1">코드</h3>
<pre><code class="language-js">//체력 회복 함수
const healing = (curHealth, value, max)=&gt;{
    const sumHealth = curHealth + value
    if(sumHealth &gt; max){
        return max
    }else{
        return sumHealth
    }
}
function solution(bandage, health, attacks) {
    var answer = 0;
    let currentHealth = health
    const maxTime = attacks[attacks.length-1][0]
    let attIndex = 0
    let accumTime = 0
    let timeCount = 0
    // console.log(attTime)
    for (let i=0; i &lt;= maxTime; i++){

        if (i === attacks[attIndex][0]){
            const val = -1 * attacks[attIndex][1]
            //공격당함
            currentHealth = healing(currentHealth, val, health)

            if (currentHealth &lt;= 0){return -1}
            accumTime = 0
            timeCount = 0
            attIndex += 1 
        }else{
            timeCount += 1
            currentHealth = healing(currentHealth, bandage[1], health)
            accumTime += 1
            if(accumTime === bandage[0]){
                console.log(&quot;???&quot;)
                currentHealth = healing(currentHealth, bandage[2], health)
                accumTime = 0
            }
        }

        console.log(currentHealth)
    }

    answer = currentHealth
    return answer;
}</code></pre>
<h3 id="걸린시간-1">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/541ac751-2127-4e3e-a9d4-4d65fe896721/image.png" /></p>
<h3 id="회고-1">회고</h3>
<p>굳이 모든 시간에 대해서 연산을 해줄 필요가 없었다. 
attTime일 때만 그 이전에 대한 회복 시간을 연산해주면 되었다
(다이나믹 프로그래밍)</p>
<hr />
<h3 id="문제-3-달리기-경주">문제 3 달리기 경주</h3>
<h3 id="해결방법-1">해결방법</h3>
<p>핵심적으로 저장해야 되는 데이터는 호출된 Player의 인덱스만을 확인하면 된다. 따라서 </p>
<blockquote>
<ol>
<li>idxToPlayer 와 PlayerToIndex mapping 객체 두 개를 사용해준다.</li>
</ol>
</blockquote>
<h3 id="코드-2">코드</h3>
<pre><code class="language-js">
function solution(players, callings) {
    const callingsAccum = {}
    let plyaerToidx = {}
    let idxToPlayer = {}
    players.forEach((item, idx)=&gt; {
        plyaerToidx[item] = idx
        idxToPlayer[idx] = item
    })
    callings.forEach((item, idx)=&gt; {
        const targetIndex = plyaerToidx[item]
        const loseP = idxToPlayer[targetIndex-1]
        const winP = idxToPlayer[targetIndex]
        plyaerToidx[loseP] = plyaerToidx[loseP] + 1
        plyaerToidx[winP] = plyaerToidx[winP] - 1
        idxToPlayer[targetIndex] = loseP
        idxToPlayer[targetIndex-1] = winP


    })
    const answer = []
    for (i=0; i &lt; players.length; i++){
        answer.push(idxToPlayer[i])
    }

    return answer;
}</code></pre>
<h3 id="걸린시간-2">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/5ba7d30e-8801-4edf-8631-d11ecda2b9e0/image.png" /></p>
<h3 id="회고-2">회고</h3>
<p>선형순회로 풀었으나 이때 시간복잡도가 매우 높아져(대충 5만 x 100만 == 5000억) 타임에러가 났다.
이때 Players의 Index를 찾을 때 선형탐색이 아닌 객체로만 저장해주면 되는 것을 너무 어렵게 생각했었다.</p>
<hr />
<h3 id="문제-4-도넛과-막대그래프">문제 4 도넛과 막대그래프</h3>
<p>DFS로 경로를 가져와서 일일히 확인하려 했으나 시간이 오래 걸릴 듯 하며
JS로 DFS/BFS가 익숙하지 않아 포기한 문제이다.
--&gt; 카카오 공식 문제풀이를 확인해봐도 도저히 못풀 문제일 것 같았다.</p>