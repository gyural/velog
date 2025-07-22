<h1 id="배경">배경</h1>
<p>객체지향 원칙은 외우기가 쉬운거 같아요 앞글자만해서 SOLID로 알고 계실겁니다.
오늘은 여기서 <code>S</code> <code>O</code>가 담당하는 단일 책임 원칙과 개방패쇄 원칙을 알아보겠습니다.</p>
<hr />
<h1 id="srpsingleresposibilityprinciple">SRP(SingleResposibilityPrinciple)</h1>
<p>단일 책임 원칙은 하나의 클래스는 하나의 책임만 가져야 한다는 원칙입니다.
즉, 변경이 필요한 이유는 <strong>오직 하나</strong>여야 합니다.</p>
<h3 id="✅-예시-코드-srp-위반">✅ 예시 코드 (SRP 위반)</h3>
<pre><code class="language-java">public class UserService {
    public void registerUser(String username) {
        // 1. 사용자 등록
        System.out.println(username + &quot; 등록 완료&quot;);

        // 2. 이메일 전송
        System.out.println(&quot;이메일 전송&quot;);
    }
}</code></pre>
<p>UserService는 사용자 등록뿐만 아니라 이메일 전송 책임까지 갖고 있어 SRP를 위반하고 있습니다.</p>
<h3 id="✅-srp-적용-후-코드">✅ SRP 적용 후 코드</h3>
<pre><code class="language-java">public class UserService {
    private final EmailService emailService;

    public UserService(EmailService emailService) {
        this.emailService = emailService;
    }

    public void registerUser(String username) {
        System.out.println(username + &quot; 등록 완료&quot;);
        emailService.sendEmail(username);
    }
}

public class EmailService {
    public void sendEmail(String username) {
        System.out.println(username + &quot;에게 이메일 전송&quot;);
    }
}</code></pre>
<p>각 클래스가 하나의 책임만을 갖게 되면서 코드의 응집도는 높이고, 변경에 유연한 구조가 됩니다.</p>
<hr />
<h1 id="ocpopenclosedprinciple">OCP(OpenClosedPrinciple)</h1>
<p>개방-폐쇄 원칙은 확장에는 열려 있고, 변경에는 닫혀 있어야 한다는 원칙입니다.
즉, 기존 코드를 수정하지 않고도 새로운 기능을 추가할 수 있어야 한다는 의미입니다.</p>
<h3 id="✅-예시-코드-ocp-위반">✅ 예시 코드 (OCP 위반)</h3>
<pre><code class="language-java">public class PaymentService {
    public void pay(String method) {
        if (method.equals(&quot;card&quot;)) {
            System.out.println(&quot;카드 결제&quot;);
        } else if (method.equals(&quot;kakao&quot;)) {
            System.out.println(&quot;카카오페이 결제&quot;);
        }
    }
}</code></pre>
<p>새로운 결제 수단이 추가될 때마다 PaymentService 내부를 계속 수정해야 하므로 OCP 위반입니다.</p>
<h3 id="✅-ocp-적용-후-코드">✅ OCP 적용 후 코드</h3>
<pre><code class="language-java">public interface Payment {
    void pay();
}

public class CardPayment implements Payment {
    public void pay() {
        System.out.println(&quot;카드 결제&quot;);
    }
}

public class KakaoPayment implements Payment {
    public void pay() {
        System.out.println(&quot;카카오페이 결제&quot;);
    }
}

public class PaymentService {
    public void doPayment(Payment payment) {
        payment.pay();
    }
}</code></pre>
<p>PaymentService는 Payment 인터페이스에만 의존하며, 새로운 결제 방식이 생기더라도 기존 코드는 전혀 건드릴 필요가 없습니다.</p>
<hr />
<h1 id="정리">정리</h1>
<table>
<thead>
<tr>
<th>원칙</th>
<th>정의</th>
</tr>
</thead>
<tbody><tr>
<td><code>SRP (단일 책임 원칙)</code></td>
<td>클래스는 하나의 책임만 가져야 함</td>
</tr>
<tr>
<td><code>OCP (개방-폐쇄 원칙)</code></td>
<td>확장에는 열려 있고, 변경에는 닫혀야 함</td>
</tr>
</tbody></table>