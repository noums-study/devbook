# 8장 메서드
# 매개변수가 유효한지 검사하라
```
this.strategy = Objects.requireNonNull(strategy, "전략);
```

```
private static voic sort(long a[], int offset, int length) {
  assert a != null;
  assert obbset >= 0 && offset <= a.length;
  assert length >= 0 && length <= a.length - offset;
  ...
}
```

유효성 검사 비용이 지나치게 높거나 계산 과정에서 암묵적으로 검사가 수행될 때는 메서드 몸체 실행 전에 매개변수 유효성 검사를 수행하지 않기도 한다.</br>
</br></br>

## 적시에 방어적 복사본을 만들라
Date는 가변으로 낡은 API이니 새로운 코드를 작성할 때는 더 이상 사용하지 않는 것을 추천한다.</br>

```
public final class Period {
  private final Date start;
  private final Date end;
  
  public Period(Date start, Date end) {
    if(start.compareTo(end) > 0) {
      throw new IllegalArgumentException(start + "가 " + end + "보다 늦다.");
    }
    
    this.start = start;
    this.end = end;
  }
  
  ...
}
```

위와 같이 Period 클래스를 생성하면 아래와 같이 Period 인스턴스 내부의 규칙을 깰 수 있다.

```
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78); // 참조되어 있는 Period p의 end의 값이 변경되 Period의 start <= end 규칙이 깨진다.
```

따라서 인스턴스 내부를 보호하려면 생성자에서 받은 가변 매개변수 각각을 방어적으로 복사(defensive copy)해야 한다.</br>

```
public Period(Date start, Date end) {  
  this.start = new Date(start.getTime());
  this.end = new Date(end.getTime());
  
  // 멀티쓰레드 환경에서 Period 조건에 맞는지 start, end를 검사하기 전에 원본이 변경될 수 있으므로 복사하고 그 복사본으로 검사한다.
  if(this.start.compareTo(this.end) > 0) {
    throw new IllegalArgumentException(this.start + "가 " + this.end + "보다 늦다.");
  }
}
```

* 검사시점/사용시점(time-of-check/time-of-use, TOCTOU) 공격: 원본 객체의 유효성 검사한 후 복사본을 만드는 그 찰나의 취약한 순간에 원본 객체를 수정하는 공격

매개변수가 제3자에 의해 확장될 수 있는 타입이라면 방어적 복사본을 만들 때 clone을 사용해서는 안 된다.</br>
만일 가변 필드를 외부에서 접근가능하게 만들어놨을 경우 아래와 같이 가변 필드의 복사본을 반환해 수정하면 안 되는 가변필드를 수정하는 공격을 막자.</br>

```
public Date start() {
  return new Date(start.getTime());
}

public Date end() {
  return new Date(end.getTime());
}
```
</br></br>

## 메서드 시그니처를 신중히 설계하라
* 메서드 이름을 신줗이 짓자
* 편의 메서드를 너무 많이 만들지 말자
* 매개변수 목록은 짧게 유지하자.(4개 이하가 좋다.)
* 매개변수로 적합한 인터페이스가 있다면 그걸 구현한 클래스가 아닌 그 인터페이스를 사용하는 것이 유연성이 좋다.
* 메서드 이름상 boolean을 사용해야 가독성이 좋아지는 경우를 제외하고는 2개짜리 열거 타입을 쓰는게 의미를 명시하기 좋다.</br>
</br></br>

## 다중정의는 신중히 사용하라
재정의한 메서드는 동적으로 선택(런타임 타입의 함수)되고, 다중 정의한 메서드는 정적(컴파일타임 타입의 함수)으로 선택된다.</br>
다중정의는 프로그래머가 기대한 대로 동작하지 않을 가능성이 있으니 혼동을 일으키는 상황을 피해야 한다.</br>
안전하고 보수적으로 가려면 매개변수 수가 같은 다중정의는 만들지 말자.</br>
메서드를 다중정의할 때, 서로 다른 함수형 인터페이스라도 같은 위치의 인수로 받아서는 안 된다.</br>
</br></br>

## 가변인수는 신중히 사용하라
인수가 1개 이상이여야할 때는 그냥 매개변수 하나를 받고 가변인수를 추가로 받으면 1개 이상의 가변 인수를 받는 것과 같은 효과를 볼 수 있다.</br>

```
static int min(int firstArg, int... remainingArgs) {
  int min = firstArg;
  for(int arg : remainingArgs) {
    if(arg < min)
      min = arg;
  }
  return min;
}
```
</br></br>

## null이 아닌, 빈 컬렉션이나 배열을 반환하라
아래와 같이 빈 컬렉션이 아닌 null을 넘기는 것은 함수를 호출하는 곳에서 별도 null처리를 해야해서 좋은 코드가 아니다.

```
private final List<Cheese> cheesesInStock = ...;

public List<Cheese> getCheeses() {
  return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
```
</br></br>

## 옵셔널 반환은 신중히 하라
컬렉션, 스트림, 배열, 옵셔널 같은 컨테이너 타입은 옵셔널로 감싸지 말고 빈 컨테이너를 반환하는 것이 클라이언트 쪽에서 옵셔널 처리해야하는 것을 줄여주므로 좋다.</br>
결과가 없을 수 있으며, 클라이언트가 이 상황을 특별하게 처리해야 한다면 Optional<T>를 반환한다.</br>
기본 타입의 경우 OptionalInt, OptionalLong, OptionalDouble을 사용해서 박싱된 기본 타입을 담은 옵셔널을 반환하는 일이 없도록 해야 한다.</br>
</br></br>

## 공개된 API 요소에는 항상 문서화 주석을 작성하라
API를 올바로 문서화하려면 공개된 모든 클래스, 인터페이스, 메서드, 필드 선어에 문서화 주석을 달아라.</br>
메서드용 문서화 주석에는 해당 메서드와 클라이언트 사이의 규약을 명료하게 기술해야 한다.</br>
`{@code}`를 사용하면 코드용 폰트로 랜더링 되고 괄호안에 있는 html이나 다른 자바독 태그를 무시하고 그대로 출력한다.</br>
`{@literal}`도 `{@code}`와 비슷하지만 코드 폰트로 랜더링은 해주지 않는다.</br>
한 클래스 또는 인터페이스 안에서 요약 설명이 똑같은 멤버(혹은 생성자)가 둘 이상이면 안 된다.</br>
`{@index}`를 태그를 이용해 중요한 문구를 색인으로 만들 수 있다.</br>
제너릭 타입이나 제너릭 메서드를 문서화할 때는 모든 타입 매개변수에 주석을 달아야 한다.</br>
열거 타입을 문서화할 때는 상수들에도 주석을 달아야 한다.</br>
어노테이션 타입을 문서화할 때는 멤버들에도 모두 주석을 달아야 한다.</br>
클래스 혹은 정적 메서드가 스레드 안전하든 그렇지 않든, 스레드 안전 수준을 반드시 API 설명에 포함해야 한다.</br>
</br></br></br></br>
