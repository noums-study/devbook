# 10장 예외
## 예외는 진짜 예외 상황에만 사용하라
예외는 오직 예외 상황에서만 써야 하며 절대로 일상적인 제어 흐름용으로 쓰여선 안 된다.</br>
잘 설계된 API라면 클라이언트가 정상적인 제어 흐름에서 예외를 사용할 일이 없게 해야 한다.</br>
</br></br>

## 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라
구현하는 비검사 throwable은 모두 RuntimeException의 하위 클래스여야 한다.</br>
* 검사 예외: Exception 하위 클래스 중 RuntimeException 및 상속 받은 클래스가 아닌 것으로 컴파일러 단계에서 컴파일러에 의해 검사되고 걸러진다.</br>
</br></br>

## 필요 없는 검사 예외 사용은 피하라
검사 예외를 남용하면 API를 사용하는 쪽에서 try-catch문을 통해 에러 처리를 해야하니 부담을 줄 수 있다.</br>
</br></br>

## 표쥰 예외를 사용하라
Exception, RuntimeException, Throwable, Error는 직접 재사용하지 말자.</br>
인수 값이 무엇이든 어차피 실패했을 거라면 IllegalStateException을, 그렇지 않으면 IllegalArgumentException을 던지자.</br>
</br></br>

## 추상화 수준에 맞는 예외를 던지라
상위 계층에서는 저수준 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔 던져야 한다.</br>

```
try {
  return i.next();
} catch (NoSuchElementException e) {
  throw new IndexOutOfBoundException("인덱스: " + index);
}
```

예외 연쇄를 이용하면 상위 계층에는 맥락에 어울리는 고수준 예외를 던지면서 근본 원인도 함꼐 알려주어 오류를 분석하기에 좋다.</br>
단 무턱대고 예외를 전파(예외 연쇄)하는 것보다야 예외 번역이 우수한 방법이지만, 그렇다고 남용해서는 안 된다.</br>

```
// 예외 전파 예
try {
  ...
} catch (LowerLevelException cause) {
  throw new HigherLevelException(cause);
}
```

</br></br>

## 메서드가 던지는 모든 예외를 문서화하라
검사 예외는 항상 따로따로 선언하고, 각 예외가 발생하는 상황을 자바독의 @throws 태그를 사용하여 정확히 문서화하자.</br>
비검사 예외는 @throws 태그를 사용하되 검사 예외와 구분해서 메서드 선언의 throws 목록에 넣지 말자.</br>
한 클래스에 정의된 많은 메서드가 같은 이유로 같은 예외를 던진다면 그 예외를 각각의 메서드가 아닌 클래스 설명에 추가하는 것이 좋다.</br>
</br></br>

## 예외의 상세 메시지에 실패 관련 정보를 담으라
실패 순간을 포착하려면 발생한 예외에 관여된 모든 매개변수와 필드의 값을 실패 메시지에 담아야 한다.</br>
에러 메시지에 담을 필요한 정보를 아예 예외 생성자에서 받아 상세 메시지를 미리 생성해놓은 것은 메시지를 만드는 작업을 중복하지 않아도 되고 예외 클래스와 관련된 코드를 예외 클래스 안에 모아주는 효과가 있다.</br>
</br></br>

## 가능한 한 실패 원자적으로 만들라
호출된 메서드가 실패하더라도 해당 객체는 메서드 호출 전 상태를 유지해야 한다.</br>
</br></br>

## 예외를 무시하지 말라
예외를 무시하기로 했다면 catch 블록 안에 그렇게 결정한 이유를 주석으로 남기고 에외 변수의 이름도 ignored로 바꿔놓도록 하자.</br>

```
int numColors = 4;
try {
  numColors = f.get(1L, TimeUnit.SECONDS);
catch (TimeoutException | ExecutionException ignored) {
  // 기본 값을 사용한다
}
```
</br></br></br></br>
