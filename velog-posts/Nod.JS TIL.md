<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/e8ae0829-374c-4e7e-9064-aae30b762f27/image.png" /></p>
<hr />
<h1 id="chap-0-1">chap 0-1</h1>
<p><span><h3 style="color: green;">Node JS란???</h1></span></p>
<h3 id="client"><code>Client</code></h3>
<p>정보를 요청하는 컴퓨터</p>
<h3 id="server"><code>Server</code></h3>
<p>정보를 처리하는 컴퓨터</p>
<h3 id="서버를-만든다는-것은">서버를 만든다는 것은???</h3>
<p>서버 컴퓨터에서 필요한 프로그램을 만드는 것을 의미한다.</p>
<p><strong>Node.js</strong>
구글 크롬 v8엔진으로 빌드된 JS런타임이며, JS를 브라우저 외부에서 실행할 수 있는 환경</p>
<h3 id="js파일-실행하기">js파일 실행하기</h3>
<p>1) 터미널 창을 연다</p>
<p>2) 해당 경로로 디렉토리를 이동해준다.</p>
<p>3) 실행 명령어를 입력해준다</p>
<pre><code class="language-bash">node &lt;fileName&gt;.js</code></pre>
<h3 id="npm으로-패키지-관리하기">npm으로 패키지 관리하기</h3>
<p><strong>모듈 :</strong></p>
<p>기능별로 쪼개놓은 파일</p>
<p><strong>라이브러리 :</strong></p>
<p>모듈보다 큰 단위 여러개의 모듈 묶음</p>
<p><strong>패키지 :</strong></p>
<p>배포할 수 있도록 여러 모듈과 관련파일을 묶어 놓은것</p>
<p><strong><code>npm</code></strong>(Node Package Manger)</p>
<p>1) NPM 초기화 하기</p>
<p>npm을 사용하지 않았으니 초기화 해준다.</p>
<pre><code class="language-bash">cd basic
npm init
--&gt; 이후 계속 엔터(Enter)눌러주기 
</code></pre>
<p><strong>2) pacage.json</strong></p>
<p>—&gt; 해당 파일에 패키지 정보들이 담긴다.</p>
<p><strong>3) 패키지 설치 해보기</strong></p>
<p><a href="http://npmjs.org">npmjs.org</a> ⇒ 해당사이트에서 사용하기</p>
<p>패키지 설지</p>
<pre><code class="language-bash">npm i ansi-colors</code></pre>
<p><strong>4) ansi-colors 사용하기</strong></p>
<p>패키지 가져온 후 사용</p>
<pre><code class="language-jsx">const c = require('ansi-colors')
console.log(c.green(&quot;HaHa&quot;))</code></pre>
<p><strong>5) package 삭제하기</strong></p>
<pre><code class="language-jsx">npm uninstall ansi-colors</code></pre>
<hr />
<h3 id="템플릿-리터럴">템플릿 리터럴</h3>
<pre><code class="language-jsx">//연결 연산자 사용
console.log(num1 + &quot;과&quot; + num2 + &quot;를 더하면&quot; + (num1 + num2))
//템플릿 리터럴 사용 - 백틱(`)
console.log(`${num1}과${num2}를 더하면 ${num1+num2}입니다.`)</code></pre>
<h3 id="함수">함수</h3>
<pre><code class="language-jsx">//함수 선언하고 호출하기-1
function greeting(name){
    console.log(`${name}님 안녕하세요`)
}
//함수 호출
greeting(&quot;홍길동&quot;)

//함수 선언하고 호출하기-2
let greeting = function(name){
    console.log(`${name}님 안녕하세요?`)
}
//함수 호출하기
greeting('홍길동')

//함수 선언하고 호출하기-3
//선언과 동시에 실행
(function(a, b){
    console.log('두 수의 합: ${a + b}');
}(100,200))

//함수 선언하고 호출하기-4
//화살표 함수
let h1 = () =&gt; '안녕하세요'</code></pre>
<h3 id="비동기-처리">비동기 처리</h3>
<p>동기(Sync) 처리 </p>
<pre><code class="language-jsx">function displayA(){
    consolelog('A');
}
function displayB(){
    setTimeout(()=&gt;{
        consolelog('B');
    },2000)
    //2초 후에 B를 나타내기
}
function displayC(){
    consolelog('C');
}

