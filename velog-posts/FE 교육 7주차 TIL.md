<h1 id="span-stylecolor-skyblue협업-하는-법span"><span style="color: skyblue;">협업 하는 법!!!!</span></h1>
<h2 id="span-stylecolor-skyblue1-파이썬-가상환경-생성-및-실행span"><span style="color: skyblue;">1. 파이썬 가상환경 생성 및 실행!!!!</span></h2>
<p>백엔드파트로 부터 장고로 작성한 파이썬 파일들을 전달받으면
먼저 파이썬 가상환경을 생성하고 실행한다.</p>
<h3 id="span-stylecolor-skyblue가상환경을-만드는-법span"><span style="color: skyblue;">가상환경을 만드는 법</span></h3>
<p>먼저 다음과 같은 명령어를 입력한다.</p>
<pre><code>python -m venv 폴더명</code></pre><p>가상환경이 생성된다면 
<img alt="" src="https://velog.velcdn.com/images/gyural/post/27294778-785f-427e-97e0-c899a71dfeab/image.png" />
터미널 상에서 &quot;(test)&quot;가 경로 앞에 생긴 것을 확인할 수 있고 가상환경이 정상적으로 생성된 것이다.</p>
<p>이 때 새로운 가상환경 폴더가 생긴것도 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/6d90dec6-5f8c-4838-a7bc-043ed06c20a8/image.png" /></p>
<h3 id="span-stylecolor-skyblue가상환경에-requirement-받기span"><span style="color: skyblue;">가상환경에 requirement 받기</span></h3>
<p>협업을 하는 과정에서 여러가지 같은 툴을 다루더라도 다른 버전이 있을 수 있고 이러한 여러가지 이슈가 발생할 수 있기 때문에
requirement.txt를 받음으로써 이 문제를 해결해 줄 수 있다.</p>
<pre><code>pip install -r requirements.txt</code></pre><p>위와같은 명령어를 사용해준다면 requirement를 가상환경에 다운받을 수 있다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/be70ba8d-7a84-4e7b-9901-e9b65f98431c/image.png" />
위의 그림을 보면 터미널 상에서 requirement를 받은 것을 확인해 줄 수 있다.</p>
<h2 id="span-stylecolor-skyblue2-폴더-구조-만들기span"><span style="color: skyblue;">2. 폴더 구조 만들기!!!!</span></h2>
<p>위에서 만든 가상환경과 백앤드에서 받은 폴더에 
HTML, CSS, JS, 이미지 파일들을 가져와야 된다.
먼저 <span style="color: skyblue;">static</span>이라는 폴더를 만들어 준 후 다음과 같이 폴도구조를 만들어 준다!!!</p>
<p><img alt="" src="https://velog.velcdn.com/images/gyural/post/9cb3bfd8-9f76-4fe8-bc04-d4c1f5bc6bdf/image.png" /></p>
<h3 id="span-stylecolor-skybluecss파일-이미지-파일-옮기기span"><span style="color: skyblue;">CSS파일 이미지 파일 옮기기</span></h3>
<p>이 부분은 간단하다 CSS파일을 CSS폴더안으로 이미지파일을 이미지 폴더안으로 그대로 파일을 복사한다!!!</p>
<h3 id="span-stylecolor-skybluehtml파일-옮기기span"><span style="color: skyblue;">HTML파일 옮기기</span></h3>
<p>위 파일은 template이라는 파일에 일단 HTML코드를 가져온다.
그리고 HTML 코드에 맨위에 장고 탬플릿 태그인</p>
<pre><code class="language-html">{% load static %}</code></pre>
<p>를 작성해줘서 load static을 설정해 준다.</p>
<hr />
<p>또한 html코드에서 작성되었던 경로들에 대해 수정이 필요하다.</p>
<p>기존의 코드가 아래와 같다면</p>
<pre><code class="language-html">&lt;a href=&quot;#&quot;&gt;&lt;img src=&quot;./images/logo.png&quot; alt=&quot;쿠팡&quot;&gt;&lt;/a&gt;</code></pre>
<p>장고 탬플릿 태그로 경로를 아래와 같이 수정해준다!!!! </p>
<pre><code class="language-html">&lt;a href=&quot;#&quot;&gt;&lt;img src=&quot;{% static 'images/logo.png' %}&quot; alt=&quot;쿠팡&quot;&gt;&lt;/a&gt;</code></pre>
<p>위와같이 수정을 완료하면 HTML파일 옮기기도 완료된다.</p>
<h3 id="span-stylecolor-skybluejs-파일-가져오기span"><span style="color: skyblue;">js 파일 가져오기</span></h3>
<p>js 파일을 가져오는 방식은 어렵지 않다.</p>
<p>방금 막 수정 완료한 HTML파일에 body태그 마지막 줄에 <span style="color: skyblue;">&quot;script&quot;</span> 태그를 작성해주고 그 태그안에 JS코드를 옮겨준다!!!</p>
<p>예시)</p>
<pre><code class="language-html">&lt;body&gt;
  &lt;!-- 여기 까지가 수정 완료된 html코드이다. --&gt;
  &lt;script&gt;
    //작성한 javascript코드가 들어간 공간이다!!!!
  &lt;/script&gt;
&lt;/body&gt;</code></pre>