<p><img alt="업로드중.." src="blob:https://velog.io/6cc7767e-58ec-4cdd-a9f2-1c310740f464" /></p>
<h1 id="ddl과-dml의-차이">DDL과 DML의 차이</h1>
<h3 id="ddldata-definition-language">DDL(Data Definition Language)</h3>
<p>DDL은 SQL의 하위 집합으로서 DMBS에서 사용되는  테이블, 뷰, 인덱스, 프로지저같은 개체를 정의하는데 사용됩니다.</p>
<h3 id="대표적인-ddl">대표적인 DDL</h3>
<blockquote>
</blockquote>
<p><code>CREATE</code> : 테이블, 뷰, 인덱스같은 개체를 만드는데 사용하는 명령어입니다.
<code>ALTER</code> : 기존 생성했던 개체를 수정하는데 사용됩니다.
<code>DROP</code> : 기존 데이터베이스 개체를 삭제하는데 사용됩니다.
<code>TRUNCATE</code> : 테이블의 모든 데이터(행)을 삭제한다. 스키마와 인덱스는 유지됩니다.
<code>RENAME</code> : 기존 데이터베이스 개체의 이름을 바꾸는데 사용됩니다.</p>
<hr />
<h3 id="dmldata-manipulation-language">DML(Data Manipulation Language)</h3>
<p>DDL은 SQL의 하위 집합으로서 DMBS내의 데이터를 조작하는데 사용됩니다. 주로 삽입, 삭제, 업데이트에 사용됩니다.</p>
<h3 id="대표적인-dml">대표적인 DML</h3>
<blockquote>
</blockquote>
<p><code>SELECT</code> : 하나 이상의 테이블에서 데이터를 검색하는데 사용됩니다.
<code>INSERT</code> : 테이블에 새로운 데이터를 삽입하는데 사용됩니다.
<code>UPDATE</code> : 테이블의 기존 데이터를 수정하는 데 사용됩니다.
<code>DELETE</code> : 테이블에서 데이터를 삭제하는 데 사용됩니다.</p>
<hr />
<h1 id="역-정규화">역 정규화</h1>
<h3 id="정규화">정규화</h3>
<p>RDB에서 정규화란 중복된 데이터를 최소화하기 위해 행이나, 테이블을 쪼개는 프로세스를 말한다.
1 ~ 5 정규화까지 가능하다.</p>
<h3 id="역-정규화-1">역 정규화</h3>
<p>정규화로 인해 테이블이 너무 많아서 데이터 조회시에 <code>JOIN</code>문 이 많이 발생해 DBMS에 전반적인 성능이 떨어질 수가 있다. 이때 정규화 규칙을 위배하는대신 성능을 높여주기 위해 테이블을 다시 합쳐주는 과정을 말한다.</p>
<h3 id="역-정규화가-필요한-상황">역 정규화가 필요한 상황</h3>
<blockquote>
<ul>
<li>JOIN 연산이 과도하게 많아 성능이 저하될 때</li>
</ul>
</blockquote>
<ul>
<li>조회가 빈번한 테이블일 때</li>
<li>실시간 처리 성능이 중요할 때</li>
</ul>
<h3 id="역-정규화의-장단점">역 정규화의 장단점</h3>
<p><strong>장점 :</strong></p>
<blockquote>
</blockquote>
<ul>
<li>읽기 성능 향상 : 빈번하게 조회되는 데이터에 대해 JOIN연산을 줄여줄 수 있다.</li>
<li>복잡한 쿼리 간소화 : 자주 사용되는 복잡한 쿼리를 간소화하여 응답시간을 줄일 수 있다.</li>
<li>속도 우선 시스템 : 데이터의 무결성 보다 조회 성능이 중요한 시스템에서 사용할 수 있다.</li>
</ul>
<p><strong>단점:</strong></p>
<blockquote>
<ul>
<li>데이터 무결성 감소 : 테이블간의 결합이 낮아지기 때문에 데이터 정합성이 떨어질 수 있다.</li>
</ul>
</blockquote>
<h3 id="ref">REF</h3>
<ol>
<li><a href="https://appmaster.io/ko/blog/ddlgwa-dmlyi-caijeom">DDL과 DML의 차이점
</a></li>
<li><a href="https://sh970901.tistory.com/102">데이터베이스 정규화</a></li>
<li><a href="https://mroh1226.tistory.com/222">[MS-SQL] 정규화 역정규화 사이에서 고민</a></li>
</ol>