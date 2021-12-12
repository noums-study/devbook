# Chapter_1) 자바 8,9,10,11 : 무슨 일이 일어나고 있는가?

자바 8 이전의 프로그램은 CPU 코어 중 하나만을 사용하였음. 나머지는 idle 상태로 두거나, 운영체제나 바이러스 검사 프로그램과 프로세스 파워를 나눠서 사용하였음. 

나머지 코어를 활용하기 위해 스레드를 사용하였다. 하지만 스레드를 사용하면 관리가 어렵고 많은 문제가 발생할 수 있음.

자바8에서는 병렬 실행을 새롭고 더 쉽게 접근할 수 있는 방법을 제공한다. 

**자바8에서 새롭게 제공하는 기술**

- 스트림 API
- 메서드에 코드를 전달하는 기법
- 인터페이스의 디폴트 메소드

<br/>
<br/>

## 1. 왜 아직도 자바는 변화하는가?

> **프로그래밍 언어 생태계에서 자바의 위치**
> 

자바가 대중적 프로그래밍 언어로 성장하게 된 이유 

- 캡슐화
- 객체지향

그러나 언어 생태계가 변화하면서 병렬 프로세싱을 활용해야하는 상황에 자바가 충분히 대응하지 못했음. 

자바8은 더 다양한 프로그래밍 도구와 프로그래밍 문제를 더 빠르고 정확하며 쉽게 유지보수할 수 있다는 장점을 제공한다. 

> **스트림 처리**
> 

스트림이란? 

→ 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임.

자바8에는 `java.util.stream` 패키지에 스트림API가 추가되었음. 스트림API는 파이프라인을 만드는데 필요한 메서드를 제공한다. 

스트림API의 핵심

- 하려는 작업을 추상화해서 일련의 스트림으로 만들어 처리
- 스트림 파이프라인을 이용해서 입력 부분을 여러 CPU코어에 쉽게 할당할 수 있음.
- 스레드라는 복잡한 작업을 사용하지 않으면서 공짜로 병렬성을 얻을 수 있다.

> **동작 파라미터화로 메서드에 코드 전달하기**
> 

동작 파라미터화란?

→ 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달하는 것 

→ 자바 8이전에는 메서드를 다른 메서드로 전달할 방법이 없었음. 자바8 이후부터는 코드를 직접 파라미터화할 수 있게 되었다. 

> **병렬성과 공유 가변 데이터**
> 

병렬성을 얻는 대신 포기해야하는 것 ? 

→ 스트림 메서드로 전달하는 코드의 동작 방식을 바꿔야함. 

→ 스트림 메서드로 전달하는 코드는 다른 코드와 동시에 실행하더라도 안전하게 실행될 수 있어야 함 

→ 안전하게 실행하려면 공유된 가변 데이터에 접근하지 않아야 한다. 

이러한 함수를 순수 함수, 부작용 없는 함수, 상태없는 함수라고 부른다. 

두 프로세스가 공유된 변수를 동시에 바꾸려 한다면?

- 기존엔 synchronized 이용
- 자바8에서는 스트림을 이용

> **자바가 진화해야 하는 이유**
> 

전통적인 객체지향 프로그래밍과 함수형 프로그래밍은 완전 상극임. 

자바8의 큰 변화는 고전적 객체지향에서 벗어나 함수형 프로그래밍으로 다가섰다는 것. 

언어는 하드웨어나 프로그래머 기대의 변화에 부응하는 방향으로 변화해야 한다. 

<br/>
<br/>

## 2. 자바 함수

프로그래밍 언어의 핵심은 값을 바꾸는 것이다. 

프로그래밍 언어에서는 이 값을 **일급 값** 또는 **일급 시민** 이라고 부른다. 

하지만 프로그램을 실행하는 동안 모든 구조체를 자유롭게 전달할 수는 없다. 

이렇게 전달할 수 없는 구조체를 **이급 시민**이라고 부른다.

클래스, 메서드 등을 이급 자바 시민에 해당한다. 

자바8에서는 이급 시민을 일급 시민으로 바꿀 수 있는 기능을 추가했다. 

- **일급 객체(First-class citizen)란?**
    - 파라미터로 전달할 수 있다.
    - 반환값(return value)로 사용할 수 있다.
    - 변수나 데이터 구조 안에 담을 수 있다.
    - 할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.
    
    → 메서드나 클래스 등은 이급 자바 시민이다. 
    
- **Java에서 메소드를 일급객체라 부를 수 없는 이유**
    - ex) String getName() 이라는 메서드를 특정 변수에 담으려 할 때 getName()의 return 값인 String 타입의 데이터가 아닌 getName() 자체를 변수에 담을 수 없다.

자바8 설계자들은 메서드나 클래스와 같은 이급 자바 시민을 일급시민으로 만들어 프로그램을 유용하게 활용할 수 있는 기능을 추가했다. 

