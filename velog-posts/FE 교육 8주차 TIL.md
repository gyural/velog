<h1 id="json-데이터-가져오기">JSON 데이터 가져오기</h1>
<h2 id="span-stylecolor-skyblue1-가상환경-구축및-기본세팅span"><span style="color: skyblue;">1. 가상환경 구축및 기본세팅</span></h2>
<p><span style="color: skyblue;">step1. 가상환경 폴더 만들어주기</span></p>
<pre><code>python -m venv venv_mapping_django</code></pre><p><img alt="" src="https://velog.velcdn.com/images/gyural/post/509a5205-e7a5-4600-b8ce-3243f57107c2/image.png" /></p>
<p>위의 명령어를 실행해주면 위와 같이 터미널 앞에 (폴더명)이 생기고 이는 가상환경이 정상적으로 만들어진 것이다.</p>
<p><span style="color: skyblue;">step2. django 파이썬 파일 설치하기</span></p>
<pre><code>pip install django</code></pre><p>위의 명령어를 입력해 주면 된다.
<span style="color: skyblue;">step3. 프로젝트 폴더 생성</span></p>
<pre><code>django-admin startproject mapping_django_proj</code></pre><p>위의 명령어를 실행하면 아래와 같이 프로젝트 폴더가 생성된다.
<img alt="" src="https://velog.velcdn.com/images/gyural/post/d48e30ac-8f14-416b-8935-0b7c7bc5292a/image.png" /></p>
<p><span style="color: skyblue;">step4.  VS코드에서 방금 만든 프로젝트 폴더 열어주기</span>
vs코드내에서 폴더를 열어주면 된다.</p>
<p><span style="color: skyblue;">step5. mapping_django_app폴더를 만든다.</span></p>
<p>아래의 명령어를 터미널에 입력하면 폴더를 생성할 수 있다.</p>
<pre><code>python manage.py startapp mapping_django_app</code></pre><p><span style="color: skyblue;">step6. app폴더에 파일구조 만들기
</span></p>
<p>static&gt;images, css, js 폴더 3개 생성
이때 각 폴더에 미리 짜준 이미지 css파일을 넣어준다.
templates&gt;mapping_django_app 폴더 생성
이 폴더안에 미리 짜준 html파일을 넣어준다.</p>
<h2 id="span-stylecolor-skyblue2-json데이터-만들어주고-가져오기"><span style="color: skyblue;">2. JSON데이터 만들어주고 가져오기</h2>
</span>
step1. app파일에 있는 view.py폴더에서 json 데이터를 만들어 줄 수 있다.

<pre><code class="language-py">json_data = {
        &quot;Category&quot;: &quot;Fisrt&quot;,
        &quot;Writer&quot;: &quot;수니니&quot;,
        &quot;Place&quot;: &quot;홍카페&quot;,
        &quot;Coord&quot;: &quot;36.6138938, 127.2896801&quot;,
        &quot;Tag&quot;: &quot;냠냠굿, 치즈케이크와 와플, 봄날에 카페&quot;,
        &quot;Q1&quot;: &quot;홍익대학교를 다니면서 과제할 카페를 찾으면서 알게 됐어요&quot;,
        &quot;Q2&quot;: &quot;봄에 남자친구랑 함께요.&quot;,
        &quot;Q3&quot;: &quot;거기 치즈케이크랑 와플을 먹고싶어용&quot;,
        &quot;Q4&quot;: &quot;치즈케이크랑 와플 먹기요&quot;
    }</code></pre>
<p>위와같이 객체 형식과 비슷하게 key : value형식의 데이터이고
다른점으로는 따옴표(&quot;  &quot;)로 묶어준다는 점이 있다.</p>
<p>step2. url.py폴더에서 경로 지정하기</p>
<pre><code class="language-py">from django.contrib import admin
from django.urls import path
from mapping_django_app import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.index, name = 'index'),
]</code></pre>
<p>step3. JavaScript에서 json데이터 가져오기</p>
<pre><code class="language-html">&lt;script&gt;
        let data = '{{ json_data }}';
        console.log(data);
        document.write(data);
&lt;/script&gt;</code></pre>
<p>변수 data에 json데이터를 가져올 수 있다.</p>