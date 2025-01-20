<hr />
<h1 id="component">Component</h1>
<h2 id="리액트-앱을이루는-재사용이-가능한-가장-최소-단위">리액트 앱을이루는 재사용이 가능한 가장 최소 단위</h2>
<h1 id="state">State!!</h1>
<p>각 컴포넌트의 상태이며 컴포넌트가 가지고 관리하는 동적인 값
State가 변화하면 해당 컴포넌트는 리랜더링이 된다!!!</p>
<h2 id="state사용법">State사용법</h2>
<h3 id="usestae-hook사용">useStae() Hook사용</h3>
<pre><code class="language-js">export default function test(){
    const [message, setMessage] = useState(&quot;&quot;)
    //message를 test컴포넌트에서 State로 관리

}</code></pre>
<h1 id="props">Props!!</h1>
<p>컴포넌트간 데이터를 전달하기위한 방법이며
부모 자식 컴포넌트간 주고받는다!!</p>
<h2 id="사용방법코드">사용방법(코드)</h2>
<pre><code class="language-js">function ParentComponent(props){
    return &lt;ChildComponent testData = &quot;testing&quot;/&gt;
}</code></pre>
<pre><code class="language-js">function ChildComponent(props){
    const testValue props.testData
}</code></pre>
<hr />
<h1 id="생애주기">생애주기</h1>
<h3 id="리액트-컴포넌트는-생애주기를-가지는데">리액트 컴포넌트는 생애주기를 가지는데...</h3>
<h3 id="마운트-">마운트 :</h3>
<p>컴포넌트가 처음 화면에 렌더링 되었을때!!</p>
<h3 id="업데이트-">업데이트 :</h3>
<p>컴포넌트가 리랜더링 되었을 때!!</p>
<h3 id="언마운트-">언마운트 :</h3>
<p>컴퍼넌트가 화면에서 사라졌을 때!!</p>
<h1 id="useeffect">UseEffect()</h1>
<h3 id="기본형식">기본형식</h3>
<pre><code class="language-js">useEffect(()=&gt;{
    //콜백함수
}, [//의존성 배열])</code></pre>
<h3 id="의존성배열이란">의존성배열이란??</h3>
<p>의존성 배열안의 값이 변화하면 useEffect Hook안에 콜백함수가 작동!!</p>
<h3 id="의존성-배열을-빈배열로-하면">의존성 배열을 빈배열로 하면??</h3>
<p>컴포넌트가 마운트/언마운트 될 때만 콜백함수가 작동!!</p>
<h1 id="useref">UseRef()</h1>
<h3 id="특정-바뀌어도-랜더링이-일어나지-않게-하기위한-hook">특정 바뀌어도 랜더링이 일어나지 않게 하기위한 Hook!!</h3>
<h3 id="사용법">사용법</h3>
<pre><code class="language-js">function testComponent(){
    const count = useRef(0) //초기값 0으로 useRef사용!!

    return(
        &lt;div&gt;{count}&lt;/div&gt;
          &lt;button onClick={()=&gt;{count.current += 1}}&gt;+1&lt;/button&gt;
        //current keyword를 통해 useRef변수에 접근한다.
    )
}
</code></pre>
<hr />
<h3 id="참고강의-">참고강의 :</h3>
<p><a href="https://www.youtube.com/watch?v=G3qglTF-fFI">리액트 Hooks에 취하다</a></p>
<h1 id="1-1-usestate-">1-1 UseState( )</h1>
<p>나머지는 이미 알고있는 내용이라 Pass!!</p>
<p>useState 초기값 설정을 한번만 하고 싶을때</p>
<pre><code class="language-js">const heavyWork = () =&gt;{
  console.log('엄청 무거운 작업')
  return ['홍길동', '김민수']
}
function App() {
  const [names, setNames] = useState(heavyWork())
  const [input, setInput] = useState(&quot;start message&quot;)</code></pre>
<h3 id="아래와-같이-초기함수를-수정해주면-해결할-수-있다">아래와 같이 초기함수를 수정해주면 해결할 수 있다.</h3>
<pre><code class="language-js">const heavyWork = () =&gt;{
  console.log('엄청 무거운 작업')
  return ['홍길동', '김민수']
}
function App() {
  const [names, setNames] = useState(()=&gt;{
    return heavyWork()})</code></pre>
<h1 id="1-2-useeffect">1-2 UseEffect</h1>
<h3 id="clean-up이란">clean Up이란??</h3>
<p>useEffect내의 콜백함수에서 했던 작업을 마무리하여 처리하는 과정
(언마운트가 될때를 말한다)</p>
<pre><code class="language-js">useEffect(()=&gt;{
  //작업실행

    return()=&gt;{
      //작업 종료 처리
    }
},[])
</code></pre>
<h1 id="2-1-useref">2-1 UseRef</h1>
<h2 id="useref의-관리">useRef의 관리</h2>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/f41aa7d3-5427-42d3-9968-2d7442430387/image.png" /></p>
<p>이 처럼 current key의 value값으로 변수의 값이 저장된다.</p>
<h2 id="ref-vs-변수">Ref VS 변수</h2>
<p>예시코드로 보자!!</p>
<pre><code class="language-js">const App = () =&gt;{

  const countRef = useRef(0);
  let countVar = 0;

  //이하 코드....
}</code></pre>
<p>이렇게 랜더링이 되어도 변수는 0으로 초기화 되지만
Ref는 그렇지 않다!!!</p>
<h1 id="2-1-useref-dom-접근">2-1 useRef Dom 접근</h1>
<p>useRef를 통해서 input Dom객체에 접근해서
최초 렌더링시에 focus되게 하기!!</p>
<pre><code class="language-js">import logo from './logo.svg';
import './App.css';
import { useEffect, useRef, useState } from 'react';
import Timer from './Timer';

function App() {
  const inputRef = useRef();

  useEffect(()=&gt;{
    inputRef.current.focus();
    // inputRef에 input태그 Dom객체가 들어간다!! (대박인듯)
  },[])

  const login = () =&gt;{
    alert(`환영합니다' ${inputRef.current.value}!`)
  }
  return (
    &lt;div&gt;
      &lt;input ref={inputRef} type=&quot;text&quot; placeholder='username'&gt;&lt;/input&gt;
      &lt;button onClick={login}&gt;로그인&lt;/button&gt;
    &lt;/div&gt;
  );
}

export default App;
</code></pre>