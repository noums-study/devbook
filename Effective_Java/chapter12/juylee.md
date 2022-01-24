# 12장 직렬화
## 자바 직렬화의 대안을 찾으라
신뢰할 수 없는 스트림을 역직렬화하면 원격 코드 실행(remote code execution, RCE), 서비스 거부(denial-of-service, DoS) 등의 공격으로 이어질 수 있다.</br>
직렬화 위험을 회피하는 가장 좋은 방법은 아무것도 역직렬화하지 않는 것이다.</br>
직렬화 대신 크로스 플랫폼 구조화된 데이터 표현(속성-값 쌍의 집합으로 구성되고 간단한 구조화된 데이터 객체)인 JSON, 포로토콜 버퍼(Protocol Buffers, protobuf)을 사용하자.</br>
직렬화를 피할 수 없고 역직렬화한 데이터가 안전한지 확신할 수 없다면 역직렬화 필터링(java.io.ObjectInputFilter)을 사용하자.</br>
</br></br>

## Serializable을 구현할지는 신중히 결정하라
Serializable을 구현하면 직렬화된 바이트 스트림을 계속 지원해야하므로 릴리즈한 뒤에 수정하기 어렵다.</br>
또한 이는 클래스의 private, package-private 인스턴스 필드들마저 API로 공개되는 꼴이 되므로 캡슐화가 깨진다.</br>
Serializable 구현은 불변식 깨짐과 허가되지 않은 접근에 쉽게 노출되는 등 버그와 보안 구멍이 생길 위험이 높아진다.</br>
직렬화 가능 클래스가 수정되면 신버전 인스턴스를 직렬화한 후 구버전으로 역질렬화 할 수 있는지, 그리고 그 반대도 가능한지 테스트해야 한다.</br>
상속용으로 설계된 클래스는 대부분 Serializable을 구현하면 안 되며, 인터페이스도 대부분 Serializable을 확장해서는 안 된다. 안 그럼 해당 클래스와 인터페이스를 구현하는 쪽에서 큰 부담을 지게 된다.</br>
inner 클래스의 경우는 기본 직렬화 형태가 분명하지 않으므로 직렬화를 구현하지 말아야 한다. 단, 정적 멤버 클래스는 바깥 인스턴스의 변수 값을 사용하기 위한 필드를 컴파일러가 자동 추가하지 않으므로 Serializable을 구현해도 무방하다.</br>
</br></br>

## 커스텀 직렬화 형태를 고려해보라
@serialData 태그는 자바독 유틸리티에게 이 내용을 직렬화 형태 페이지에 추가하도록 요청하는 역할을 한다.</br>
해당 객체의 논리적 상태와 무관한 필드(직렬화 되지 말아야 하는 필드)라고 확신할 때만 transient 한정자를 생략해야 한다.</br>
transient로 선언하지 않은 모든 인스턴스 필드는 defulatWriteObject() 메서드를 호출하면 모두 직렬화된다.</br>
기본 직렬화를 사용한다면 transient 필드들은 역직렬화될 때 기본 값으로 초기화 된다.</br>
기본 값으로 사용하면 안 되면 readObject 메서드에서 defaultReadObject를 호출한 다음 해당 필드를 원하는 필드로 설정하자.</br>
모든 메서드를 synchronized로 선언하여 스레드 안전하게 만든 객체에서 기본 직렬화를 사용하려면 writeObject도 synchronized로 선언해야 하다.</br>

```
private synchronized void writeObject(ObjectOutputStream a) throws IOException {
  ...
}
```

어떤 직렬화 형태를 택하든 직렬화 가능 클래스 모두에 직렬 버전 UID를 명시하면 직렬 버전 UID가 일으키는 잠재적인 호환성 문제가 사라진다.</br>
또한 UID를 명시하면 런타임 때 값을 생성하느라 복잡한 연산을 수행하지 않아도 되서 속도가 더 빨라진다.</br>
구버전으로 직렬화된 인스턴스들과 호환성을 끊으려는 경우를 제외하고는 직렬 버전 UID를 절대 수정하지 말아야 한다. 안 그러면 역직렬화할 때 InvalidClassException이 발생한다.</br>
</br></br>

## readObject 메서드는 방어적으로 작성하라
readOjbect는 실질적으로 또 다른 public 생성자이기 때문에 생성자와 똑같은 수준으로 주의를 기울여야한다. 안 러면 공격자가 클래스의 불변식을 깨뜨릴 수 있다.</br>
readObject는 바이트 스트림이 진짜 직렬화된 인스턴스라고 가정하고 역직렬화를 하면 안 된다.</br>
readObject는 어떤 바이트 스트림이 넘어오더라도 유효한 인스턴스를 만들어내도록 주의를 기울여야 한다.</br>
private이어야 하는 객체 참조 필드는 외부에서 역직렬화할 때 참조해서 불변식을 깨뜨리고 마음대로 값을 변경할 수 있으므로 각 필드가 가리키는 객체를 방어적으로 복사해서 사용하는 것이 좋다. 특히 가변 요소라면 더더욱 복사해서 사용하는 것이 좋다.</br>
</br></br>

## 인스턴스 수를 통제해야 한다면 readResolve보다는 열거 타입을 사용하라
readResolve는 역직렬화 시 만들어진 객체를 인수로 받아서 안에 정의된 코드를 수행해 Object를 반환한다.</br>

```
private Object readResolve() {
  // 싱글톤으로 만들어진 인스턴스의 경우는 이와 같이 역직렬화해 새로운 인스턴스가 만들어지는 것을 막을 수 있다.
  return INSTANCE;
}
```

readResolve를 인스턴스 통제 목적으로 사용한다면 객체 참조 타임 필드는 모두 transient로 선언해 직렬화할 때 쓸데 없이 실 데이터를 가져 공격자에게 이용되는 것을 막는 것이 좋다.</br>
</br></br>

## 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라
```
private static class SerializationProxy implements Serializable {
  private final Date start;
  private final Date end;
  
  SerializationProxy(Period p) {
    this.start = p.start;
    this.end = p.end;
  }
  
  private static final long serialiVersionUID = ...;
}
```

직렬화 프록시를 사용할 클래스도 Serializable을 구현하고 writeReplace 메서드를 구현한다.</br>

```
private Object writeReplace() {
  return new SerializationProxy(this);
}
```

writeReplace 메서드는 직렬화할 때 클래스(Period)의 인스턴스가 아니라 직렬화 프록시(SerializableProxy)의 인스턴스를 반환하도록 하는 역할을 한다.</br>
직렬화 프록시를 이용해서 직렬화를 수행하고 공격자가 불변식을 훼손하는 것을 막기 위해 아래와 같이 직렬화 프록시 없이는 역직렬화가 불가하게 만들 수 있다.</br>

```
private void readObject(ObjectInputStream stream) throw InvalidObjectException {
  throw new InvalidObjectException("프록시가 필요합니다.");
}
```

클래스와 논리적으로 동일한 인스턴스를 반환하는 readResolve 메서드를 직렬화 프록시(SerializableProxy)에 추가해서 역직렬화가 가능하게 만든다.</br>
이와 같이 역직렬화를 구성할 경우 직렬화 프록시에서 클래스의 생성자를 호출할 수 밖에 없으므로 불변식을 또 검사할 필요가 없다.(기존 클래스에서 불변식 검사를 하므로)</br>
단, 직렬화 프록시는 직렬화 프록시를 사용하지 않았을 때보다 성능이 안 좋다.</br>
</br></br></br></br>
