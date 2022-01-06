# 2장 객체 생성과 파괴

## 생성자 대신 정적 팩토리 메서드를 고려하라
* 정적 팩토리 메서드(static factory method)

  ```
  public static Boolean valudOf(boolean b) {
    retuen b ? Boolean.TRUE : Boolean.FALSE;
  }
  ```
  <i>잠점</i>
  </br>
  생성자와 달리 이름을 명시하기 좋다.</br>
  호출 될 때마다 인스턴스를 새로 생성하지 않아도 되서 생성 비용이 큰 같은 객체가 자주 요청되는 상황에서 성능을 끌어올려 준다.</br>
  * 플라이웨이트 패턴(Flyweight Pattern): 인스턴스 한 개만 가지고 여러 개의 가상 인스턴스를 제공하고 싶을 때 사용하는 패턴</br>
  
  인스턴스 생성을 제어할 수 있는 인스턴스 통제(instance-controlled) 클래스를 구현할 수 있다.</br>
  하위 타입의 객체를 반환할 수 있게 해 유연성을 높일 수 있다.</br>
  입력 매개 변수에 따라서 매번 다른 클래스의 객체를 반환할 수 있다.</br>
  정적 팩토리 메서드를 작성하는 시점에 반환할 객체의 클래스가 존재하지 않아도 된다.</br>
  * 브리지 패턴(Bridge Pattern): 클래스에서 기능과 클래스 구현에 대해서 두 개를 별도의 클래스로 구현해 독립적으로 변형이 가능하고 확장이 가능하도록 한 디자인 패턴.</br>
  
  <i>단점</i>
  </br>
  상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.</br>
  정적 팩토리 메서드는 프로그래머가 찾기 어려워 메서드 이름을 널리 알려진 규약에 따라 지어야 한다.
</br></br>

## 생성자에 매개변수가 많다면 빌더를 고려하라
점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.</br>
* 자바빈즈 패턴(javaBeans pattern)</br>
  매개변수가 없는 생성자로 객체를 만든 후, Setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식</br>
  객체가 완전히 생성되기 전까지는 일관성이 무너진 상태이며 불변으로 만들 수 없다.</br>
* 빌더 패턴(Builder patten)</br>
  필수 매개변수 만으로 빌더 객체를 얻어 빌더 객체의 Setter 메서드들로 필요한 매개변수들을 설정하고 build 메서드를 호출해 객체를 얻는 방식</br>
  
  ```
  public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    ...
    
    public static class Builder {
       // 필수
       private final int servingSize;
       private final int servings;
       
       // 선택 매개변수 초기화
       private int calories = 0;
       private int fat = 0;
       
       public Builder(int sergingSize, int servings) {
        this.servingSize = servingSize;
        this.servings = servings;
       }
       
       public Builder calories(int calories) { this.calories = calories; }
       ...
       public NutritionFacts build() { return new NutritionFacts(this); }
    }
    
    private NutritionFacts(Builder builder) {
      this.servingSize = builder.servingSize;
      this.servings = builder.servings;
      this.calories = builder.calories;
      this.fat = builder.fat;
      ...
    }
  }
  ```
  
  ```
    NutritionFacts cocaCola = new NutritionFacts.Builder(240, 9).calories(100).build();
  ```
  
    * 플루언트 API(fluent API) / 메서드 연쇄(method chaining): Builder의 Setter가 Builder 자기 자신을 반환해서 Setter를 물 흐르듯이 연결해서 사용할 수 있는 것</br>
    * 불변식(invariant)</br>
      프로그램이 실행되는 동안, 혹은 정해진 기간 동안 반드시 만족해야하는 조건</br>
      예) 리스트의 크기는 반드시 0 이상, Period 클래스의 start 필드는 반드시 end 필드보다 작아야함</br>
  
  빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.</br>
    * 공변 반환 타이핑(covariant return typing): 상속 받아서 구현한 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능
 </br></br>
 
 ## private 생성자나 열거 타입으로 싱글톤임을 보증하라
 클래스를 싱글턴으로 만들면 싱글톤 인스턴스를 가짜(mock) 구현으로 대체할 수 없을 수 없어(인터페이스를 구현한 클래스 제외) 사용하는 클라이어트를 테스트하기가 어려워 질 수도 있다.</br>
 생성자가 private이어도 reflection API의 AccessibleObject.setAccessible를 이용해서 호출할 수 있으므로 생성자에 두 번째 객체가 생성되려 할 때 예외를 던지도록 하는게 좋다.</br>
 
 <i>정적 팩토리 메서드로 싱글턴 구현</i>
 </br>
 ```
 public class Elvis {
  private static final Elvis INSTANCE = new Elvis();
  private Elvis() {...}
  public static Elvis getInstance() { return INSTANCE; }
  ...
 }
 ```
 API를 바꾸지 않고도 싱글톤이 아니게 변경할 수 있다.</br>
 정적 팩토리 메서드를 통해 다른 인스턴르를 넘겨주게 할 수도 있다.</br>
 정적 팩토리 메서드를 제너릭 싱글턴 팩토리로 만들 수 있다.</br>
 정적 팩토리의 메서드 참고를 공급자로 사용할 수 있다.(Elvis::getInstance -> Supplier<Elvis>)</br>
 </br>
 
 싱글톤 클래스는 Serializable로만 구현할 경우 역직렬화할 때마다 새로운 인스턴스를 만드므로 readResolve를 구현해야한다.</br>
 
 ```
 private Object readResolve() {
  return INSTANCE;
 }
 ```
 </br>
 
 원소가 하나뿐인 열거 타입의 싱글톤은 열거형으로 만드는 것이 좋다.</br>
 
 ```
 public enum Elvis {
  INSTANCE;
  
  ...
 }
 ```
 
