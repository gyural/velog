<h1 id="1-공원산책">1. 공원산책</h1>
<h3 id="문제">문제</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/172928">https://school.programmers.co.kr/learn/courses/30/lessons/172928</a></p>
<h3 id="해결방법">해결방법</h3>
<p>1) 명령어를 순차적으로 확인한다.
2) 다음 위치를 <code>nxtY, nxtX</code> 에 저장하여 이동가능한지를 체크한다.
3) 가능하다면 이동 불가능하다면 이동 X</p>
<h3 id="코드">코드</h3>
<pre><code class="language-js">const getStart = (park)=&gt;{
    let X = 0
    let Y = 0
    for (str of park){
        X = str.indexOf(&quot;S&quot;)
        if (X &gt;= 0){
            return[Y,X]
        }
        Y = Y +1
    }
}
const isInner = (park, Y, X) =&gt;{
    if(park.length &gt; Y &amp;&amp; Y &gt;= 0 &amp;&amp; X &gt;=0 &amp;&amp; X &lt; park[0].length){

        return true
    }
    return false
}
const isPossible = (park, movement, current) =&gt;{
    let nxtY = current[0]
    let nxtX = current[1]
    const vector = {&quot;E&quot;:[0,1], &quot;S&quot;: [1,0], &quot;W&quot;:[0,-1], &quot;N&quot;:[-1,0]}

    for (i=0; i&lt; Number(movement[1]); i++){
        nxtY = nxtY + vector[movement[0]][0]
        nxtX = nxtX + vector[movement[0]][1]
        console.log(nxtY, nxtX)
        if(isInner(park, nxtY, nxtX)){
            if (park[nxtY][nxtX] === &quot;X&quot;){
                return false
            }
        }
        else{
            console.log(&quot;밖&quot;)
            return false
        }
    }

    return true
}
function solution(park, routes) {
    const vector = {&quot;E&quot;:[0,1], &quot;S&quot;: [1,0], &quot;W&quot;:[0,-1], &quot;N&quot;:[-1,0]}
    let answer = [0,0]
    let [startY,startX] = getStart(park)
    answer[0] = startY
    answer[1] = startX

      routes.forEach((cmd)=&gt;{
        const movement = cmd.split(&quot; &quot;)
        if (isPossible(park, movement, answer) === true){
                answer[0] += vector[cmd[0]][0] * Number(movement[1])
                answer[1] += vector[cmd[0]][1] * Number(movement[1])
            }
        console.log(answer)
    })

    return answer;
}</code></pre>
<h3 id="회고">회고</h3>
<p>1) forEach문에서는 break나 return문이 작동할 수 없다.
=&gt; 이거때문에 30분이상은 썼다 ㅠㅠ
=&gt; 알고리즘 문제는 forEach문의 효율이 안좋아 보인다.</p>
<h3 id="걸린시간">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/85c564e9-280b-45b8-a7b6-831cb81d6d53/image.png" /></p>
<hr />
<h1 id="2-바탕화면-정리">2. 바탕화면 정리</h1>
<h3 id="문제-1">문제</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/161990">https://school.programmers.co.kr/learn/courses/30/lessons/161990</a></p>
<h3 id="해결방법-1">해결방법</h3>
<p>각 드래그가 가능한 4개의 포인트를 전체 탐색했다.</p>
<p>1) countFileNum()
--&gt; 4개의 좌표를 주어졌을때 해당 드래그안에 파일 개수 리턴
2) getFileNum()
--&gt; matrix를 주고 File의 개수 리턴
3) solution()
--&gt; 모든 드래그 가능한 경우의수 4중 for문으로 탐색후 가장 작은 거리를 가지는 경우를 answer에 대입</p>
<h3 id="코드-1">코드</h3>
<pre><code class="language-js">const countFileNum = (sY, sX, eY, eX, wallpaper) =&gt;{
    let files = 0
    for (Y=sY; Y&lt;eY; Y++){

        for(X=sX; X&lt;eX; X++){
            if(wallpaper[Y][X] === &quot;#&quot;){
                files = files + 1
            }
        }
    }
    return files
}
function getFileNum(wallpaper){
    let res = 0
    for (line of wallpaper){
        for (char of line){
            if(char===&quot;#&quot;){
                res += 1
            }
        }

    }
    return res

}