displayA();
displayB();
displayC();</code></pre>
<p><strong>출력 :</strong>
A
C
B
—&gt; 빨리할 수 있는 작업부터 처리한다.</p>
<p><strong>비동기(Async) 처리</strong> </p>
<p>—&gt; A B C순으로 실행하기</p>
<p><strong>코드</strong></p>
<pre><code class="language-jsx">//콜백함수를 이용
function displayA(){
    consolelog('A');
}
function displayB(callback){
    setTimeout(()=&gt;{
        consolelog('B');
        callback
    }, 2000)
    //2초 후에 B를 나타내기
}
function displayC(){
    consolelog('C');
}

displayA();
displayB(displayC());</code></pre>
<p>—&gt; 원하는 순서에 맞게 처리할 수 있다.</p>
<h3 id="js에서-비동기를-처리하는-방법">JS에서 비동기를 처리하는 방법</h3>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/ac331b2e-186c-4b73-b79b-05741c86a91f/image.png" />
<strong>프로미스 :</strong>
성공했을 때 실행했을 때 각각의 로직을 처리한다.</p>
<hr />
<h3 id="nodejs-모듈">Node.js 모듈</h3>
<p>독립된 모듈로 프로그램을 구성한다….!!!</p>
<p><strong>1) 코드 중복 방지</strong></p>
<p><strong>2) 수정시 필요한 모듈만 수정</strong></p>
<h3 id="commonjs-모듈-시스템-vs-es-모듈-시스템">CommonJS 모듈 시스템 VS ES 모듈 시스템</h3>
<p><strong><code>CommonJS</code></strong>
require, module.exports 사용</p>
<p><strong><code>ES 모듈</code></strong>
import, export 사용</p>
<h3 id="모듈-만들기">모듈 만들기</h3>
<p><strong>user.js</strong></p>
<pre><code class="language-jsx">const user = &quot;홍길동&quot;
const user2 = &quot;Lee&quot;
const user3 = &quot;choi&quot;
module.exports = user;

module.exports = {user1, user2}
//원하는 모듈만 내보내줄 수 있다.</code></pre>
<p><strong>hello.js</strong></p>
<pre><code class="language-jsx">const hello = (name)=&gt;{
    console.log(`${name}님 안녕하세요`)
}
module.exports = hello</code></pre>
<p><strong><code>module.export</code></strong>를 사용하여 모듈을 내보내기 했다!!</p>
<p><strong>app-1.js</strong></p>
<pre><code class="language-jsx">const user = require(&quot;./user&quot;);
const hello = require(&quot;./hello&quot;);
// require문과 상대경로를 이용한다.

hello(user);

const {user1, user2} = require(&quot;./users&quot;)
// 여러개의 변수를 가져올 수 있다.</code></pre>
<p>변수 <strong><code>user</code></strong>와 함수 <strong><code>hello</code></strong>를 모듈화 했다!!!</p>
<h3 id="노드-모듈들">노드 모듈들</h3>
<p><strong>코어모듈 :</strong>
Node.js에서 포함되어있는 모듈 들이다
ex) http, fs, os 등…
—&gt; 공식문서에서 사용법들이 모두 나와있다!!</p>
<p><strong>글로벌 모듈 :</strong>
require없이 사용할 수 있는 모듈
ex: console.log, __dirname, __filename 등…</p>
<hr />
<h2 id="node-path-모듈">Node path 모듈</h2>
<h3 id="path모듈-필요성">path모듈 필요성</h3>
<p>환경 마다 다른 경로 구분자를 통일한다.</p>
<p>윈도우 - <strong>역슬래시()</strong> 사용
ex) C:\users\funco\Desktop
맥 - <strong>슬래시(/)</strong>사용
 ex) /Users/funnycom/Desktop</p>
<h3 id="절대경로">절대경로</h3>
<p>파일 폴더를 나타낼때 root폴더부터의 모든 경로를 나타낸것</p>
<h3 id="상대경로">상대경로</h3>
<p>특정(주로 현재 파일)기준으로해서 경로를 나타낸 것</p>
<p><strong>path.js</strong></p>
<pre><code class="language-jsx">//path모듈 연습하기
const path = require(&quot;path&quot;)

//join path들을 묶어주는 함수
const fullPath = path.join(&quot;some&quot;, 'work', 'ex.txt');
console.log(fullpath);
//출력 : some\work\ex.txt

//경로만 추출 = dirname
const dir = path.dirname(fullPath);
console.log(dir);
//출력 : some\work\

