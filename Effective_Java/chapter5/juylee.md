# 5장 제네릭
## 로 타입은 사용하지 말라
* 제너릭 타입(generic type): 제너릭 인터페이스, 제너릭 클래스</br>
* 로 타입(raw type): 제너릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때</br>

```
// 로 타입
private final Collection stamps = ...;

// 제너릭 타입에 타입 매개변수를 사용한 것
private final Collection<Stamp> = ...;
```

로 타입을 쓰면 제너릭이 안겨주는 안전성과 표현력을 모두 잃게 된다.</br>
List<Object> 같은 매개변수화 타입은 컴파일러에게 모든 타입을 허용한다고 명시를 하는 것이므로 List와 같은 로 타입을 사용해 안전성을 잃는 것과는 다르다.</br>
비한정적 와일드카드: 타입을 신경쓰고 싶지 않을 때 제너릭에 ? 명시 (예. Set<?>)</br>
비한정적 와일드카드를 이용해서 제너릭을 표현한 Collection<?>에는 어떤 원소도 넣을 수 없어서 타입 불변식을 훼손할 수 없다. 따라서 매개변수 등에 타입을 한정하고 싶지 않을 때 사용한다.</br>
class 리터컬에는 로 타입을 써야 한다.(예. List.class)</br>

```
// 로 타입으로 써도 좋은 예
if(o instanceof Set)
  Set<?> s = (Set<?>) o;
  ...
}
```
</br></br>

## 비검사 경고를 제거하라
경고를 제거할 수는 없지만 타입이 안전하다고 확신할 수 있다면 @SuppressWarnings("unchecked") 어노테이션을 달아 경고를 숨기자.</br>
@SupressWarnings는 가능한한 좁은 범위에 적용하자.</br>
@SupressWarning("unchekced") 어노테이션을 사용할 때면 그 경고를 무시해도 안전한 이유를 항상 주석으로 남겨야 한다.
</br></br>

## 배열보다는 리스트를 사용하라
```
Object [] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."; // 런타임 에러가 발생한다.

List<Object> ol = new ArrayList<Long>(); // 컴파일 시점에서 에러가 발생한다.
ol.add("타입이 달라 넣을 수 없다.");
```

* 공변: Super[]에 Sub[] 할당 가능, 즉 서로 다른 타입이 상속 관계일 때 자동 변환</br>
* 불공변: `List<Super>`에 `List<Sub>` 할당 불가, 서로 다른 타입이 상속 관계일지라도 자동 변환 불가</br>
* 실체불가 타입: `E, List<E>, List<String>` 같은 타입으로 실체화되지 않아서 런타임에는 컴파일 타임보다 타입 정보를 적게 가진다.(제너릭은 런타임시 제거되므로)</br>
</br>
비검사 형변환 경고를 제거하려면 배열 대신 리스트를 쓰면 된다.</br>

```
T[] choiceArray1 = (T[]) choices.toArray(); // 비검사 형변환 에러: T extends Object declared in class Chooser
List<T> chocieArray2 = new ArrayList<>(choices);
```
</br></br>

## 이왕이면 제너릭 타입으로 만들라
E와 같은 정규 타입 매개변수는 실체화 불가 타입이므로 배열로 생성할 수 없다.</br>

```
E[] = new E[10]; // error!
```

제너릭 배열을 사용할 경우 배열의 런타임 타입과 컴파일 타입이 달라 힙 오염(heap pollution)을 일으킨다.</br>

```
// 런타임에서는 E, 컴파일 때는 E에 넣은 타입
E[] elements = (E[]) new Object[16];
```
</br></br>

## 이왕이면 제너릭 메서드로 만들라
```
public static <E> Set<E> union(Set<E> s1, Set<E>s2) {
  ...
}
```
* 제너릭 싱글톤 팩토리: 제너릭으로 타입 설정 가능한 인스턴스를 만들어두고 한봔 시에 제너릭으로 받은 타입을 이용해 타입을 지정하는 것</br>
* 항등함수(identity function): 입력 값을 수정 없이 그대로 반환하는 함수</br>

```
// 제너릭 싱글톤 예
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public sttic <T> UnaryOperator<T> identityFuntion() {
  return (UnarOperator<T>) IDENTITY_FN;
}
```

