# 4장 클래스와 인터페이스
## 클래스와 멤버의 접근 권한을 최소화하라
<i>정보 은닉의 장점</i>
</br>
* 여러 컴포넌트를 병렬로 개발할 수 있기 때문에 시스템 개발 속도를 높인다.
* 시스템 관리 비용을 낮춘다.
* 다른 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화할 수 있기 때문에 성능 최적화에 도움을 준다.
* 소프트웨어의 재사용성을 높인다.
* 시스템 전체가 아직 완성되지 않은 상태에서도 개별 컴포넌트의 동작을 검증할 수 있기 때문에 큰 시스템을 제작하는 난이도를 낮춰준다.
</br>

* package-private: 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있는 것으로 접근 제한자를 명시하지 않았을 경우 적용된다.
* 상위 클래스의 메서드를 재정의할 때 그 접근 수준을 상위 클래스에서보다 좁게 설정할 수 없다.
* public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.
* public 가변 필드를 갖는 클래스는 일반적으로 스레드에 안전하지 않다.
</br></br>

## public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라
package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.
</br></br>

## 변경 가능성을 최소화하라
<i>불변 클래스 생성 규칙</i>
</br>
* 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
* 클래스를 확장할 수 없도록 한다.
* 모든 필드를 final로 선언한다.
* 모든 필드를 private으로 선언한다.
* 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.
</br>

* 함수형 프로그래밍: 피연산자에 함수를 적용해서 그 결과를 반환하지만, 피연산자 자체는 그대로인 프로그래밍 패턴

```
...
public Complex minus(Complex c) {
  return new Complex(re - c.re, im - c.im);
}
...
```

* 불변 객체는 스레드에 안전하여 따로 동기화할 필요가 없다.
* 실패 원자성(failure atomicity): 메서드에서 예외가 발생한 후에도 그 객체는 여전시 메서드 호출 전과 똑같은 유효한 상태여야 한다.
* 단, 불변 클래스는 값이 다르면 반드시 독립된 객체(인스턴스)로 만들어야 한다.
* 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.
</br></br>

## 상속(클래스가 다른 클래스를 확장, extends)보다는 컴포지션을 사용하라
메서드 호출과 달리 상속(extends)은 캡슐화를 깨뜨린다.</br>
컴포지션(composition): 기존 클래스의 인스턴스를 private 필드로 사용해 기존 클래스가 새로운 클래스의 구성요소로 쓰이게 하는 설계</br>

```
// Set 인스턴스를 감싸고 있으므로 래퍼 클래스라고 한다.
// 또는 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패던(Decorator pattern)이라고 한다.
public class InstrumentedSet<E> extends ForwardingSet<E> {
  private int addCount = 0;
  
  public InstrumentedSet(Set<E> s) {
    super(s);
  }
  
  @Override
  public boolean add(E e) {
    addCount++;
    return super.add(e);
  }
  
  @Override
  public boolean addAll(Collection<? extends E> c) {
    addCount += c.size();
    return super.addAll(c);
  }
  
  public int getAddCount() {
    return addCount;
  }
}
```

```
public class ForwardingSet<E> implements Set<E> {
  private final Set<E> s;
  public ForwardingSet(Set<E> s) { this.s = s; }
  
  public void clear() { s.clear(); }
  ...
  public boolean add(E e) { return s.add(e); }
  ...
  public boolean addAll(Bollection<? extends E> c) { return s.addAll(c); }
  ...
  @Override
  public boolean equals(Object o) { return s.equals(o); }
  ...
}
```

* 상속은 상위 클래스와 하위 클래스가 순수하게 is-a 관계일 때만 써야 한다.
* 상속 문제점 예: Properties p가 있을 때 p.getProperty(key)와 p.get(key)는 결과가 다를 수 있다.
  -> getProperty(key)는 Properties의 동작이나 get(key)는 Properties의 상위 클래스인 Hashtable로부터 물려받은 동작이므로 문제가 있을 수 있다.
</br></br>

## 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라
상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지 문서로 남겨야 한다.</br>
보통 javaDoc에 @implSepc에 메서드의 내부 동작 방식을 설명한다.</br>

```
    /**
     *
     * @param x
     * @param y
     * @implSpec // 여기에 서술
     */
    public void test(int x, int y) {
```

