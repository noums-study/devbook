이번 4장부터 8장까지의 내용들은 어려운 개념이나 처음 보는 용어가 적었다.</br>
그래서 그런지 더 수월하게 익을 수 있었으며 코드로 예시를 잘 해놔서 더 이론을 이애하기 쉬웠다.</br>
또한 객제와 자료구조의 차이 및 캡슐화의 중요성을 더 느낄 수 이었다.
</br></br>

## 04. 주석
코드만으로 의미를 전달할 능력이 있다면 주석이 필요하지 않을 것이라는게 확실히 와닿았다.</br>
내가 지금까지 달았던 주석은 뭔가 이 객체, 변수, 메소드를 호출하는 사람이 이해를 잘 못할 것이라고 생각해서 달았던 주석이었기 때문이다.</br>
또한 이 책의 저자의 말대로 주석까지 신경써서 유지보수를 해본 적은 없었다. 유지보수할 때 코드만 신경쓰지 주석은 신경쓰지 않았다.</br>
반성하자 나 자신...
</br></br>
* 주석은 나쁜 코드를 보완하지 못한다.
* 코드로 의도를 표현하라!
* 좋은 주석
  * 법적인 주석</br>
    외부 라이브러리를 가져와서 써봤으면 많이 봤을 주석이다.(특히 프론트 개발 때)</br>
    소스파일 첫 머리에 저작권 및 소유권에 정보를 표시하는 주석이다.
  * 정보를 제공하는 주석</br>
    예를 들어 한 번에 이해하기 힘든 정규표현식에 대해 뭘 의하는지 정보를 제공하는 주석
  * 의도를 설명하는 주석</br>
    왜 굳이 이렇게 해야했는지 설명하는 주석
  * 의미를 명로하게 밝히려는 주석</br>
    외부에서 가져온 라이브러리와 같이 변경하지 못하는 코드, 즉 리팩토링을 못하는 코드의 경우는 설명을 달 수 밖에 없다.</br>
    단, 해당 주석이 올바른지 검증하지는 힘들다.
  * 결과를 경고하는 주석</br>
    예를 들어 특정 조건에서는 사용하면 안 되는 함수에 대해 경고 설명 주석</br>
    테스트에서는 @Ignore("경고 메시지") 어노테이션으로 해당 로직이 돌아가는 것을 방지할 수 있다.</br>
  * TODO 주석
  * 중요성을 강조하는 주석
  * 공개 API에서 Javadocs 주석
* 나쁜 주석
  * 주절거리는 주석</br>
    주절거려서 더 이해하기 어렵게 만드는 주석
  * 같은 이야기를 중복하는 주석</br>
    이미 코드로 다 설명이 되는데 굳이 달아놓은 주석
  * 오해할 여지가 있는 주석
  * 의무적으로 그냥 다는 주석
  * 이력을 기록하는 주석</br>
    현재는 소스코드 관리 시스템(git과 같은)에서 이력을 다 알 수 있다.
  * 있으나 마나 한 주석</br>
    주석을 건너뛰는 습관을 만들거 정작 중요한 주석을 안 읽게 할 수 있다.
  * 위치를 표시하는 주석
  * 닫는 괄호에 다는 주석</br>
    while/for 등과 같이 괄호 끝에 해당 괄호 끝이다라고 표시하는 주석을 달바에는 그걸 헷갈리지 않게 함수화 시킬 수 있는 방법이 없는지 고민하자.
  * 공로를 돌리거나 저자를 표시하는 주석
  * 주석으로 처리한 코드</br>
    과거 코드는 소스관리 시스템에서 다 볼 수 있다.
  * HTML 주석
  * 전역 정보</br>
    코드 일부에 주석을 달면서 시스템의 전반적인 정보(너무 광범위한 정보)를 주석으로 기술하지 말자.
  * 너무 많은 정보
  * 모호한 관계</br>
    설명하려고 작성해놓은 주석을 다시 또 해독해야하는 번거로움을 만들지 말자
  * 함수 헤더
  * 비공개 코드에서 JavaDocs 주석

</br></br>

## 05. 형식 맞추기
코드 형식을 맞추기 위한 규칙을 정하고 해당 규칙을 따르자.</br>
필요하면 규칙을 자동으로 적용시키는 도구도 사용하자.</br>
확실히 맞는 얘기였다. 현재 아직 재직 중인 회사에서 우리팀끼리 개발 프로젝트를 하면 개발자가 다 사원이어서 이런 부분이 처음에는 잘 안 잡혔었다.</br>
그러다보니 다른 사람이 작성해놓은 로직을 수정하거나 이해하는데 시간이 더 많이 걸렸고, 결국 점점 프로젝트를 시작하기 전에 패키지 구조, 명명 등에 대해 규칙을 정하고 개발을 시작했다.</br>
그러니 서서히 다른 사람이 작성한 코드를 이해하기 수월해졌다.
</br></br>
* 형식을 맞추는 목적</br>
  말 그대로 코드의 가족성을 높이기 위한 것이다.</br>
  코드의 가독성이 좋지 않으면 유지보수 용이성과 확장성에 안 좋은 영향을 미친다.
