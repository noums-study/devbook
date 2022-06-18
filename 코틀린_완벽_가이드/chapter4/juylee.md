# 4장 클래스와 객체 다루기

## 4.1 클래스 정의하기
### 4.1.1 클래스 내부 구조
자바 클래스와 비슷하며 프로퍼티(필드)에는 단순 변수 값 뿐만 아니라 함수가 포함될 수 있다.
위와 같은 경우에서는 클래스 인스턴스 내에 저장되는 대신 그 때 그 때 계산되거나 지연계산되거나 맵(map)에서 값을 얻어오는 등의 방식으로 프로퍼티의 값을 제공할 수 있다.

</br>
클래스 선언은 기본적으로 참조 타입(referential type)을 정의
* 참조 타입: 클래스 인스턴스의 실제 데이터 위치를 가리킴
</br>

인스턴스 수신 객체(receiver): 프로퍼티에 접근할 때 사용하는 객체</br>
(ex. p.firstName에서 p)</br>
</br>

클래스 내부에서 this를 디폴트로 가정하기에 생략 가능하나 프로퍼티와 메서드 파라미터 이름이 같은 경우 둘을 구분하기 위해 this 사용
``` kotlin
fun setName(firstName: String, familyName: String) {
  this.firstName = firstName
  this.familyName = familyName
}
```
</br>

코틀린에서는 자바와 달리 자동으로 getter, setter를 생성해주므로 프로퍼티 이름만으로 getter, setter 기능을 사용할 수 있다.</br>
<a href="https://stackoverflow.com/questions/10115588/what-is-the-difference-between-field-variable-attribute-and-property-in-java">field vs property vs attribute vs variable</a></br>
(field는 값 자체, property는 접근할 수 있게 getter, setter를 제공하며 외부 방식에 영향을 주지 않고 필드를 변경할 수 있는 추상화 제공 - 자바에서는 필드와 접근자 getter, setter를 묶어서 프로퍼티라고 함)</br>
kotlin 클래스 에서는 field 를 바로 선언할 수 없고 property 가 backing field 가 필요하면 알아서 제공한다. backing field 는 접근자 안에서 field 로 참조할 수 있다.
코틀린에서는 커스텀 getter, setter를 추가해서 프로퍼티의 구현을 바꿀 수 있다.
``` java
public class Test{
  private int count; field
  
  public int getCount() {
    return count;
  }
  
  public void setCount(int count) {
    if(count > 0) {
      this.count = count
    }
  }
}
```
</br>

``` kotlin
class Test {
  var count = 0
    set(value) {
      if(value > 0) field = value // backing field
    }
}
```
</br>

생성자를 호출하면 새 인스턴스에 대한 힙 메모리를 할당한 다음 생성자 코들르 호출한다.
</br>

코틀린의 접근제어자는 기본 public이다.</br>
자바에서는 기본 package private이어서 public을 꼭 명시해 줬어야하나 코틀린은 생략 한다.
</br>

코틀린은 파일 하나에 여러 공개 클래스를 넣을 수 있어 파일과 그 안의 클래스 이름이 일치하는 방식의 이름 짓기가 엄격한 규칙이 아니다.</br>
단, 보통은 파일 하나에 한 클래스가 있으면 일치 시킨다.
</br>
</br>

### 4.1.2 생성자
코틀린에서는 생성자를 호출할 때 자바의 new와 같은 특별한 키워드를 사용하지 않는다.</br>
``` kotlin
claa Pserson(firstName: String, familyName: String { // 주생성자(primary constructor)
  val fullName = "$firstName $familyName"
  
  init { // 초기화 블록. 여러 개일 수 있다.(순서대로 수행). 하나의 식으로 표현하기 어려운 복잡한 초기화 로직의 경우 여기에 작성해서 프로퍼티 초기화
    println("Created new Person instance: $fullName")
  }
}
```
</br>

컴파일러는 주생성자의 모든 실행 경로(초기화 블록 포함)가 모든 멤버 프로퍼티를 초기화하거나 예외를 발생시키는지 확인할 수 없다면 에러를 발생시킨다.</br>
``` 
error: property must be initialized or be abstract
```
</br>