function solution(wallpaper) {
    var answer = [0,0,0,0];
    let minSize = 2100000000
    const FileNum = getFileNum(wallpaper)


    for (sY = 0; sY &lt; wallpaper.length; sY++){
        for (sX=0; sX &lt; wallpaper[0].length; sX++){
            for (eY = sY; eY &lt;= wallpaper.length; eY++){
                for (eX=sX; eX &lt;= wallpaper[0].length; eX++){
                    if(countFileNum(sY, sX, eY, eX, wallpaper)===FileNum){

                        let Size = (eY-sY) + (eX-sX)
                        if(Size &lt; minSize){
                            minSize = Size
                            answer = [sY,sX,eY,eX]
                        }
                }}
            }
        }
    }

    return answer;
}</code></pre>
<h3 id="회고-1">회고</h3>
<p>1) 4중 for문을 썼다 웬만하면 안쓰는 방식이지만 matrix의 최대 크기가 50x50이어서 시간복잡도는 50^4이고 해볼만했다
2) 다이나믹 프로그래밍이 가능했다ㅠㅠㅠ
--&gt; 모든 파일의 좌표를 모은뒤 가장 왼쪽,위쪽 <code>minimum/maximum</code>을 구해주면 해결되는 문제였다 </p>
<h3 id="걸린시간-1">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/1cc7120f-e5db-41e2-9fb9-924b1ee06b7c/image.png" /></p>
<hr />
<h1 id="3-추억점수">3. 추억점수</h1>
<h3 id="문제-2">문제</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/176963">https://school.programmers.co.kr/learn/courses/30/lessons/176963</a></p>
<h3 id="해결방법-2">해결방법</h3>
<p>mapping 객체를 만들어 주고 리스트를 순회하면서 목표값을 Counting했다.
--&gt; 꽤 단순했던 문제</p>
<h3 id="코드-2">코드</h3>
<pre><code class="language-js">function solution(name, yearning, photo) {
    var answer = [];
    const scoreMapping = {}
//  Mapping해주기
    for (i=0; i &lt; name.length; i++){
        scoreMapping[name[i]] = yearning[i]
    }
    console.log(scoreMapping)
// 추억 카운팅
    photo.forEach((nameList)=&gt;{
        let count = 0
        nameList.forEach((name)=&gt;{
            if(scoreMapping[name]){
                count = count + scoreMapping[name]
            }
        })
        answer.push(count)
    })
    return answer;
}</code></pre>
<h3 id="회고-2">회고</h3>
<p>1) map메서드와 reduce메서드를 사용하면 1줄로도 가능한 문제였다. ㄷㄷㄷ</p>
<h3 id="걸린시간-2">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/20eb42b4-4dad-440b-82d2-182b75824739/image.png" /></p>
<hr />
<h1 id="4-요격-시스템">4. 요격 시스템</h1>
<h3 id="문제-3">문제</h3>
<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/181188">https://school.programmers.co.kr/learn/courses/30/lessons/181188</a></p>
<h3 id="해결방법-3">해결방법</h3>
<p>고민을 많이 해봐도 깔끔한 해결책이 안보였고 <code>그리디 알고리즘</code>으로 구현까지는 해봄
과정 : </p>
<ol>
<li><code>solution</code> : 가장 많이 요격 할 수 있는 위치부터 우선적으로 요격함</li>
<li><code>getMaxTargetIndex</code> : 가장 많이 요격시킬 수 있는 위치 반환 함수</li>
<li><code>getMinMax</code> : 미사일 가장 시작 범위부터 마지막 범위 Get </li>
</ol>
<h3 id="코드-3">코드</h3>
<pre><code class="language-js">const getMaxTargetIndex = (targetCheck, accumList) =&gt;{
    let maxIdx = -1
    let maxKill = -1
    accumList.forEach((targetSet, idx)=&gt;{
        let count = 0
        targetSet.forEach(target=&gt;{
            if(targetCheck[target] === false){
//             살아있으면 counting
               count += 1 
            }
        })
        if(maxKill &lt; count){
            maxKill = count
            maxIdx = idx
        }
    })

    return maxIdx

}
const getMinMax = (targets) =&gt; {
    let max_value = -1
    let min_value = 100000000
    targets.forEach((item)=&gt;{
        max_value = Math.max(item[1], max_value)
        min_value = Math.min(item[0], min_value)
    })
    return [min_value, max_value]
}

function solution(targets) {
    var answer = 0;
    const [min, max] = getMinMax(targets)
    const accumList = []
    const targetCheck = Array(targets.length+1).fill(false)
    targetCheck[0] = true

    for (let i = 0; i &lt;= max+1; i++){
        accumList.push(new Set())
    }

    targets.forEach((item, idx)=&gt;{
        for(i = item[0]; i&lt;item[1]; i++){
            accumList[i].add(idx+1)
        }
    })
    while(targetCheck.indexOf(false) &gt; 0){
        const shootingIdx = getMaxTargetIndex(targetCheck, accumList)
        accumList[shootingIdx].forEach(target=&gt;{targetCheck[target]=true})
        answer+= 1
    }
    return answer;

}</code></pre>
<h3 id="틀림">틀림</h3>
<ol>
<li>정확도 테스트 결과 18/100점으로 2개의 경우만 통과했다....</li>
<li>방법이 안떠오른다 그나마 <code>그리디 알고리즘</code>으로 가장 많이 제거할 수 있는 방향부터 미사일을 요격했는데 2개의 케이스에서만 정답이었다...</li>
</ol>
<h3 id="걸린시간-3">걸린시간</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/0b094cf3-1873-4ed2-8080-b275306939bd/image.png" /></p>