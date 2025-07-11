<h1 id="1-rebase-vs-merge-분석">1. Rebase vs Merge 분석</h1>
<p><code>rebase</code>와 <code>merge</code> 의 차이점을 설명하기 전에 공통점을 먼저 설명하겠습니다. 왜냐하면 차이점을 설명하기 이전에 공통점을 설명하게 되면 <em><strong>어떤 부분에서 극명한 차이가 나는지 더 확실하게 확인할 수 있기 때문입니다.</strong></em></p>
<h3 id="1-1-rebase와-merge의-공통점">1-1. Rebase와 Merge의 공통점</h3>
<p><code>Rebase</code>와 <code>Merge</code>모두 갈라진 상황에서 사용할 수 있는 명령입니다.
좀 더 자세히 말하면 브랜치가 두 개 이상 있고 이 브랜치들을 알맞게 합칠 때 사용하는 명령어입니다.
즉 합칠 때 어떤 측면들을 고려하는게 중요한지에 따라서 Rebase와 Merge 둘 중의 선택이 더 쉬워질 것입니다.</p>
<h3 id="1-2-브랜치를-합칠-때-중요한-점들">1-2. 브랜치를 합칠 때 중요한 점들</h3>
<p><strong>1) 충돌이 잘 안나는가?
*<em>합치는 과정에서 충돌은 불가피 합니다. 그렇기 때문에 합치는 과정에서 충돌에 대한 다양한 전략들이 존재하며 이러한 전략이 충돌 관리의 중요성을 나타냅니다.
*</em>2) 손실되는 코드가 없이 합쳐졌는가?</strong>
충돌관리 및 합치는 과정에서 손실되는 코드가 발생할 수 있습니다. 이는 엄청큰 리스크로 번질 수 있습니다. 물론 코드리뷰, CI전략등을 통해도 보완할 수 있지만 먼저 합치는 상황에서는 코드의 손실보다 위험한 것은 없을 것입니다.
<strong>3) 커밋 히스토리가 더 분명한가?</strong>
커밋 히스토리는 여러 측면에서 중요합니다. 포토폴리오가 될 수 있고, 에러가 났을 때 어느시점 정확히 발생했는지 판단할 때에도 너무 중요합니다.</p>
<p>중요한 점들은 크게 3가지입니다. 그런데 이것만으로 <code>rebase</code>와 <code>merge</code>의 판단에 대해서는 어렵습니다. 왜냐하면 두가지의 정확한 동작을 모르기 때문입니다. 두가지의 동작에 대한 비교를 해보겠습니다.</p>
<hr />
<h3 id="1-3-merge의-동작과정">1-3. Merge의 동작과정</h3>
<p>merge는 단순합니다. main으로 부터 분기된 <code>K-branch</code>의 모든 변경과정을 <code>main-branch</code>에 합치며 1개의 새로운 커밋을 만들어냅니다.
그림으로 보면 더 이해하기 쉽습니다.(각 알파벳은 서로다른 커밋입니다)</p>
<p><code>before merge</code></p>
<blockquote>
<pre><code class="language-js">      A---B---C (K-branch)
     /
    D---E---F---G (main-branch)</code></pre>
</blockquote>
<pre><code>
`after merge`
&gt;```js
      A---B---C (K-branch)
     /         \
    D---E---F---G---H (main-branch)</code></pre><p>k라는 사람이 k-branch를 판 이후 A-B-C의 커밋을 만들었고 해당 <code>branch</code>의 기능을 완료해서 <code>main-branch</code>에 merge이후 H라는 <code>branch</code>로 다시 생겼습니다.</p>
<h3 id="1-4-rebase의-동작과정">1-4 rebase의 동작과정</h3>
<p>rebase는 두개의 <code>branch</code>를 합친다는 점에서는 비슷할 수 있지만 핵심 동작은 다릅니다.
말 그래도 여러 <code>branch</code>의 순서관계를 재배치하는데 초점이 맞춰져 있습니다.
단순합치는 것이 아닌 배치를 하는 것이기때문에 <strong>여러 시나리오가 존재</strong>할 수 있습니다.</p>
<p><code>시나리오-1-Before</code></p>
<blockquote>
<pre><code class="language-js">          A---B---C topic
         /
    D---E---F---G master</code></pre>
</blockquote>
<pre><code>
`시나리오-1-After`
&gt;```js
                  A'--B'--C' topic
                 /
    D---E---F---G master</code></pre><p><code>시나리오-1-After(실제)</code></p>