주생성자 파라미터를 프로퍼티 초기화나 init 블록 밖에서는 사용할 수 없다.</br>
하지만 코틀린에서는 간단하게 생성자 파라미터의 값을 멤버 프로퍼티로 만들 수 있다.</br>
이 때 파라미터 이름을 프로퍼티 초기화나 init 블록 안에서 참조하면 생성자 파라미터를 가리키고, 다른 위치에서 참조하면 프로퍼티를 가리키게 된다.</br>
``` kotlin
class Person(firstName: String, familyName: String) {
// class Person val firstName: String, familyName: String) { // no error
  val fullName = "$fistName $familyName"
  
  fun printFirstName() {
    println(firstName) // Error: first name is not available here
  }
}
```
</br>

컴파일러는 주생성자의 모든 실행 경로(초기화 블록 포함)가 모든 멤버 프로퍼티를 초기화하거나 예외를 발생시키는지 확인할 수 없다면 에러를 발생시킨다.
</br>

코틀린에서 가변 인자는 vararg를 사용한다.</br>
``` kotlin
class Room(vararg val personList: Person) {
  fun showNames() {
    for(person in psersonList) {
      println(person.fullName)
    }
  }
}
```
</br>

부생성자는 constructor 키워드를 사용해서 정의한다.</br>
부생성자 파라미터에는 val/var를 사용할 수 없다.</br>
``` kotlin
class Person (
  val firstName: String,
  val familyName: String
) {
  constructor(fullName: String) {
    ...
    // 부생성자는 기본 Unit 타입 값을 반환한다. 따라서 reutrn 사용 가능
  }
}
```
</br>

주생성자가 선언되어 있지 않다면 부생성자 실행 전에 프로퍼티 초기화와 init 블록을 실행한다. 이는 어떤 부생성자를 호출하든지 공통적인 초기화 코드가 정확히 한 번만 실행되게 하기 위함이다.
``` kotlin
class TestClass{
    private val test: String

    init {
        println("this is init!")
    }

    constructor(test: String) { // 부생성자 통해 생성시 위에 init 블록이 먼저 실행됨
        this.test = test
    }

    fun printTest() {
        println(test)
    }
}
```
</br>

부생성자가 생성자 위임 호출을 사용해 다른 부생성자를 호출할 수도 있다.</br>
``` kotlin
class Person {
  val fullName: String
  
  constructor(firstName: String, familyName: String): this("$firstName $familyName") // 생성자 위임 호출
  
  constructor(fullName: String) {
    this.fullName = fullName
  }
}
```
</br>


주생성자가 있는 경우 부생성자는 반드시 주생성자 위임 호출을 해야한다. (안 그럼 컴파일 에러 발생)
</br>
</br>

### 4.1.3 멤버 가시성
* public(공개): 기본값. 어디서나 볼 수 있음
* internal(모듈 내부): 멤버가 속한 클래스가 포함된 컴파일 모듈 내부에서만 볼 수 있음
* protected(보호): 멤버가 속한 클래스와 그 클래스의 하위 클래스에서 볼 수 있음
* private(비공개): 멤버가 속한 클래스 내부에서만 볼 수 있음
</br>

주생성자의 가시성을 지정하려면 constructor 키워드를 꼭 명시해야 한다.</br>
``` kotlin
class Empty private constructor() {
  fun showMe() = println("Empty")
}
```
</br>
</br>

### 4.1.4 내포된 클래스
``` kotlin
class Person(val id: Id, val age: Int) {
  class Id(val firstName: String, val familyName: String)
  fun showMe() = println("${id.firstName} ${id.familyName}, $age)
}

fun main() {
  val id = Person.Id("John", "Doe")
  val person = Person(id, 25)
  person.showMe()
}
```
</br>

내포된 클래스에 inner를 붙이면 자신을 툴러싼 외부 클래스의 현재 인스턴스에 접근할 수 있다.</br>
``` kotlin
class Person(val firstName: String, val familyName: String) {
  inner class Possession(val description: String) {
    fun showOwner() = println(fullName())
  }
  
  private fun fullName() = "$firstName $familyName"
}

fun main() {
  val person = Person("John", "Doe")
  val wallet = person.Possession("Wallet") // inner class의 경우 생성자 호출을 위해서는 외부 클래스 인스턴스를 먼저 생성해야 한다.
  ...
}
```
</br>