* 적절한 행 길이를 유지하라</br>
  다른 시스템을 봐도 200~500 줄의 파일로도 커다란 시스템을 구축할 수 있다.</br>
  너무 큰 파일의 경우 읽기도 싫고 이해하기도 어려우니 지양하자.
  * 산문 기사처럼 작성하라</br>
    신문 기사처럼 위에서 아래로 내려갈 수록 세세하게 묘사하고 나누자.
  * 개념은 빈 행으로 분리하다</br>
  모든 코드를 한 덩어리로 묶으면 읽기 힘들다. 그리고 읽기도 싫다.
  * 세로 밀집도</br>
    그렇다고 빈 행을 남발하지 말자.
  * 수직 거리</br>
    서로 연관성이 높은 개념은 세로로 가까이 둬야 한다.</br>
    코드 해석을 위해 다른 파일을 뒤지거나 상속을 거슬러 올라가야 한다면 수직 거리 규칙을 지키지 않은 것이다.</br>

    **연관성: 한 개념을 이해하는데 다른 개념이 중요한 정도**
    </br>
    protected 변수는 동일한 패키지 내에 있으면서 굳이 분리한 것이므로 변수 선언은 사용하는 위치에 가깝게 해야한다라는 규칙을 위배한다.<br>
    즉, protected 변수 사용은 지양하자.</br>
    인스턴스 변수는 보통 클래스 맨 처음에 선언한다. 왜냐하면 클래스 내의 메서드가 인스턴스 변수를 사용하기 때문이다.</br>
    종속 함수의 경우 호출하는 추상화 수준이 좀 더 높은 함수보다 호출되는 추상화 수준이 낮은 함수를 뒤에 배치한다.</br>
    개념적 유사성(종속성, 변수-함수, 비슷한 동작 수행)은 가깝게 배치한다.
  * 세로 순서</br>
    수직 서리에서 설명했듯이 호출되는 함수를 뒤에 가깝게 배치한다.
* 가로 형식 맞추기</br>
  120자 정도의 짧은 행이 좋다.
  * 가로 공백과 밀집도</br>
    <pre><code>a = b</code></pre>
    => 연산자를 강조해서 연산자 앞 뒤에 띄어쓰기를 했다.</br>
    함수와 인수를 넣는 괄호 사이에 띄어쓰기는 밀접하므로 하지 않아야 한다.</br>
    <pre><code>a-b / (c*d)</code></pre>
    우선순위가 높은 연산자 앞뒤로는 띄어쓰기를 하지 않는다.</br>
  * 가로 정렬</br>
    연산자 앞 뒤 등에 tab을 불필요하게 써서 정렬하지 말자
  * 들여쓰기</br>
    아무리 짧은 코드여도 행 바꾸기, 들여쓰기를 지나치지 말자.</br>
    **가짜범위**: 빈 while/for 문 뒤에 세미콜론을 분이는 행위</br>
    => 이러한 것은 개발자가 밑의 코드가 반복 될 거라는 착각을 심어줄 수 있으므로 빈 괄호를 붙여주거나 새로운 행에 세미콜론을 붙여준다.
* 팀 규칙

</br></br>

## 06. 객체와 자료 구조
지금까지 객체와 자료를 따로 분리해서 생각해보지 않았는데 이 장을 읽어보니 확실히 객체와 자료 구조의 차이점이 와닿는다.</br>
그리고 내가 객제와 자료를 혼합해서 사용하고 있다는 것을 깨닿는다.
</br></br>

* 자료 추상화</br>
  구현을 감추려면 추상화가 필요하다. (setter 사용 X)
* 자료/객체 비대칭</br>
  * 객제의 경우 로직을 변경하지 않으면서 새로운 객체 타입을 추가하기 쉽다. (interface)</br>
  * 자료의 경우 새 로직을 추가하기는 쉬우나 새로운 자료를 추가하기는 쉽지 않다. (새로운 자료에 따른 로직 변경이 연쇄적으로 일어나므로)</br>
* 데이터 법칙</br>
  자신이 조작하는 객체의 속사정을 몰라야하며 반환 받은 객체에서 메서드를 호출하는 것은 안 좋은 것이다.(기차 충돌)</br>
  객체 구조와 자료 구조를 혼합해서 사용하지 말자.