<blockquote>
<pre><code class="language-js">D---E---F---G---A'---B'---C' topic</code></pre>
</blockquote>
<pre><code>--&gt; 실제로는 하나의 선이 됩니다.</code></pre><hr />
<p><code>시나리오-2-Before</code></p>
<blockquote>
<pre><code class="language-js">                            H---I---J topicB
                           /
                  E---F---G  topicA
                 /
    A---B---C---D  master</code></pre>
</blockquote>
<pre><code>
`명령어`
```bash
git rebase --onto master topicA topicB</code></pre><p><code>시나리오-2-After</code></p>
<blockquote>
<pre><code class="language-js">                  H'--I'--J'  topicB
                /
                | E---F---G  topicA
                |/
    A---B---C---D  master</code></pre>
</blockquote>
<pre><code>
말그대로 브랜치끼리의 재배치가 이루어집니다.
위 2개의 시나리오 말고도 더 많은 시나리오가 발생할 수 있습니다. 즉 `rebase`는 브랜치끼리의 배치만을 하는 것이 핵심 동작입니다.
이제 동작원리와 합칠 때의 중요한 점을 알았으니 **장단점을 비교해봅시다.**

---

### 1-5. Merge의 장단점
`장점`
**러닝커브가 낮다 :** 위에서도 보았듯이 시나리오도 1개이며 결국 합치기만 하는 동작입니다. 따라서 명령어를 작성하는 것도 단순하며 상대적으로 직관적으로 이해하기 쉽게 동작합니다.

**충돌관리가 쉽고 코드 손실 위험이 낮음 :** merge 시나리오-1을 보게되면 master의 최신 커밋인 G와 topic-branch 두가지만의 코드비교만으로 충돌이 발생할 수 있게됩니다. 즉 master에 어떤 부분이 사라지고 어떤부분이 추가되었는지 상대적으로 명확합니다.
따라서 충돌관리와 코드손실 위험에 유리합니다.

`단점`
**커밋 히스토리가 추적 어려움 :** merge를 하면 하나의 새로운 커밋으로 `Main-branch`에 추가됩니다. 따라서 이전에 정확히 어떤 작업을 했는지에 대한 추적이 어려울 수 있습니다.
또한 히스토리 추적이 상세하게 되지 않습니다.(merge이전 branch압축) 따라서 에러가 발생했을 때 정확히 어떤 커밋에서 발생했는지 판단하기 어려워집니다.

### 1-6. Rebase의 장단점
`장점` 
**커밋 히스토리 깔끔 :** 선형적으로 커밋 히스토리가 관리되기때문에 에러발생시 추적이 쉬워지며, 단계별 코드 체킹이 쉬워진다.

`단점`
**러닝커브가 높다 :** 위에서 봤듯이 시나리오만 여러개기 때문에 어떤상황에서 어떻게 써야할지에 대한 학습이 필요해진다. 즉 단순히 합치는 merge보다는 더 공부해야한다.
**충돌 위험이 높다 :** Merge는 여러개의 커밋을 압축해서 합친다면 Rebase는 여러개의 커밋을 하나하나 다시 쌓기 때문에 각각에서의 충돌이 모두 발생하면 충돌관리가 매우 복잡해진다.

---

# 2. 협업 시 Rebase와  Merge 어떻게 사용할까?

먼저 브랜치를 합치는 것은 필수이기에 `Merge`는 무조건 사용할 수 밖에 없다.
그리고 `Rebase`는 옵셔널이다.
### 2-1. Merge만 사용하기 적합한 경우

**✅ 커밋 히스토리에 대한 요구사항이 없는(X) 경우**
개인 프로젝트 및 토이프로젝트 같은 프로젝트들은 대부분 타이트한 커밋히스토리를 관리할 필요가 없다. 따라서 어려운 `Rebase`를 접목하지 않는 것이 좋다.

**✅ Git/GitHub숙련도가 낮은 경우**
Git 숙련도가 낮은 경우에는 기본 `Merge`및`Branch`관리만으로도 벅찰 것이다. 따라서 `Rebase`를 도입하는 것은 힘들 것이다.

**✅ 생산성이 중요한 경우**
Rebase까지 고려하며 협업을 하면 조금 더 소요가 생길것이고 이는 생산성의  저하(조금이라도..)가 발생할 수 있다.

### 2-2. Rebase를 도입하기 적합한 경우

**✅ 커밋 히스토리에 대한 요구사항이 있는(O) 경우**
버그가 발생했을 때 리스크가 크거나, 일의 진행도의 정확한 파악을 위해서는 정확하며 선형적인 커밋 히스토리가 필수이다.
**✅ Git/GitHub숙련도가 높은 경우**
Git에 숙련도가 높다면 충돌관리나 브랜치 전략에 대한 부분을 어렵지않게 해결할 것이며, 이럴때는`Rebase`를 도입해도 좋다.


### 2-3 Rebase + Merge 사용법

처음에도 소개했다시피 합치는 과정에서 이용하면 된다.

**1. main branch 것을 rebase pulll**
```bash
git pull origin develop --rebase</code></pre><p><strong>1-1. 충돌이 발생했다면 충돌 해결</strong></p>
<p><strong>1-2. 충돌이후 rebase계속</strong></p>
<pre><code class="language-bash">git rebase --continue</code></pre>
<p><strong>2. origin/branch로 push</strong>
main브랜치와 무사히 <code>rebase</code> 완료 되었기 때문에 푸쉬</p>
<pre><code class="language-bash">git push origin branch</code></pre>
<p><strong>3. GitHub에서 origin/main로 rebase merge</strong>
위의 과정에서 문제업이 rebase pull이 되었다면 자동적으로 rebase-merge가 가능해집니다.</p>
<h1 id="3-rebase-merge정리">3. Rebase, Merge정리</h1>
<p>이렇게 rebase와 merge에 대해서 분석하고 장단점, 적합환경, 사용법을 알아봤습니다.
정리하자면 두 개념은 합치는데 사용하는 Git 명령어들이며, 요구사항에 맞게 커밋스토리를 관리하는데에 사용할 수 있는 명령어 였습니다.</p>
<p>다음은 <code>fetch</code> <code>pull</code>을 알아보겠습니다.</p>
<hr />
<h1 id="4-pull-vs-fetch">4. pull vs fetch</h1>
<h3 id="4-1-pull이란">4-1 pull이란?</h3>
<p>pull은 어렵지 않습니다. 현재 로컬(local)에 작업하는 나의 브랜치가 원격(origin)보다 뒤쳐질 때 사용할 수 있는 명령어 입니다. 
즉 뒤쳐져있는 만큼의 커밋을 가져오게 됩니다. 그림으로 확인해보겠습니다.</p>
<p><code>before pull</code></p>
<pre><code class="language-js">          A---B---C master on origin
         /
    D---E---F---G master on local</code></pre>