일반적으로 this는 항상 가장 내부 클래스 인스턴스를 가리킨다. 따라서 내부 클래스에서 외부 클래스 인스턴스를 가리켜야 한다면 한정시킨 this를 사용해야 한다.</br>
``` kotlin
class Person(val firstName: String, val familyName: String) {
  inner class Possession(val description: String) {
    fun getOwner() = this@Person // @ 기호 다음에 외부 클래스 이름
  }
}
```
</br>

자바에서는 내부 클래스에 inner를 안 붙여도 되지만 코틀린과 달리 외부 클래스 인스턴스와 연관되길 원하지 않으면 static을 붙여야한다. (코틀린은 inner가 없는 내포 클래스)
</br>
</br>

### 4.1.5 지역 클래스
지역 클래스: 함수 본문에 정의한 클래스</br>
지역 클래스는 자신을 둘러싼 코드의 선언에 접근할 수 있고 변경할 수도 있다. (자바에서는 변경할 수 없다.)</br>
단, 둘러싼 코드 선언에 접근하고 변경할 경우 컴파일러는 공유되는 값을 특별한 wrapper 객체로 둘러싸는 비용이 발생한다.</br>
``` kotlin
fun main() {
  var x = 1
  
  class Counter {
    fun increment() {
      x ++
    }
  }
  ...
}
```
</br>

지역 클래스에는 가시성 변경자는 붙일 수 없다. (자신을 둘러싼 블록으로 제한되므로)</br>
지역 클래스 또한 내부 클래스를 가질 수 있으나 내포 클래스(inner 키워드가 없는)는 못 가진다. (내포 클래스는 외부 클래스가 접근할 수 있는 값에 접근을 못하게 되므로)</br>

## 4.2 널 가능성
코틀린은 널 값이 될 수 있는 참조 타입과 널 값이 될 수 없는 참조 타입을 확실하게 구분해줘서 런타임에서만 발견할 수 있는 NullPointerException이나 null 참조가 생기는 시점과 NullPointerException이 발생하는 시점에 차이가 발생하는 것을 방지해준다.
</br>

### 4.2.1 널이 될 수 있는 타입
코트린의 참조 타입은 기본적으로 널이 될 수 없는 타입으로 간주한다. (자바에서는 기본적으로 널이 될 수 있는 타입으로 간주했다.)
</br>

널이 될 수 있는 타입은 뒤에 ?를 붙인다. (String?)</br>
런타임 때는 널이 될 수 있는 값과 될 수 없는 값은 차이가 없고 컴파일 떄에만 구분한다. (Optional 사용 X) 따라서 널이 될 수 있는 값이라고 해서 런타임 시 추가 비용일 발생하거나 하지 않는다.</br>
단, Int, Boolean과 값은 원시타입의 널이 될 수 있는 타입은 박싱한 타입의 값을 참조한다.</br>
``` kotlin
val n: Int = 1 // 원시 타입 값
val x: Int? = 1 // 박싱한 타입의 값 참조
```
</br>

Nothing? : 널 상수 이외의 어떤 값도 포함하지 않는 null 값 자체의 타입으로 다른 널이 될 수 있는 모든 타입의 하위 타입이다.</br>
Any? : 널이 될 수 있는 모든 타입의 상위 타입이다. (Any는 코틀린 타입에서 제일 큰 타입)
</br>

널이 될 수 있는 타입은 해당 타입의 어떤 프로퍼티나 메서드를 제공하지 않는다. (null에서 의미가 없기 때문)</br>
단, 코틀린 확장 메커니즘을 활용해 널이 될 수 있는 타입 자체적인 메서드와 프로퍼티를 제공한다.</br>
그래서 String?에 +로 문자열을 붙일 수 있고 null.toString()시 "null"을 반환한다.</br>
``` kotlin
fun isLetterString(s: String?): Boolean {
  if(s.isEmpty()) return false // 에러 발생. isEmpty를 쓸 수 없음.
  ...
}
```
</br>
</br>

