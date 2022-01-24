# 11장 동시성
## 공유 중인 가변 데이터는 동기화해 사용하라
동기화는 배타적 실행뿐 아니라 스레드 사이의 안정적인 통신에 꼭 필요하다.</br>
즉, 한 스레드가 저장한 값이 다른 스레드에게 보이게 하기 위해서도 중요하다.</br>
volatile 필드는 항상 가장 최근에 기록된 값을 읽게 됨을 보장한다.</br>

```
private stativ bolatitle int nextSerialNumber = 0;

public static int generateSerialNumber() {
  return nextSerialNumber ++; 
  // ++는 필드에 접근해서 읽고 그 후에 1을 더한다.
  // 따라서 필드에 접근해서 읽고 1을 더하기 직전에 다른 스레드가 필드에 접근해서 값을 읽는다면 1을 더하기 전의 값을 읽는다.
```

되도록이면 가변 데이터는 단일 스레드에서만 쓰는 것이 좋다.</br>
</br></br>

## 과도한 동기화는 피하라
응답 불가와 안전 실패를 피하려면 동기화 메서드나 동기화 블록 안에서는 제어를 절대로 클라이언트에 양도하면 안 된다.</br>
즉, 동기화된 영역 안에서는 재정의할 수 있는 메서드는 호출하면 안 되며, 클라이언트가 넘겨준 함수 객체를 호출해서도 안 된다.</br>
기본 규칙은 동기화 영역에서는 가능한 한 일을 적게 하는 것이다.</br>
</br></br>

## 스레드보다는 실행자, 테스트, 스트림을 애용하라
```
// 테스크(runnable 또는 callable)를 직접 쓰레드로 실행하는 것이 아닌 ExcutorService라는 실행자를 통해 실행한다.
ExcutorService exec = Executors.newSingThreadExcutor();
exec.execute(runnable);

// exec.shutdown(); // 실행자 종료
```

</br></br>

## wait와 notify보다는 동시성 유틸리티를 애용하라
wait와 notify는 올바르게 사용하기가 아주 까다로우니 고수준 동시성 유틸리티를 사용하자.(현재는 wait, notify를 사용할 이유가 없다.)</br>
동시성 컬렉션에서 동시성을 무력화하는건 불가능하며, 외부에서 락을 추가로 사용하면 오리혀 속도가 느려진다.</br>
Collections.synchronizedMap보다는 ConcurrentHashMap을 사용하는게 월씬 좋다.</br>
wait 메서드를 사용할 때는 반드시 대기 반복문(wait loop) 관용구를 사용하고 반복문 밖에서는 절대로 호출하지 말자.</br>
</br></br>

## 스레드 안전성 수준을 문서화하라
무조건적 스레드 안전 클래스를 작성할 때는 DoS 공격을 막기 위해서 클라이언트에서 동기화에 관려할 수 없도록 비공개 락 객체를 사용하는 것이 좋다.</br>

```
private final Object lock = new Object();

public void foo() {
  synchronized(lock) {
    ...
  }
}
```

</br></br>

## 지연 초기화는 신중히 사용하라
대부분의 상황에서 일반적인 초기화가 지연 초기화보다 낫다.</br>
성능 때문에 정적 필드를 지연 초기화해야 한다면 지연 초기화 홀더 클래스(lazy initialization holder class) 관용구를 사용하자.</br>

```
private static class FieldHolder {
  static final FieldType field = computeFieldValue();
}

private static FieldType getField() { return FieldHolder.field; // FieldHolder 필드 초기화 }
```

성능 때문에 인스턴스 필드를 지연 초기화해야 한다면 이중검사(double check) 관용구를 사용하라.</br>

```
private volatile FieldType field;

private FieldType getField() {
  FieldType result = field;
  if(result != null ) {
    return result
    
    synchronized(this) {
      if(field == null) {
        field = computerFieldValud();
      }
      return field;
    }
  }
}
```

</br></br>

## 프로그램의 동작을 스레드 스케줄러에 기대지 말라
정확성이나 성능이 스레드 스케줄러에 따라 달라지는 프로그램이라면 다른 플랫폼에 이식하기 어렵다.</br>
Thread.yield를 사용해서 성능을 개선하는 것은 다른 JVM에서는 오히려 느려질 수 있으며 테스트할 수단이 없으니 되도록 하지 말자.(같은 맥락으로 스레드의 우선순위를 변경해서 성능 개선하는 것 또한 좋지 않다,)</br>
</br></br></br></br>