* 자료 전달 객체(DTO)</br>
  private 변수에 getter/setter 함수로 조작하는 빈 형태는 좋지 않다.</br>
  save, find과 같이 탐색하는 함수를 제공하는 활성레코드는 그나마 나으나 비즈니스 규칙을 담을 것이면 내부 자료를 숨기는 객체로 생성하는 것이 바람직하다.

 </br></br>
 
## 07. 오류 처리
오류 처리의 경우는 아직도 어떻게 하는 것이 제일 베스트일지는 그렇게 크게 와닿지는 않는다.</br>
하지만 throws를 어디에 써야하고 어디에 쓰지 말아야하는지는 설명할 수 있을 것 같다.

</br></br>

* 오류코드보다 에외를 사용하라</br>
  즉, 오류를 처리하는 코드는 별도로 분리해서 throws를 받아서 처리하자.
* Try-Catch-Finally 문부터 작성하라</br>
  먼저 강제로 로직에서 어떤 예외가 발생하는지 예외를 발생시켜 테스트해보고 try 블록에서 잡을 Exception 범위(트랜잭션 범위)를 정하자.
* 미확인 예외를 사용하라</br>
  메서드를 선언할 때 throws로 메서드가 반환할 모든 예외를 일일이 열거하는 것은 OCP(Open Closed Principle)를 위반한다.
* 예외에 의미를 제공하라</br>
  오류 메시지에 연산 이름, 실패 유형 등 예외 정보를 넣어 예외와 함꼐 던져라.
* 호출자를 고려해 예외 클래스를 정의하라</br>
  외부 라이브러리를 사용할 때 에러 종류가 다양하면 그것을 별도 객체로 한 번 더 감싸서 예외를 효과적으로 처리하도록 추상화해 의존성을 줄이다.
* 정상 프름을 정의하라</br>
  **특수 사례 패턴**: 클래스를 만들거나 객체를 조작해 특수 상황(예외)를 처리하는 방식으로 default value를 설정해주는 것을 생각하면 된다.
* null을 반환하지 말아라</br>
  호출자가 일일이 null인지 체크를 해야한다. 따라서 null 반환보다는 예외를 던지거나 특수 사례 객체를 반환하라.
* null을 전달하지 마라</br>
  null을 반환하지 말라는 것과 같은 맥락이다. 함수 인수로 null을 전달하지 말아라.</br>
  만일 null 인수를 넣을 수도 있는 함수의 경우 함수 안에서 if 또는 assert 메서드로 null 체크를 해라.</br>
  <pre><code>assert x != null : "x가 null이다!!!!!";</code></pre>

</br></br>

## 08. 경계
처음에는 Map 예제로 Generics를 사용하는 것 또한 베스트가 아니라는 부분을 읽었을 때 왜 안 좋지? 타입을 명시해주니까 그렇게 나쁘지는 않지 않은가?라는 생각이 들었다.</br>
하지만 Generics 사용이 아닌 객체로 추상화를 시키는 것을 읽고 나서 납득이 갔다.</br>
그래도 역시 상황에 따라서 객체로 추상화 하지 않아도 될 때는 있을 것 같다. 그럴 때는 잘 판단해서 Generics를 사용하자.

</br></br>

* 외부 코드 사용하기</br>
  <pre><code>Map<String, User></code></pre>
  => 위와 같이 제너릭스(Generics)를 사용할 경우 타입 명시는 되지만 Map 인터페이스가 변경될 경우 수정할 코드가 생길 수도 있다.</br>
  따라서 Map과 같은 경계 인터페이스의 경우 별도 객체로 묶어 추상화 시키는 것이 제일이다.</br>
  또한, 공개 API 인수나 반환 값으로 경계 인터페이스를 사용하지 말자.
* 경계 살피고 익히기</br>
  **학습 테스트**: 간단하게 말하면 외부 API를 실제 내부 로직에 적용하기 전에 통제된 내부 환경에서 사용하려는 방식대로 외부 API를 호출해서 테스트
* log4j 익히기</br>
  Logger 콘솔 로거 초기화 하는게 여러 줄이라면 그것을 별도 객체로 만들어 사용하자.</br>
  그러면 만들어진 객체를 사용하는 사람은 log4j 경계 인터페이스 자체를 몰라도 된다.
* 학습 테스트는 공짜 이상이다.
* 아직 존재하지 않는 코드를 사용하기</br>
  API 사용을 한 interface로 캡술화해 API가 바뀔 때 수정해야하는 코드를 한 곳에 모으자.</br>
  **ADAPTER 패턴**: 클래스의 인터페이스를 다른 인터페이스로 변환

</br></br>
**소프트웨어 설계가 우수하다면 변경하는데 많은 투자와 재작업이 필요하지 않다.**