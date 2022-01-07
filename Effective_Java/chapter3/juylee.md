# 3장 모든 객체의 공통 메서드
## equals는 일반 규약을 지켜 재정의하라
<i>equals를 재정의 하지 않아야하는 경우</i>
</br>
* 각 인스턴스가 본질적으로 고유하다.
* 인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없다.
* 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
* 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
  
  ```
  @Override
  public boolean equals(Object o) {
    throw new AssertionError(); // equals가 잘못 호출되는 것을 방지한다.
  }
  ```

* Enum과 같이 인스턴스가 둘 이상 만들어지지 않음을 보장하는 클래스는 equals를 재정의 하지 않아도 된다.
</br>

<i>equals 메서드 재정의 규약</i>
</br>
* 빈시상(reflexivity): null이 아닌 모든 참조 값 x에 대해 x.equals(x)는 true다.
* 대칭성(symmetry): null이 아닌 모든 참조 값 x, y에 대해, x.equals(y) = true이면 y.equals(x) = true이다.
* 추이성(transitivity): null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y) = true이고 y.equals(z) = true이면 x.equals(z) = true이다.
* 일관성(consistency): null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)는 항상 true거나 false이다.
* null 아님: null이 아닌 모든 참조 값 x에 대해 x.equals(null) = false이다.
</br>

<i>양질의 equals 메서드 구현 방법</i>
</br>
* == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
* instanceof 연산자로 입력이 올바른 타입인지 확인한다.
* 입력을 올바른 타입로 형변환한다.
* 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.
</br>

* float, double의 경우는 특수한 부동소수 값등을 다뤄야하지 때문에 compare()를 사용하는게 좋다.
* null을 정상 취급해서 사용하는 참조 타입 필드의 경우 Object.equals(Object, Object)를 사용해서 NullPointException 발생을 방지하는 것이 좋다
* equals를 재정의할 때 hashCode도 반드시 재정의하자.
* AutoValue: 구글에서 만든 프레임워크로 어노테이션 추가만 하면 equals를 작성하고 테스트하는 작업을 하지 않아도 된다.
</br></br>

## equals를 재정의하려거든 hashCode도 재정의하라
그렇지 않으면 hashCode 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제를 일으킬 것이다.</br>

```
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");
m.get(new PhoneNumber(707, 867, 5309); // return null
```

<i>hashCode 만드는 요령</i>
</br>
핵심필드의 equals를 수행할 때 사용하는 hashCode 값을 계산한다(null일 경우 0 사용)</br>

```
int c = 31;
int hashCode = Integer.hashCode(firstNumber);
hashCode = c * result + (fieldObject == null ? 0 : fieldObject.hashCode());
...
```

equals 비교에 사용되지 않은 필드의 경우는 hashCode 계산에서 제외한다.</br>
Object 객체의 hash 메소드를 이용하면 한 줄로 hash 값을 나타낼 수 있다.</br>

```
@Override public int hashCode() {
  return Objects.hash(lineNum, prefix, areaCode);
}
```

hashCode를 계산하는 비용이 클 경우 매번 계산하는 것은 성능에 안 좋으로 hashCode를 캐시하거나 hashCode가 처음 호출 될 때만 값을 만드는 방식이 좋다.</br>
-> 처음 호출 할 때만 값을 만드는 경우는 스레드 안전하게 만들도록 주의해야 한다.
</br></br>

## toString을 항상 재정의하라
toString을 제대로 재정의할 경우 에러 메시지에 쓰기 좋아 디버깅하기 쉽다.</br>
toString은 그 객체가 가진 주요 정보 모두를 반환하는게 좋다.</br>
구글의 AutoValue 프레임뭐크(어노테이션으로 사용)은 toString도 생성해준다.
</br></br>

## clone 재정의는 주의해서 진행하라
Cloneable을 구현한 클래스(implement Cloneable)의 인스턴스에서 clone을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환하며, 그렇지 않은 클래스의 인스턴스를 호출하면 CloneNotSupportedException을 반환한다.</br>
-> 생성자를 호출하지 않고도 객체를 생성할 수 있게 하는 것이므로 좋은 것은 아니다.</br>
clone()은 super.clone을 통해 인스턴스를 반환해야 한다. -> 안 그럼 하위 클래스에서 잘못된 클래스의 객체가 만들어진다.</br>

```
@Override 
public PhoneNumber clone() {
  try {
    return (PhoneNumber) super.clone();
   } catch (CloneNotSupportedException e) {
    throw new AssertionError(); // 일어날 수 없는 일이다.
   }
}
```

clone은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야한다.(복제본을 수정한다고 원본에 영향이 가면 안된다.)</br>
Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라'는 일반 용법과 충돌한다.(clone 할 때 final 필드에 clone 값을 넣을 수 없으므로)</br>
상속용 클래스는 Cloneable을 구현해서는 안 된다.</br>
복제 기능은 생성자(변환 생성자(conversion constructor))와 팩토리(변환 팩토리(conversion factory)를 이용하는게 좋다.</br>
단, 배열은 clone()을 사용하는게 깔끔하다.</br>

```
// 복사 생성자
public Yum(Yum yum) {...}

// 복사 팩토리
public static Yun newInstance(Yun yun) {...}
```
</br></br>

## Comparable을 구현할지 고려하라
compareTo를 수행해서 결과가 같다로 나오면 equals를 수행해도 똑같다는 결과가 나와야한다.</br>
compareTo 메서드에서 관계 연산자 <와 >를 사용하는 이전 방식은 거추장스럽고 오류를 유발하니, 이제는 compare를 이용하길 추천한다.</br>
비교자 생성 메서드를 사용해서 compareTo를 구현하면 코드가 간결해지만 성능이 저하된다.</br>

```
// 비교자 생성 메서드(Comparator.comparingInt)
private static final Comparator<PhoneNumber> COMPARATOR 
    = comparingInt((PhoneNumber pn) -> pn.areaCode) // return Comparator<PhoneNumber>
        .thenComparingInt(pn -> pn.prefix)
        .thenComparingInt(pn -> pn.lineNum);

public int compareTo(PhoneNumber pn) {
  return COMPARATOR.compare(this, pn);
}
```
</br></br></br></br>