> **메서드와 람다를 일급 시민으로**
> 

자바8에서는 메서드를 값으로 취급할 수 있는 환경을 제공하고 있다.  이는 스트림과 같은 자바8 기능의 토대를 제공했다. 

참조

[[Java] Java로 살펴보는 일급 객체](https://isooo.github.io/etc/2019/11/13/%EC%9D%BC%EA%B8%89%EA%B0%9D%EC%B2%B4.html)

<br/>

### 메소드 참조

다음은 숨겨진 파일들을 필더링 하는 코드이다. 

```java
File[] hiddenFiles = new File(".").listFiles(new FileFilter(){
	public boolean accept(File file){
		return file.isHidden();
	}
});
```

자바 8이 나타나기 전에는 File 객체에 숨겨진 파일을 걸러내는 isHidden 이라는 함수가 있음에도 불구하고 FileFilter 로 감싼 다음에 인스턴스화해서 사용해야 했다. 

자바8의 메소드의 참조`::` 를 이용하면 isHidden 함수를 직접 전달할 수 있다. 

```java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```
<br/>

### 람다 : 익명함수

자바8에서는 람다함수도 값으로 취급할 수 있다. 

메소드 정의를 사용하지않고 람다 코드가 필요한 경우는, 이용할 수 있는 편리한 클래스나 메서드가 없을 때 람다문법을 이용하여 더 간결하게 코드를 구현할 수 있다. 

람다 문법으로 구현된 프로그램을 함수형 프로그래밍이라고 한다. = 함수를 일급 값으로 넘겨주는 프로그램 구현

<br/>
<br/>

## 3. 코드 넘겨주기

```java
public static void main(String... args) {
    List<Apple> inventory = Arrays.asList(
        new Apple(80, "green"),
        new Apple(155, "green"),
        new Apple(120, "red")
    );
}

public static class Apple {

    private int weight = 0;
    private String color = "";

    public Apple(int weight, String color) {
      this.weight = weight;
      this.color = color;
    }

    public int getWeight() {
      return weight;
    }

    public void setWeight(int weight) {
      this.weight = weight;
    }

    public String getColor() {
      return color;
    }

    public void setColor(String color) {
      this.color = color;
    }

  }
```

다음과 같이 Apple 클래스와 Apple 리스트를 반환하는 변수 inventory 가 있다. 이때 녹색 사과를 선택해서 리스트를 반환하는 프로그램을 구현해보자 = 이러한 동작을 필터 라고 한다. 

<br/>


자바8 이전 구현방식 

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if ("green".equals(apple.getColor())) {
        result.add(apple);
      }
    }
    return result;
  }
```

위와 같은 메서드를 정의해놓는 방식. 하지만 사과의 무게를 기준으로 필터를 하겠다면? 거의 동일한 코드로 같은 메서드를 하나 더 만들어야 한다. 

<br/>


자바8 에서의 구현

```java
// 메서드 호출 
public static void main(String... args) {
    List<Apple> inventory = Arrays.asList(
        new Apple(80, "green"),
        new Apple(155, "green"),
        new Apple(120, "red")
    );

    // [Apple{color='green', weight=80}, Apple{color='green', weight=155}]
    List<Apple> greenApples = filterApples(inventory, FilteringApples::isGreenApple);
    System.out.println(greenApples);

    // [Apple{color='green', weight=155}]
    List<Apple> heavyApples = filterApples(inventory, FilteringApples::isHeavyApple);
    System.out.println(heavyApples);

}

public static boolean isGreenApple(Apple apple) {
    return "green".equals(apple.getColor());
  }

public static boolean isHeavyApple(Apple apple) {
    return apple.getWeight() > 150;
  }

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (p.test(apple)) {
        result.add(apple);
      }
    }
    return result;
  }
```

- predicate란?
    
    수학에서는 인수로 값을 받아 boolean값으로 반환하는 함수를 프리디케이트라고 한다. 위의 코드에서는 Apple::isGreenApple 메서드를 filterApples 인수로 넘겨주었음.

    <br/>
   <br/>

### 메서드 전달에서 람다로

```bash
filterApples(inventory,(Apple a) -> "green".equals(a.getColor()));
filterApples(inventory,(Apple a) -> a.getWeight() > 150 );
filterApples(inventory,(Apple a) -> a.getWeight() < 80 || "red".equals(a.getColor()));
```

isGreenApples 와 같이 한번만 사용할 메서드는 굳이 안만드는것이 좋다. 

위와 같이 넘겨주려는 코드를 애써 찾을 필요가 없이 람다식으로 바로 코드를 넘겨준다. 

**But 람다식이 몇줄 이상으로 길어진다면,** 이름을 가진 메서드를 명시하고 사용하는것이 좋다. 

**코드의 명확성이 우선시 되어야 한다.!!**