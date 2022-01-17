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