//파일 이름만 추출 - basename
const fn1 = path.basename(__filename);
console.log(dir);
//출력 : path.js

const fn2 = path.basename(__filename, '.js');
console.log(fn2)
//확장자를 뺀 파일이름 출</code></pre>
<hr />
<h2 id="fsfile-system모듈">fs(File System)모듈</h2>
<p><strong>list-2.js</strong></p>
<pre><code class="language-jsx">//fs모듈 readdir 함수 활용하기

const fs = require(&quot;fs&quot;);
fs.readdir(&quot;./&quot;, (err, files)-&gt;{
    if(err){
        console.log(err);
    }
    console.log(files);
});

//fs모듈 readFile 함수 활용하기
const fs = require(&quot;fs&quot;);
//utf8로 인코딩을 해준다!!!
fs.readFile(&quot;./example.txt&quot;, &quot;utf8&quot;, (err, data)=&gt;{
    if(err){
        console.log(err)
    }
    //console.log(data)
    fs.writeFile(&quot;./test.txt&quot;, data, (err)=&gt;{
         if(err){
             console.log(err);
         }
         console.log(&quot;test.txt is saved&quot;);
    })
})
</code></pre>
<p>JS에는 버퍼라는 메모리의 공간이 있는데 이때 <strong>utf8로 인코딩</strong>을 안한 후 출력을 하게된다면 이진값만이 출력된다.</p>
<hr />
<h2 id="http-모듈">HTTP 모듈</h2>
<p><strong>클라이언트와 서버</strong>
서버는 클라이언트가 보낸 <strong>요청</strong>을 처리하고 <strong>응답</strong>을 해주는 역할을 한다.</p>
<p><strong>IP주소</strong>
컴퓨터 마다의 주소이며 응답이나 요청을 보낼때에 보낼 수 있다.
IP주소뒤에는 포트번호가 붙는다.</p>
<p><strong>포트</strong>
서버에 있는 여러가지 프로그램중 어떤 프로그램인지를 지정하는 번호이다.
IP주소 뒤에 : 기호와 함께 적는다.</p>
<p>예) <strong><a href="http://172.217.25.174:80">http://172.217.25.174:80</a></strong>
<img alt="" src="https://velog.velcdn.com/images/gyural/post/fd7e9f9e-3512-486f-ac20-30436e70c579/image.png" /></p>
<p><strong>http.js</strong></p>
<pre><code class="language-jsx">// HTTP 모듈로 서버 만들고 실행하기

const http = require(&quot;http&quot;)

//res 응답객체를 통해 text 타입 응답
const server = http.createServer((req, res)=&gt;{
  res.setHeader(&quot;Content-type&quot;, &quot;text/plain&quot;);
  res.write(&quot;hello Node&quot;, );
  res.end();
})

// 3000번 포트에 대해서 listen
server.listen(3000, ()=&gt;{
  console.log(&quot;서버가 실행 중&quot;)
})</code></pre>
<hr />
<h3 id="라우팅">라우팅</h3>
<p>클라이언트의 요청에 따라 그에맞는 로직을 실행하는 
예시) <code>nodejs.org/en/about</code>  <code>nodejs.org/en/download</code> </p>
<p>두 개의 다른 URL 요청시에 다른 값을 응답한다.
메서드에 따라서도 다른 값을 응답 가능
<strong>메서드 예)</strong> GET, PUT, POST, DELETE</p>
<p><strong>routing.js</strong></p>
<pre><code class="language-jsx">// HTTP 모듈로 서버 만들고 실행하기

const http = require(&quot;http&quot;)

// 응답객체 res를 통해 text 타입 응답
const server = http.createServer((req, res)=&gt;{
  // req.url: 요청경로
  // req.method : 요청방식

  const {url, method} = req;

  if (method===&quot;GET&quot; &amp;&amp; url===&quot;/home&quot;){
    res.write(&quot;HOME&quot;)
    res.end();
  }
  else if(method===&quot;GET&quot; &amp;&amp; url===&quot;/about&quot;){
    res.write(&quot;about&quot;)
    res.end()
  }
  else{
    res.end(&quot;Not found&quot;)
  }
})

// 3000번 포트에 대해서 listen
server.listen(3000, ()=&gt;{
  console.log(&quot;서버가 실행 중&quot;)
})</code></pre>
<p><code>/about</code> 와 <code>/home</code> 서로다른 url에 대한 다른 응답을 보냄!!</p>