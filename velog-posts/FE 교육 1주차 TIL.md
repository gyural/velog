<h1 id="span-style-color-royalbluereact란span"><span style="color: royalblue;">React란??</span></h1>
<p>페이스북에서 만든 js 웹프레임 워크이며 정말 많이 사용된다.</p>
<h2 id="span-style-color-royalbluenpm-vs-yarnspan"><span style="color: royalblue;">NPM vs YARN??</span></h2>
<p>이 둘은 라이브러리, 모듈들을 쉽게 설치 관리하게 해주는 패키지들이며,
앞으로의 교육에서는 성능이좋고 크기가 작은 YARN을 사용해 줄 것이다.</p>
<hr />
<h2 id="span-style-color-royalblue리액트-프로젝트-시작하기span"><span style="color: royalblue;">리액트 프로젝트 시작하기</span></h2>
<h3 id="1-node-js-설치">1) Node js 설치</h3>
<ul>
<li>구글링을 통해 최신버전을 설치한다.</li>
</ul>
<h3 id="2-yarn-설치">2) yarn 설치</h3>
<ul>
<li>vs코드 내에서 터미널 창을 열고 다음 명령어를 입력한다.<pre><code class="language-Bash">yarn add global crete-react-app</code></pre>
</li>
</ul>
<h3 id="3-cra-react-app-폴더-생성">3) CRA (react app 폴더 생성)</h3>
<ul>
<li>해당 터미널에<pre><code class="language-Bash">yarn create react-app your-app-name</code></pre>
</li>
</ul>
<hr />
<h1 id="span-style-color-royalbluecomponentspan"><span style="color: royalblue;">Component??</span></h1>
<p>컴포넌트는 재사용 가능한 UI를 나타내는 React의 핵심 요소입니다
리액트 구조는 모두 Component로 이루어진다.</p>
<h2 id="props">props??</h2>
<p>React 컴포넌트 간에 데이터를 전달하고 부모 컴포넌트에서 자식 컴포넌트로 정보를 전송하는 데 사용되는 객체입니다.</p>
<hr />
<h2 id="span-style-color-royalblue리액트에서-styling-span"><span style="color: royalblue;">리액트에서 styling </span></h2>
<h3 id="1-인라인-styling">1. 인라인 styling</h3>
<pre><code class="language-js">import React from &quot;react&quot;;

function Hello(){
     return(
        &lt;&gt;
              &lt;div style ={{width: &quot;100px&quot;, height: &quot;150px&quot;}}&gt;
                  Hello World!
              &lt;/div&gt;
          &lt;/&gt;
    )
}</code></pre>
<h3 id="2-styled-component">2. Styled-component</h3>
<p>사용법1)
styled-component 모듈 설치</p>
<pre><code class="language-Bash">yarn add styled-components</code></pre>
<p>사용법2)
코딩하기</p>
<pre><code class="language-js">import styled from &quot;styled-components&quot;;

function testCompoent(){

    const StyledButton = styled.button`
        color: red;
        background-color: royalblue;

    `;

}
    return &lt;StyledButton&gt; 나만의 버튼 &lt;/StyledButton&gt;;
</code></pre>
<h3 id="span-style-color-red주의-사항-span"><span style="color: red;">주의 사항 :</span></h3>
<p>styled 컴포넌트도 컴포넌트이기 때문에 앞에 알파벳을 대문자로 사용해야 한다!!!</p>