### 4.2.2 널 가능성과 스마트 캐스트
스마트 캐스트(smart cast)라는 코틀린의 기능은 null에 대한 검사를 수행하면 컴파일러는 코드 흐름에서 이제 이 값이 null이 아님을 인지할 수 있게 되고 그 값을 null이 아닌 타입으로 타입 변환해 사용하게 된다.</br>
만약 널 검사한 시점과 사용 시점 사이에 값이 변하면 컴파일러는 이 값이 null이 아님을 확신할 수 없어 null이 아닌 타입으로 캐스팅하지 않는다.</br>
이 스마트 캐스트는 when 과 ||, && 연산의 오른쪽에서도 동작한다.</br>
``` kotlin
when(n) {
  null -> "null"
  else -> { 
    ... // 위에서 null 체크를 했으므로 여기서는 null이 아닌 타입으로 캐스팅됨
  }
}

if(s != null && s.length == 1) {...}
```
</br>
</br>

### 4.2.3 널 아님 단언 연산자
!!는 null이 아님을 단언할 수 있지만 KotlinNullPointerException을 발생시킬 수 있다.</br>
!!는 되도록 쓰지 않는게 좋지만 컴파일러가 인식하기 힘들지만 확실히 null이 아닌 값인 경우 사용할 경우 정당화 할 수 있다.</br>
하지만 정당화 할 수 있는 경우도 코드 제어흐름을 고치는 등 컴팡일러가 스마트 캐스트를 적용할 수 있게 하는 것이 좋다.</br>
``` kotlin
fun main() {
  var name: String? = null
  
  fun initialize() {
    name = "John"
  }
  
  fun sayHello() {
    println(name!!.uppercase()
  }
  
  initialize()
  sayHello()
}
```
</br>

### 4.2.4 안전한 호출 연산자
?.는 안전한 호출 연산자로 왼쪽이 널이 아닐 경우 일반적인 함수 호출처럼 작동하고 널일 경우 오른쪽 호출을 수행하지 않는다.</br>
우선순위는 .함수 호출과 같은 수준이다.</br>
안전한 호출 연산자는 null을 반환할 수 있어 연쇄 호출 할 수 있다.</br>
``` kotlin
// readLine()?.toInt() 결과가 null을 반환
println(readLine()?.toInt()?.toString(16))
```

### 4.2.5 엘비스 연산자
?:를 사용하면 null을 대신할 디폴트 값을 제정할 수 있다.</br>
``` kotlin
val s = name?: "default value!"
```
</br>

우선 순위: in, !in > 엘비스 연산자 > or, ||, &&
</br>
</br>

## 4.3 단순한 변수 이상인 프로퍼티
### 4.3.1 최상위 프로퍼티
최상위 프로퍼티에 가시성(public/internal/private)를 지정할 수 있으며 임포트 디렉티브에서 최상위 프로퍼티를 임포트 할 수도 있다.
* util.kt
``` kotlin
val prefix = "Hello" // 최상위 불변 프로퍼티. 전역 변수나 상수 역할

class Test {
  ...
}
```
* main.kt
``` kotlin
import util.prefix

fun main() {
  println("$prefix, I'm ooo")
}
```
</br>
</br>

### 4.3.2 늦은 초기화
lateinit는 생성자에서 초기화되지 않으나 다른 곳에서 초기화 되지 않는 프로퍼티에 붙일 수 있다.</br>
lateinit가 붙은 프로피의 값을 읽었을 때 프로퍼티가 초기화되지 않은 경우 UninitializedPropertyAccessException이 발생한다.</br>
프로퍼티가 변경될 수 있는 지점이(초기화 될 수 있는 지점이) 여러 군데일 수 있으므로 var를 사용해야 한다.</br>
또한 Int, Boolean과 같은 원시 값을 표현하는 타입이 아니어야 한다. lateinit는 초기화되지 않은 상태를 표현하기 위해 널이 될 수 있는 값으로 표현하는데 원시 타입은 바로 null을 넣을 수 없기 때문이다. (박싱이 필요)
</br>
</br>

### 4.3.3 커스텀 접근자 사용하기
``` kotlin
class Person(val firstName: String, val familyName: String) {
  val fullName: String
    get(): String { 
    // fullName 프로퍼티 호출하면 자동으로 커스텀 getter 호출됨(파라미터가 없고 프로퍼티와 반환 타입이 같아야 함. 따라서 반환 타입 생략 가능)
    // fullName은 초기화 없이 읽을 때마다 다시 계산한다. (뒷받침 하는 필드가 없으므로 fullName은 클래스 인스턴스에서 전혀 메모리를 차지하지 않는다.)
      return "$firstName $familyName"
    }
}
```
</br>