재귀적 타입 한정(recursive type bound): 자기 자신이 들어간 표현식을 사용하여 타입 매개변수 허용 범위를 한정하는 것</br>

```
public static <E extends Comparable<E>> E max(Collection<E> c);
```
</br></br>

## 한정적 와일드카드를 사용해 API 유연성을 높이라
유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라.</br>

```
public void pushAll(Iterable<? extends E> src) {
  for(E e : src)
    push(e);
}

public void popAll(Collection<? super E> dst) {
  while(!isEmpty())
    dst.add(pop());
}
```

* 팩스(PECS): producer-extends, consumer-super</br>
   -> 생상자면 `<? extends T>`를 사용하고 소비자이면 `<? super T>`를 사용하라(겟풋 원칙(Get and Put Principle)이라고도 한다.)</br>
</br>

반환 타입에는 한정적 와일드카드를 사용하면 안 된다.</br>
클래스 사용자가 와일드카드 타입을 신경 써야 한다면 그 API에 무슨 문제가 있을 가능성이 크다.</br>

```
// <Number>를 Set의 목표 타입(target typing)이라고 한다. 자바 8부터 지원한다.
Set<Number> numbers = union(integers, doubles);

// 자바 7까지는 목표 타입을 지원하지 않아서 아래와 같이 작성해야 한다.
Set<Number> numbers = Union.<Number>union(integers, doubles);
```

메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라.</br>
`List<?>`와 같은 와일드 카드를 쓰면 그 안에 null 외는 어떤 값도 넣을 수 없으므로 `<?>`로 매개변수를 받아서 사용할 경우 실제 타입을 명시해주는 helper가 필요하다.</br>

```
public static void swap(List<?> list, int i, int j) {
  swapHelper(list, i, j);
}

private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```
</br></br>

## 제너릭과 가변인수를 함께 쓸 때는 신중하라
제너릭 vararges 배열 매개변수에 값을 저장하는 것은 안전하지 않다.</br>

```
static void dangerous(List<String>... stringLists) {
  List<Integer> intList = List.of(42);
  Object[] objects = stringLists;
  objects[0] = intList; // 힙 오염 발생
  String s = stringLists[0].get(0); // ClassCastException error!
}
```

그러나 제너릭 varages 배열 매개변수를 사용하는 것은 유용할 수도 있어서 자바 라이브러리에서 이러한 메소드들을 제공한다.</br>

```
Arrays.asList(T... a)
Collections.addAll(Collection<? super T> c, T... elements)
```

@SafeVarargs 어노테이션은 메서드 작성자가 그 메서드가 타입 안전함을 보장하는 장치다.</br>
varargs 매개변수 배열이 호출자로부터 그 메서드들로 순수하게 인수들을 전달하는 역할만 한다면 안전하다.</br>
제너릭 varargs 매개변수 배열에 다른 메서드가 접근하도록 허용하면 안전하지 않다.</br>

```
public static void main(String[] args) {
  String[] attributes = pickTow("좋은", "빠른", "저렴한"); // ClassCastException error!!
}

static <T> T[] pickTwo(T a, T b, T c) {
  switch(ThreadLocalRandom.current().nextInt(3)) {
    case 0: return toArray(a, b);
    ...
  }
  throw new AssertionError(); // 도달할 수 없는 에러
}
```
varargs 매개변수 배열에 아무것도 저장하지 않으며 그 배열(혹은 복제볼)을 신뢰할 수 없는 코드에 노출하지 않는 경우는 안전하므로 제너릭이나 매개변수화 타입의 varargs 매개변수를 받는 모든 메서드에 @SafeVarargs를 달라.</br>
또한 @SafeVarargs는 재정의할 수 없는 메서드에만 달아야 한다.</br>

```
public static void main(String[] args) {
  List<String> attributes = pickTwo("좋은", "빠른", "저렴한");
}

static <T> List<T> pickTwo(T a, T b, T c) {
  switch(ThreadLocalRandom.current().nextInt(3)) {
    case 0: return List.of(a, b); // toArray의 List버전 자바 9부터 지원
    ...
  }
  throw new AssertionError();
}
```
</br></br>
