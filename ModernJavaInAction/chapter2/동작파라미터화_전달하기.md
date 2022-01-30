# 1. 변화하는 요구사항에 대응하기

동작 파라미터화(behavior parameterization)을 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다.

동작파라미터화란?

: 아직 어떻게 실용할 것인지 결정하지 않은 코드 블럭

예시 )

- 리스트의 모든 요소에 대해서 '어떤 동작'을 수행할 수 있다.
- 리스트 관련 작업을 끝낸 다음에 '어떤 다른 동작'을 수행할 수 있다.
- 에러가 발생하면 '정해진 어떤 다른 동작'을 수행할 수 있다.

1) 비슷한 코드가 반복 존재한다면 그 코드를 추상화해야한다.

2) 필터링을 할 때 플래그와 같은 조건을 사용하는 것은 좋지 않다. 

ex) 색, 무게에 따라 사과를 필터링하는 메서드를 만들었다고 가정했을 때, 여기에 출하지 등과 같이 또 다른 조건이 더해진다면? 결국 모든것을 다 if문으로 해결하는 거대한 메서드가 만들어질 것이다. 

→ **동작 파라미터화**를 이용해서 변화하는 요구사항에 대한 유연성을 얻어야 한다.

<br/>
<br/>
<br/>

# 2. 동작 파라미터화

### 전략 디자인 패턴

: 전략이라 불리우는 각 알고리즘을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음, 런타임에 알고리즘을 선택하는 기법. 

알고리즘 패밀리 

→ 사과 선택하는 전략을 캡슐화

```java
public interface ApplePredicate {
    boolean test(Apple apple) ;
}
```

전략

```java
public class AppleGreenColorPredicate implements ApplePredicate{
    @Override
    public boolean test(Apple apple) {
        return "GREEN".equals(apple.getColor());
    }
}

public class AppleHeavyWeightPredicate implements ApplePredicate{
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
}
```

⇒ 메서드가 다양한 동작을 받아서 내부적으로 다양한 동작을 수행할 수 있게끔 하는것을 동작파라미터화 라고 한다. 

<br/>
<br/>

### 추상적 조건으로 필터링 하기

코드/동작을 전달하기 

```java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p){
        List<Apple> result = new ArrayList<>();
        for (Apple apple : inventory){
            if (p.test(apple)){
                result.add(apple);
            }
        }
        return result;
    }
```

```java
List<Apple> greenApples = filterApples(inventory, new AppleGreenColorPredicate());
List<Apple> heavyApples = filterApples(inventory, new AppleHeavyWeightPredicate());
```

파라미터로 전달한 Predicate 객체에 따라서 filterApples의 동작이 결정된다. 즉 filterApples의 동작을 파라미터화 하였다. 

하지만 동작 조건/전략이 추가될 때마다 매번 Predicate 객체를 생성해주면 번거롭다. 

람다를 이용해서 여러개의 Predicate 객체 정의 없이 여러 동작을 메서드로 전달하는 방법을 알아보자. 


<br/>
<br/>
<br/>


# 3.복잡한 과정 간소화

### 익명클래스 사용

익명클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 할 수 있음. 즉석에서 필요한 구현 만들어서 사용. 

하지만 익명클래스도 부족한 점이 있음. 

- 여전히 많은 코드 공간을 차지
- 많은 프로그래머들이 익명클래스 사용에 익숙치 않음
- 코드가 장황해짐

<br/>
<br/>


### 람다 표현식 사용

```java
List<Apple> greenApples = filterApples(inventory, (Apple a) -> "GREEN".equals(a.getColor()));
```

람다식으로 코드가 복잡성 해결 

<br/>
<br/>

### 리스트 형식으로 추상화 하기

```java
public interface Predicate<T> {
    boolean test(T t);
}
```

```java
/*리스트 형식으로 추상화*/
    public static <T> List<T> filter(List<T> list, Predicate<T> p){
        List<T> result = new ArrayList<>();
        for (T e:list){
            if (p.test(e)){
                result.add(e);
            }
        }
        return result;
    }
```

```java
List<Apple> redApples = filter(inventory, (Apple a)->"RED".equals(a.getColor()));
```

<br/>
<br/>
<br/>

# 4. 그 외 코드 전달 개념 예제

### Comparator

java.util.Comparator 객체를 이용해서 sort의 동작을 파라미터화 할 수 있다. 

```java
public interface Comparator<T>{
	int compare(T o1, T o2)
}
```

- 익명클래스를 이용해서 무게가 적은 순서로 목록에서 사과를 정렬

```java
inventory.sort(new Comparator<Apple>() {
    @Override
    public int compare(Apple o1, Apple o2) {
        return o1.getWeight().compareTo(o2.getWeight());
    }
});
```

- 람다표현식으로 구현

```java
inventory.sort((Apple a1, Apple a2)-> a1.getWeight().compareTo(a2.getWeight()));
```

<br/>
<br/>

### Runnable

Runnable을 사용해서 병렬로 코드 블록을 실행하기. 

- 익명클래스로 구현

```java
Thread t = new Thread(new Runnable(){
	public void run(){
		System.out.println("Hello World");
	}
})
```

- 람다표현식으로 구현

```java
Thread t = new Thread(()-> System.out.println("Hello world"));
```