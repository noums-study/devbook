# 6장 열거 타입과 어노테이션
## int 상수 대신 열거 타입을 사용하라
```
// int 상수
// string 상수 또한 안 좋다
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
```

열거 타입 자체는 클래스이며 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개하는 싱글톤 형태다.</br>
열거 타입은 근본적으로 불변이라 열거 인스턴스 필드는 모두 public final이거나 private인 것이 좋다.</br>
특정 클래스에서만 쓰이는 열거 타입은 해당 클래스의 멤버 클래스로 만든다.</br>

* 상수별 메서드 구현(constant-specific method implementation): 추상 메서드를 선언하고 각 상수별 클래스 몸체(constant-specific class body), 즉 각 상수에서 자신에 맞게 재정의하는 것

```
public enum Operation {
  PLUS {
    public double apply(double x, double y) {
      return x + y;
    }
  },
  MINUS {
    public double apply(double x, double y) {
      return x - y;
    }
  },
  TIMES {
    public double apply(double x, double y) {
      return x * y;
    }
  }
  ,
  DIVIDE {
    public double apply(double x, double y) {
      return x / y;
    }
  }
  
  public abstract double apply(double x, double y);
}
```

switch 문은 열거 타입의 상수별 동작을 구현하는데 적합하지 않지만 기존 열거 타입에 또다른 기존 상수별 동작을 혼합해 넣을 때는 switch 문이 좋은 선택이 될 수 있다.</br>

```
public static Operation inverse(Operation op) {
  switch(op) {
    case PLUS: return Operation.MINUS;
    case MINUS: return Operation.PLUS;
    case TIMES: return Operation.DIVIDE;
    case DIVIDE: return Operation.TIMES;
  }
}
```

필요한 원소를 컴파일타임에 알 수 있는 상수 집합이라면 항상 열거 타입을 사용하자.</br>
열거 타입에 정의된 상수 개수가 영원히 고정 불변일 필요는 없구 추후에 상수가 추가되도 바어닐 수준에서 호환되도록 열거타입이 설계되어 있다.
</br></br>

## oridinal 메서드 대신 인스턴스 필드를 사용히라
열거 타입 순서가 바뀔 수도 있으며 중간에 값이 비울 수도 없는 등 제약 사항이 있으니 열거 타입 상수에 해당하는 숫자 값을 사용하고 싶으면 oridinal 메서드로 얻어서 사용하지 말고 인스턴스 필드에 저장해 상수 별 값을 명시 하는 것이 좋다.</br>
oridinal 함수 설명에도 프래그래머들이 이 메서드를 쓸 일이 없으며 oridinal은 EnumSet과 EnumMap 같이 열거 타입 기반의 범용 자료구조에 쓰일 목적으로 설계되었다고 쓰여 있다.
</br></br>

## 비트 필드 대신 EnumSet을 사용하라
list, set보다 빠르다는 장점 때문에 비트 필드를 사용하는 경우가 있곤 한다.</br>

```
public class Text {
  public static final int STYLE_BOLD = 1 << 0; // 1
  public static final int STYLE_ITALIC = 1 << 1; // 2
  public static final int STYLE_UNDERLINE = 1 << 2; // 4
  public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8
  
  // 매개변수 styles는 0개 이상의 STYLE_ 상수를 비트별 OR한 값이다.
  public void applySyles(int styles) {...}
```

```
text.applyStyles(STYLE_BOLD | STYLE_ITALIC);
```

하지만 이러한 비트 필드는 그 값이 무엇을 의미하는지 해석하기 위한 비트 연산이 필요하므로 이를 비트 필드보다는 열거형으로 나타내고 EnumSet을 이용하는 것이 좋다.</br>
EnumSet은 내부적으로 비트 벡터를 사용해서 비트 필드를 사용하는 것과 같은 효과를 볼 수 있다.</br>

```
public class Text {
  public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUCH }
  
  // 어떤 Set을 넘겨도 되나, EnumSet이 가장 좋다.
  public void applyStyles(Set<Style> styles) {...}
```
</br></br>

## oridinal 인덱싱 대신 EnumMap을 사용하라
oridinal은 상수를 추가하거나 제거할 시 바뀌므로 인덱싱으로 사용하기에는 값을 보장할 수 없다.</br>
EnumMap은 Enum에 특화된 Map으로 HashMap보다 빠르며 key인 Enum 순서대로 정렬된 컬렉션이며 내부적으로 배열로 동작한다.</br>

```
// LifeCycle에 따라서 Plant을 그룹화 짓는 것

// oridinal 인덱싱
for(Plant p: garden) {
  plantsByLifeCycle[p.lifeCycle.oridinal()].add(p);
}


// EnumMap 사용
Array.stream(garden).collect(groupingBy(p -> p.lifeCycle, () -> new EnumMap<>(LifeCycle.class), toSet())));
```
</br></br>

## 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라
열거 타입 자체는 확장할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다.</br>
그러나 열거 타입은 기본적으로 확장하지 않는 상수 타입을 사용하기 위한 클래스로 되도록이면 확장을 염두해두고 사용하지 않는다.</br>

```
public interface Operation {
  double apply(double x, double y);
}

public enum BasicOperation implements Operation {
  PLUS("+") {
    public double apply(double x, double y) { return x + y; }
  ...
  
  private final String symbol;

  BasicOperation(String symbol) {
    this.symbol = symbol;
  }

  @Override
  public String toString() {
    return symbol;
  }
}
```
</br></br>

## 명명 패턴보다는 어노테이션을 사용하라
예를 들어 JUnit 3에서는 메서드 이름을 'test'로 시작하게 명명 해놓으면 컴파일러가 해당 메서드는 테스트 메서드라는 것을 인지하고 JUnit이 테스트를 수행했다.</br>
이와 같은 기능적인 명명은 오타가 났을 경우 원하는대로 기능하지 않으며 함수 이름이 쓸데없이 길어지고 알아보기 어렵게 만든다.</br>
따라서 기능을 위해서는 명명 패턴보다는 어노테이션을 사용하는 것이 좋다.</br>
</br>

* 메타어노테이션(meta-annotation): @Retention, @Target과 같이 어노테이션 선언에 다는 어노테이션
* 마커 어노테이션(marker, annotation): 아무 매개변수 없이 단순 대상에 마킹(marking)하는 어노테이션

* @Repeatable</br>
  같은 어노테이션을 한 곳에 여러 개 달 수 있게 허용하는 메타어노테이션</br>
  단, @Repeatable 메타어노테이션을 갖는 어노테이션의 매개변수를 배열로 반환해줄 컨테이너 어노테이션을 별도 정의해줘야한다.</br>
  
  ```
  @Repeatable(value = ExceptionTestContainer.class)
  public @interface ExceptionTest {
    Class<? extends Throwable> value();
  }
  
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.METHOD)
  public @interface ExceptionTestContainer {
    Class<? extedns Trowable>[] value(); // 여러 번 같은 곳에 달린 ExceptionTest의 value를 묶어줌
  }
  ```
  Method.isAnnotationPresent로 @ExceptionTest가 달렸는지 