클래스의 내부 동작과정 중간에 끼어들 수 있는 훅(hook)(동작 과정에 호출되는 다른 내부 메서드)을 잘 선별하여 protected 메서드 형태로 공개해야 할 수도 있다.</br>
상속용 클래스를 시험하는 방법은 직접 하위 클래스를 만들어보는 것이 '유일'하다.</br>
상속용으로 설계한 클래스는 배포 전에 반드시 하위 클래스를 만들어 검증해야 한다.</br>
상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안 된다.</br>
-> 상위 클래스 생성자에서 쓴 메서드를 하위 클래스에서 재정의 해 하위 클래스의 생성자에서 초기화하는 값을 다룬다면 상위 클래스의 생성자가 먼저 호출되므로 의도하지 않는 대로 동작할 가능성이 있다.</br>

```
public final class Sub extends Super {
  // 생성자에서 초기화한다.
  private final Instant instant;
  
  Sub() {
    instant = Instant.now();
  }
  
  // 총 두 번 호출되는데
  // 1. 상위 클래스의 생성자를 호출하므로 null 출력(상위 클래스에서는 instant를 초기화 하지 않으므로)
  // 2. 상위 클래스 생성자 호출 후 하위 클래스의 생성자가 호출되서 instant가 출력됨
  @Override
  public void overrideMe() {
    System.out.printlnt(instant); 
  }
  
  public static void main(String[] args) {
     Sub sub = new Sub();
     sub.overrideMe();
  }
}
```

clone과 readObject 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안 된다.</br>
Serializable을 구현한 상속용 클래스가 readResolve나 writeReplace 메서드를 갖는다면, private으로 선언 히 하위 클래스에서 무시되기 때문에 protected로 선언해야 한다.
</br></br>

## 추상 클래스보다는 인터페이스를 우선하라
인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.</br>
인터페이스는 계층구조가 없는 타입 프레임워크를 만들 수 있다.</br>
인터페이스는 기능을 향상 시키는 안전하고 강력한 수단이 된다.</br>
equals, hashCode와 같은 메서드들을 디폴트 메서드로 제공해서는 안 되며 인스턴스 필드를 가질 수 없고 public이 아닌 정적 멤버도 가질 수 없다.</br>
복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법이 좋다.</br>
골격 구현은 가능한 한 인터페이스에 디폴트 메서드로 구현해 제공하고 인터페이스의 구현상의 제약 때문에 디폴트 메소드로 구현이 불가한 경우에는 인터페이스를 구현한 추상 클래스로 제공한다.
</br></br>

## 인터페이스는 구현하는 쪽을 생각해 설계하라
디폴트 메서드는 컴파일에 성공하더라도 기존 구현체에 런타임 오류를 일으킬 수 있다.
</br></br>

## 인터페이스는 타입을 정의하는 용도로만 사용하라
상수 인터페이스 안티패턴은 인터페이스를 잘못 사용한 예다.</br>

```
public interface PhysicalConstants {
  static final double AVOGADROS_NUMBER = 6.022140857e23;
  
  static final double BOLTZMANN_CONSTANT = 1.38064852e-23;
  
  static final double ELECTRON_MASS = 9.10938356e-31;
}
```
</br></br>

## 태그 달린 클래스보다는 클래스 계층구조를 활용하라
```
class Figure {
  enum Shape { RECTANGLE, CIRCLE };
  
  // 태그 필드
  final Shape shape;
  
  // CIRCLE(원)일 때만 쓰이고 RECTANGLE(사각형)일 때는 사용되지 않는다.
  double radius;
  
  ...
  
  // CIRCLE(원)일 때만 쓰이는 생성자
  Figure(double radius) {
    shape = SHAPE.CIRCLE;
    this.radius = radius;
  }
  
  ...
  
  double area() {
    switch(shape) {
      case RECTANGLE:
        return length * width;
    ...
  }
  ...
}
```

root가 될 추상 클래스를 정의하고 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언해서 계층 태그 대신 계층 구조를 활용한다.
</br></br>

## 멤버 클래스는 되도록 static으로 만들라
정규화된 this: 겉클래스.this로 비정적 멤버 클래스(innerClass)에서 겉 클래스의 인스턴스나 메서드를 호출 할 수 있다.</br>

```
public class OutClass {
  final String str = "This is the test!";
  
  // public이든 private이든 static만 안 붙으면 됨(비정적 멤버 클래스)
  class InClass {
    public void test() {
      System.out.println(OutClass.this.str);
    }
  }
}
```
멤버 클래스(innerClass)에서 바깥 인스턴스(겉 클래스의 인스턴스)에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.
</br></br>

## 톱레벨 클래스는 한 파일에 하나만 담으라
</br></br></br></br>