뒷밭침 하는 필드 참조 field라는 키워드는 접근자의 본문 안에서만 유용하다.</br>
``` kotlin
class Person(val firstName: String, val familyName: String, age: Int) {
  val age: Int = age // 뒷받침하는 필드가 있으므로 메모리 차지함
    get(): Int {
      println("Accessing age")
      return field
    }
}
```
</br>

커스텀 getter가 파라미터가 없는 함수와 비슷하지만 함수보가 프로퍼티를 사용하는 쪽을 권장하는 경우
* 값을 계산하는 비용이 충분히 쌈
* 예외가 발생할 여지가 없음
* 값을 캐시해둠
* 함수를 호출해도 항상 똑같은 결과를 내는 경우
* 인스턴스의 상태가 바뀌기 전에 여러 번 해당 프로퍼티를 읽는 경우
</br>

``` kotlin
class Person(val firstName: Stringm val familyName: String) {
  var age: Int? = null
    set(value) {
    // 프로퍼티와 같은 타입의 파라미터 하나만 가질 수 있다. 따라서 파라미터 타입 생략 가능
      if(value != null && value <= 0) {
        throw IllegalArgumentException("Invalid age: $value")
      }
      field = value
    }
 }
 ```
 </br>
 
 접근자에 접근제어자(가시성 변경자)를 붙일 수 있어 클래스 외부에서는 프로퍼티의 값을 변경하지 못하게 해 밖에서는 해당 프로퍼티가 불변인 것처럼 할 수 있다.</br>
 
``` kotlin
class Person(name: String) {
  var lastChanged: Date? = null
     private set // Person 클래스 밖에서는 변경할 수 없음
   
  var name: String = name
    set(value) {
      lastChanged = Date() // 여기 내부에서만 lastChanged 변경
      field = value
    }
  }
}
```
</br>

lateinit 프로퍼티의 경우 자동으로 접근자가 생성되기 때문에 직접 커스텀 접근자를 정의할 수 없다.</br>
주생성자 파라미터로 선언된 프로퍼티에 대한 커스텀 접근자도 정의할 수 없다.
</br>
</br>

### 4.3.4 지연 계산 프로퍼티와 위임
``` kotlin
val text by lazy { // 위임 프로퍼티
  File("data.txt").readText()
}

fun main() {
  while(true) {
    when(val commane = readLine()?: return) {
      "print data" -> println(text) // 이 때 text가 lazy 블록으로 초기화 되고 필드에 값이 저장되고 그 후에는 프로퍼티를 읽을 때마다 저장된 필드 값을 읽는다.
      "exit" -> return
    }
  }
}
```
</br>

위임 객체(delegate object): 프로퍼티 처리에 필요한 데이터를 모아 유지하면서 읽기와 쓰기를 처리한다. by 키워드 다음에 위치한다.</br>
lazy 프로퍼티는 초기화 된 다음에 변하지 않아 val로 선언해야한다.</br>
또한 lazy 프로퍼티는 다중 스레드 환경에서도 값을 한 스레드 안에서만 계산하기 때문에 thread-safe 하다.</br>
위임 프로퍼티는 구현이 다 다를 수 있기 때문에 커스텀 접근자로 정의된 프로퍼티처럼 다뤄진다. 따라서 스마트 캐스트를 쓸 수 없다.
</br>
</br>

## 4.4 객체
### 4.4.1 객체 선언
코틀린은 어떤 클래스에 인스턴스가 오직 하나만 존재하게 보장하는 싱클턴 패턴을 내장하고 있어 object라는 키워드를 사용해 싱글턴을 선언할 수 있다.</br>
초기화는 싱글턴 클래스가 실제 로딩되는 시점까지 지연된다. (프로그램이 객체 인스턴스에 처음 접근할 때 초기화)</br>
``` kotlin
object Application {
  val name = "My Application"
  
  ovveride fun toString() = name
  
  fun exit() {}
}
```
</br>
``` kotlin
fun describe(app: Application) = app.name // Application type으로 사용

fun main() {
  println(Application) // Application 값으로 사용. "My Application"이 출력됨
}
```
</br>

싱글턴 클래스의 경우는 주생성자나 부생성자가 없다. 항상 암시적으로 만들어지기 때문에 생성자 호출이 의미가 없기 때문이다. 단, 초기화 블록은 존재할 수 있다.</br>
