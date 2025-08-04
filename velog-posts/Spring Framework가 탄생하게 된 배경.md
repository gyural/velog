<h1 id="1-spring-framework-탄생-배경">1. Spring Framework 탄생 배경</h1>
<p>Spring Framwork는 20년 넘게 이어지는 오픈소스 프레임 워크이다.
이녀석의 등장배경에는 <strong>EJB(EnterpriceJavaBeans)</strong> 는 비지니스 로직에만 집중할 수 있게 다른 모듈을 제공해주는 프레림워크이다.
비지니스 로직만 짜면 되지만 이거와 연관된 클래스가 업데이트가 되거나 문제가 생길 때마다 계속 수정해줘야한다...!!</p>
<blockquote>
<p>EJB는 의존도가 높아진다는 단점이 있다.</p>
</blockquote>
<p>이때 POJO라는 말이 등장하면서 EJB를 대체할 수 있는 자바만으로 개발된 프로그램이 책으로 출시된다. 해당 책은 이거다
<img alt="" src="https://velog.velcdn.com/images/gyural/post/f5d50b93-4092-4446-87c4-aff91f0518f7/image.png" />
해당 책에서 현재도 사용되는 스프링 핵심 개념인 <strong>BeanFactory, ApplicationContext, POJO, IoC(제어의 역전) , DI(의존성 주입)</strong>등 이 포함되었다.</p>
<p>여기서 이 책을 핵심아이디어로한 오픈소스 프로젝트를 유겐 휠러(Juergen Hoeller)와 Yann Caroff(얀 카로프)가 출시하게 되는데...</p>
<blockquote>
<p><strong>이것이 스프링 프레임워크 1.0이다...!!</strong></p>
</blockquote>
<hr />
<h1 id="2-프레임-워크와-라이브러리의-차이점">2. 프레임 워크와 라이브러리의 차이점</h1>
<h3 id="2-1-개요">2-1. 개요</h3>
<p>공통점으로는....</p>
<blockquote>
<p>둘다 어느정도 만들어낸 프로그램을 개발자가 프로젝트의 개발시 편의와 생산성을 위해 사용하는 모듈이라고 볼 수 있다.</p>
</blockquote>
<p>차이점으로는 흐름에 주체에서 차이가 난다.</p>
<hr />
<h3 id="2-2-흐름의-주체">2-2. 흐름의 주체</h3>
<p>라이브러리는 개발자가 흐름을 제어하며 <strong>&quot;필요할 때&quot;만 호출 하며 개발</strong>
프레임 워크는 이미 앱의 흐름을 제어하고 있으면서 개발자는 프레임워크에 <strong>&quot;정의되어있는 규칙에 맞춰서&quot; 개발</strong></p>
<p>뭔가 말로만 들으면 그럴싸 해보이며 어느정도 타협을 하면 수긍하기 편한것 같다....
그러나 뭔가 확 와닿지 않은것 같다</p>
<blockquote>
<p>흐름이란 무엇이고, 흐름에 어느정도 관여를 해야지 흐름제어인가???</p>
</blockquote>
<p>사실 흐름이라는 단어 자체가 추장적이다.
내가 오늘 위워크에서 지하철을 타고 걸어서 집에 온것도 흐름이고, 스프링 프레임워크에서 요청이 왔을 때 서블릿 컨테이너에서 해당 스레드에 서블릿을 할당하고 필터를 지나 Dispatcher-Servlet으로 와서 컨트롤러로 오는... 이런것도 흐름이다...!!!</p>
<blockquote>
<ol>
<li>프레인워크와 라이브러리는 이분적으로 나눠지는것이 아니라 어느정도 연속선상에 있다.</li>
</ol>
</blockquote>
<blockquote>
<ol start="2">
<li>연속성상에 있기 때문에 어디부터 정확한 흐름제어이다 라고 구분짓기 어렵다.</li>
</ol>
</blockquote>
<p>위 두가지는 React가 라이브러리이면서, 프레임워크이기도한 특징이기도 합니다.
<strong>React는 전통적으로 라이브러리이지만, React Router같은 복잡한 흐름에 관여하기 시작한다면 프레임워크이다.</strong></p>
<h3 id="2-3-예시">2-3. 예시</h3>
<p>저는 Spring 개발자이니 Spring으로 예시를 들겠습니다...!!</p>
<p>*<em>대표적인 프레임 워크 *</em></p>
<blockquote>
<ul>
<li>Srping Framework</li>
</ul>
</blockquote>
<ul>
<li>Srping Boot</li>
<li>JPA</li>
</ul>
<p><strong>대표적인 라이브러리</strong></p>
<blockquote>
<ul>
<li>JDK Serializer</li>
</ul>
</blockquote>
<ul>
<li>Object Mapper(JSON 변환도구)</li>
<li>Lombok</li>
</ul>
<h1 id="ref">REF</h1>
<p><a href="https://velog.io/@zayson/Spring-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8-1-Spring%EC%9D%98-%EB%93%B1%EC%9E%A5%EB%B0%B0%EA%B2%BD%EA%B3%BC-Spring%EC%9D%B4%EB%9E%80">[Spring 핵심 원리 - 기본편] 1) Spring의 등장배경과 Spring이란?</a>
<a href="https://www.youtube.com/watch?v=GpMDhfvsDcw">프레임워크 란? | 라이브러리 차이 | 프레임워크 공부 방법
</a></p>