<p><code>after pull</code></p>
<pre><code class="language-js">          A---B---C origin/master
         /         \
    D---E---F---G---H master</code></pre>
<p>1) 기존의 로컬은 원격에비해 <code>A</code> <code>B</code> <code>C</code> 만큼의 커밋이 뒤쳐져있습니다.
2) 이후 pull명령어로 <code>A</code> <code>B</code> <code>C</code>를 가져 왔지만 <code>F</code> <code>G</code> 만큼에 변경사항과의 충돌이 발생합니다.
3) 충돌을 해결한 이후 <code>H</code>라는 새로운 버전이 생기며 뒤쳐짐없이 모든 원격위 커밋을 가져오는데 성공합니다.</p>
<hr />
<h3 id="4-2-git-fetch란">4-2 git fetch란?</h3>
<p>git fetch는 변형된 pull이라고 생각하면 이해하기 편합니다.
fetch의 주목적은 여러개의 브랜치(branch)나 태그(tag)를 가져오는 것이 목적입니다.
이 때 기존에 있던 branch에 새로운 commit이 있다면 이도 가져옵니다.</p>
<p><code>before fetch</code></p>
<pre><code class="language-js">          A---B---C master on origin
         /
    D---E---F---G master on local</code></pre>
<p><code>after fetch</code></p>
<pre><code class="language-js">          A---B---C---K---L  origin/master
         /
D---E---F---G  master

                   X---Y  origin/feature</code></pre>
<p>1) 기존 로컬에 없던 origin<code>K</code> <code>L</code> 커밋들이 댕겨와집니다.
2) 기존 로컬에 없던 <code>feature-branch</code>가 가져와집니다. </p>
<p>git fetch는 이렇게 원격저장소와 다른점을 모두 가져오는 <strong><code>동기화</code></strong> 역할을 한다고 이해하면 편합니다.</p>
<hr />
<h1 id="5-마무리">5. 마무리</h1>
<p>지금까지 git의 핵심 명령어들을 알아봤습니다. 각각의 동작과 이해의 도움이 되었스면 좋겠습니다. 감사합니다😀</p>
<p>ref : </p>
<p>1) git-merge docs : <a href="https://git-scm.com/docs/git-merge">https://git-scm.com/docs/git-merge</a>
2) git-rebase docs : <a href="https://git-scm.com/docs/git-rebase">https://git-scm.com/docs/git-rebase</a>
3) git-pull docs : <a href="https://git-scm.com/docs/git-pull">https://git-scm.com/docs/git-pull</a>
4) git-fetch docs : <a href="https://git-scm.com/docs/git-fetch">https://git-scm.com/docs/git-fetch</a></p>