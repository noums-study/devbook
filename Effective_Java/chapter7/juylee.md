# 7장 람다와 스트림
## 익명 클래스보다는 람다를 사용하라
타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략하자.</br>

```
Collections.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length());
```

```
public enum Operation {
  PLUS("+", (x, y) -> x + y;),
  MINUS("-", (x, y) -> x - y;),
  ...
  
  private final String symbol;
  private final DoubleBinaryOperator op;
  
  ...
  public double apply(double x, double y) {
    return op.applyAsDouble(x, y);
  }
}
```

람다는 이름이 없고 문서화도 못 하므로 코드 자체로 동작히 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다.</br>
람다에서의 this는 바깥 인스턴스를 가리킨다.</br>
람다를 직렬화하는 일은 극히 삼가야 한다.</br>
</br></br>

## 람다보다는 메서드 참조를 사용하라
```
// 람다로 표현
// key 값이 없다면 1 저장, 있다면 현재 값인 count에 1 값인 incr를 더해 덮어쓰지
map.merge(key, 1, (count, incr) -> count + incr);

// 함수 참조로 표현
map.merge(key, 1, Integer::sum);
```
* 정적 메서드 참조</br>
  ```
  Integer::parseInt
  
  str -> Integer.parseInt(str)
  ```
* 한정적(bound) 인스턴스 메서드 참조</br>
  ```
  Instant.now()::isAfter
  
  Instant then = Instant.now();
  t -> then.isAfter();
  ```
* 비한정적(unbound) 인스턴스 메서드 참조</br>
  ```
  String::toLowerCase
  
  str -> str.toLowerCase()
  ```
* 클래스 생성자 메서드 참조 유형
  ```
  TreeMap<K, V>::new
  
  () -> new TreeMap<K, V>()
  ```
* 배열 생성자 메서드 참조 유형
  ```
  int[]::new
  
  len -> new int[len]
  ```
</br></br>

## 표쥰 함수형 인터페이스를 사용하라
필요한 용도에 맞는 게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라.</br>
BinaryOperator와 같은 함수형 인터페이스가 java.util.function에 존재한다.</br>
기본 함수형 인터페이스에 박싱된 기본 타입을 넣어 사용하는 것은 계산량이 많을 때 성능이 느려질 수 있으니 사용하지 말자.</br>
직접 만든 함수형 인터페이스에는 항상 @FuntionalInterface 어노테이션을 사용해서 추상 메서드를 하나만 가지게 하고(컴파일타임에 잡아준다.) 해당 인터페이스가 람다용으로 설계된 것임을 명시하는 것이 좋다.</br>
</br></br>

## 스트림은 주의해서 사용하라
스트림을 과용하면 프로그램이 읽거나 유지보수하기 어려워진다.</br>
람다에서는 타입 이름을 자주 생략하므로 매개변수 이름을 잘 지어야 스트림 파이프라인의 가독성이 유지된다.</br>
도우미 메서드를 적절히 활용하는 일의 중요성은 일반 반복 코드에서보다는 스트림 파이프라인에서 훨씬 크다.</br>
char스트림은 지원하지 않으므로 char 값들을 처리할 때는 스트림을 삼가는 편이 좋다.</br>
</br></br>

## 스트림에서는 부작용 없는 함수를 사용하라
forEach 연산은 스트림 계산 결과를 보고할 때만 사용하고 계산하는데 쓰지 말자.</br>

```
// 스트림을 잘못 사용한 예
// 스트림 외부에 있는 freq 값을 참조하고 수정한다.
Map<String, Long> freq = new HashMapM<();
try (Stream<String> words = new Scanner(file).tokens()) {
  words.forEach(word -> {
    freq.merge(word.toLowerCase(), 1L, Long::sum);
  });
}
```

```
// 스트림을 제대로 사용한 예
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
  freq = words.collect(Collectors.groupingBy(String::toLowerCase, counting()));
}
```

* toMap</br>
  
  ```
  private static final Map<String, Operation> stringToEnum = Stream.of(values()).collect(toMap(Object::toString, e -> e));
  
  // 음악가와 그 음악가의 판매량이 제일 많은 앨범으로 map 생성
  Map<Artist, Album> topHits = albums.collect(toMap(Album::artist, a -> a, maxBy(comparing(Album::sales))));
  
  // 마지막 쓴 값 사용
  toMap(keyMapper, valueMapper, (oldVal, newVal) -> newVal);
  ```
</br></br>

## 반환 타입으로는 스트림보다는 컬렉션이 낫다
```
// Stream <-> Iterable 어댑터
// Stream<E> -> Iterable<E>
public static <E> Iterable<E> iterableOf(Stream<E> stream) {
  return stream::iterator;
}

// Iterable<E> -> Stream<E>
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
  return StreamSupport.stream(iterable.spliterator(), false);
}
```

어댑터 코드를 사용하면 구현하고자 하는 상황에 맞게 Stream, Iterator를 변환해 사용할 수 있지만 코드를 어수선하게 만들고 성능도 좋아지지 않는다.</br>
Collection은 Iterable의 하위 타입이고 stream 메서드도 제공하므로 원소 시퀀스를 반환하는 공개 API의 반환 타입에는 Collection이나 그 하위 타입을 쓰는게 좋다.</br>
하지만 덩치 큰 시퀀스 또한 쓸데없이 컬렉션으로 반환해 메모리를 낭비해서는 안된다.</br>
</br></br>

## 스트림 병렬화는 주의해서 적용하라
데이터 소스가 Stream.iterate거나 중간 연산으로 limit를 쓰면 파이프라인 병렬화로는 성능 개선을 기대할 수 없다.</br>
대체로 스트림의 소삭 ArrayList, HashMap, HashSet, ConcurrentHashMap의 인스턴스거나 배열, int 범위, long 범위일 때 병렬화의 효과가 가장 좋다.</br>

```
// 병렬화에 적합한 예
static long pi(long n) {
  return LongStream.rangeClosed(2, n)
                  .parallel()
                  .mapToObj(BigInteger::valueOf)
                  .filter(i -> i.isProbablePrime(50))
                  .count();
}
```

스트림 병렬화는 계산도 올바르게 수행하고 성능이 빠라질 거라는 확신이 있을 때 운영 환경과 유사한 환경에서 테스트해보고 반영하는 것이 좋다.</br>
그렇지 않을 경우 오작동하거나 오히려 프로그램의 성능을 급격히 떨어뜨릴 수 있다.</br>
</br></br></br></br>
