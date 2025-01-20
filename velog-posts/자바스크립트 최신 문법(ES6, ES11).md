<p>ES6(2015) ~ ES11(2020)까지의 문법에 대해서 소개한다.</p>
<h2 id="span-stylecolor-skyblue1-shorthand-property-namesspan"><span style="color: skyblue;">1. shorthand property names</span></h2>
<p>객체의 KEY값과 VAlUE값을 설정할 때 다음과 같이 축약해서 지정해줄 수 있다!!!
아래 3가지 방법으로 저장한 객체는 같은 key value값을 가진다!!!</p>
<pre><code class="language-js">/*Shorthand property names
아래3개의 객체는 모두 같은 key값과 value값을 가지게된다.*/

const ellie1 ={
  name: 'Ellie',
  age: '18',
};

const name = 'Ellie';
const age = '18';
const ellie2 ={
  name: name,
  age: age,
};

const ellie3 ={
  name,
  age,
};

console.log(ellie3, ellie2, ellie1)</code></pre>
<hr />
<h2 id="span-stylecolor-skyblue2-destructuring-assignmentspan"><span style="color: skyblue;">2. Destructuring Assignment</span></h2>
<p>배열이나 객체처럼 여려개의 정보를 변수에 담고 싶을 때 위 문법을 통해 축약해서 저장할수 있다.</p>
<pre><code class="language-js">const student = {
    name: 'Anna',
    level: 1,
};

{
const name = student.name;
const level = student.level;
}
const {name, level} = student
//위와 같이 객체에 객체의 정보를 담아줄 때 위와 같이 축약해줄 수 있다.

const {name: studnetName, level: studentLevel} = student
//위와 같이 객체의 KEY값의 이름도 바꿔줄 수 있다.


//배열에 대해서도 적용이 가능한데

const aniamls = ['puppy', 'kitty']

const first = animals[0];
const second = animals[1];
//배열에 대한 저장을 아래와 같이 축약해서 사용해줄 수 있다.
const [first, second] = animals;</code></pre>
<hr />
<h2 id="span-stylecolor-skyblue3-spread-syntax활용도가-높다span"><span style="color: skyblue;">3. Spread Syntax(활용도가 높다)</span></h2>
<p>'. . .' 연산자를 사용 해주는데 이 연산자를 사용해주면 짧은 코드로 각 배열이나 객체의 정보를 하나하나 저장해줄 수 있다.
사용 예시는 아래의 코드에 있다!!!</p>
<pre><code class="language-js">const obj1 = {key: 'key1'};
const obj2 = {key: 'key2'};
const array = [obj1, obj2];

//array copy
const arrayCopy = [...array];
//객체 배열을 복사할 때 '...'을 사용해주는데 이는 각각 낱개로 복사한다는 연산자이다.
//이때 주소값을 복사해오기 때문에 동일한 obj를 가리키고 있다.
//따라서 obj1을 변경한다면 3개의 배열내에서 변경이 된다.

//복사후 추가 하고자 할때는
const arrayCopy2 = [...array, {key: 'key3'}];

//obj에 대해서도 복사가 가능하다
const obj3 = {...obj1};

//array concatenation (병합으로도 응용 가능)
const fruiets1 = ['orange', 'strawberry']
const fruiets2 = ['banana', 'kiwiee']
const fruiets = [...fruits1, ...fruits2]

//object merge
const dog1 = {dog: 'dog1'}
const dog2 = {dog: 'dog2'}
const dog = {...dog1, ...dog2};
//여기서 배열과 다른점은 같은 키값이 병합될 때에는 뒤에있는 객체의 value값으로
//저장된다.</code></pre>
<hr />
<h2 id="span-stylecolor-skyblue4-default-parametersspan"><span style="color: skyblue;">4. Default parameters</span></h2>
<p>함수에서 매개변수를 지정하지 않았을 때 디폴트 매개변수로 함수를 작동하게 해주는 문법이다!!!</p>
<pre><code class="language-js">
function printMessage(message = 'default message'){
    console.log(message);
}

printMessage()
//다음과같이 매개변수 부분에 아무것도 없을 때 디폴트 매개변수 값에 대해 함수가 작동한다.</code></pre>
<h2 id="span-stylecolor-skyblue5-ternary-operatorspan"><span style="color: skyblue;">5. Ternary Operator</span></h2>
<p>if문으로 작성한 복잡한 코드를 한줄로 간단히 해줄 수 있는 문법이다</p>
<pre><code class="language-js">const isCat = true;

{
    let component;
    if(isCat){
        component = 'cat'
    }
    else{
        componet = 'dog'
    }
    console.log(component);
}
//위와 같은 if문으로 작성된 코드를 아래와 같이 간단히 작성할 수 있다.
const component = isCat ? 'cat' : 'dog';</code></pre>
<h2 id="span-stylecolor-skyblue6-template-literalsspan"><span style="color: skyblue;">6. Template Literals</span></h2>
<p>변수와 String을 같이 출력하고 싶을때 하나의 백틱(따옴표 아님!!!)안에 작성해주면 간단히 작성해줄 수 있다.</p>
<pre><code class="language-js">const weather = 'hot'
const temperature = '16c'

console.log(`Today weather is ${weather} and temparatuer is ${temperature}`);
//이런식으로 변수값들을 백틱 ' `${변수명}` ' 넣어준다면 깔끔하게 문자열을 작성할 수 있다!!! </code></pre>
<h2 id="span-stylecolor-skyblue7-optional-chainingspan"><span style="color: skyblue;">7. Optional Chaining</span></h2>
<p>객체안에 객체가 있을때 그 객체안의 key값이 존재하는지에 따라 동작이 달라지는 코드를 작성하고 싶을 때
위 문법을 사용해줄 수 있다.</p>
<pre><code class="language-js">function printManager(person){
    console.log(person.job &amp;&amp; person.manager &amp;&amp; person.job.manager.name)
}

//아래와 같이 수정
function printManager(person){
    console.log(person.job?.manager?.name);
    //person에 job에 manager가 있으면 name을 출력한다.
}</code></pre>
<hr />
<h2 id="span-stylecolor-skyblue8-nullish-coalescing-operatorspan"><span style="color: skyblue;">8. Nullish Coalescing Operator</span></h2>
<p>'or' 논리 연산자는 null, '', undefined에 대해서 false로 간주하는데
이를 이용해 다음과 같은 코드를 작성해 줄 수 있다.</p>
<pre><code class="language-js">const userName = name || 'Guest';
// userName이 없다면 'Guest'로 이름을 저장한다.</code></pre>
<p>위 코드에서 발생할 수 있는 문제는 유저가 이름을 작성을 안하거나 숫자0으로 할당되어 있는 경우에도 'Guest'로 이름이 저장된다는 문제가 발생하는데 
위 문법으로 이를 해결해 줄 수 있다 사용예시는 아래와 같다!!!</p>
<pre><code class="language-js">const message = num ?? 'undefined';
//위와 같이 작성한다면 이 문제를 해결해 줄 수 있다.</code></pre>