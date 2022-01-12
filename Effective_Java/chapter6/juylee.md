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