</br></br>

## 인스턴화를 막으려거든 private 생성자를 사용하라
정적 메서드와 정적 필드만을 담은 클래스를 만들고 싶을 때 private 생성자를 명시해 생성자가 호출되는 것을 막을 수 있다.
</br></br>

## 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
사용하는 자원(필드 등)이 동작에 따라 달라지는 클래스에는 정적 유틸리티 클래스나 싱글톤 방식이 적합하지 않다.</br>
-> 이럴 때 정적 유틸리티 클래스나 싱글톤 방식이 아닌 의존 객체 주입을 통해서 사용하는 자원을 생성자로 주입 받아서 사용한다.
</br></br>

## 불필요한 객체 생성을 피하라
String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다.</br>

```
static boolean isRomanNumberal(String s) {
  // matches()의 매개변수인 Pattern가 호출될 때마다 생성된다
  // Pattern은 인스턴스 생성 비용이 높다
  return s.matches("^(?=.)M*C[MD]|D?C{0,3})(X[CL]|L?X{0,3}){I[XV]|V?I{0,3})$");
}
```
* 오토박싱(auto boxing): 기본 타입이 래퍼 클래스로 자동 변환 되는 것
박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.</br>
단순 객체 생성을 피하려고 객체 풀(pool)을 사용하는 것은 메모리 사용량을 늘리고 성능을 떨어뜨린다.
</br></br>

## 다 쓴 객체 참조를 해제하라
다 쓴 참조를 null 처리하면 다 쓴 참조를 해제할 수 있다.</br>
허자먼 객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.</br>
다 쓴 참조를 해제하는 가장 좋은 방법은 변수의 범위를 최소가 되게 정의해서 그 참조를 담은 별수를 유효 범위(scope) 밖으로 밀어내는 것이다.</br>
자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.
</br></br>

## finalizer와 cleaner 사용을 피하라
객체 소멸자인 finailzer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요 하다.</br>
cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.</br>
finalizer, cleaner는 실행되기까지 얼마나 걸릴지 알 수 없어 제때 실행되어야 하는 작업은 절대 할 수 없다.</br>
데이터베이스 같은 공유 자원의 영구 락(lock) 해제와 같은 상태를 영구적으로 수정하는 작업에서는 finalizer나 cleaner에 의존해서는 안 된다.</br>
finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
</br></br>

## try-finally보다는 try-with-resources를 사용하라
try-with-resources 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현해야 한다.</br>

```
static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src);
        OutputStream out = new OutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0 )
          out.write(buf, 0, n);
       }
}
```
</br></br></br></br>
