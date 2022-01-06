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
