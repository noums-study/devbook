```
책에는 기법에 대한 단순 설명뿐만 아니라 안전하게 리팩토링을 하는 방법에 대해서도 잘 나와있다.
예제뿐만 아니라 방법에 대해서도 숙지하면 좋을 것 같다.
```

## 메서드 정리 방법


### 메서드 추출
> 어떤 코드를 그룹으로 묶어도 되겠다고 판단될 땐 그 코드를 빼내어 목적을 잘 나타내는 직관적 이름의 메서드로 만들자.

- 예제
```java

// 기본 메서드
void printOwing(double previousAmount) {
	Enumeration e = _orders.elements();
	double outstanding = previousAmount * 1.2;

	System.out.println("###################");
	System.out.println("######고객 외상######");
	System.out.println("###################");

	// 외상액 계산
	while (e.hasMoreElements()) {
		Order each = (Order) e.nextElement();
		outstanding += each.getAmount();
	}

	System.out.println("고객명 : " + _name);
	System.out.println("외상액 : " + outstanding);
}


// 메서드 추출 사용
void printOwing(double previousAmount) {
	printBanner(); // 지역변수 사용 안하는 경우

	double outstanding = getOutstanding(previousAmount * 1.2); // 지역변수를 다시 대입하는 경우

	printDetails(outstanding); // 지역변수 사용하는 경우
}

void printBanner() {
	System.out.println("###################");
	System.out.println("######고객 외상######");
	System.out.println("###################");
}

void printDetails(double outstanding) {
	System.out.println("고객명 : " + _name);
	System.out.println("외상액 : " + outstanding);
}

double getOutstanding(double initialValue) {
	Enumeration e = _orders.elements();
	double result = initialValue;

	// 외상액 계산
	while (e.hasMoreElements()) {
		Order each = (Order) e.nextElement();
		result += each.getAmount();
	}
	return result;
}

```


### 메서드 내용 직접 삽입
> 메서드 기능이 너무 단순해서 메서드명만 봐도 너무 뻔할 땐 그 메서드의 기능을 호출하는 메서드에 넣어버리고 그 메서드는 삭제하자.

```java

// 수정 전
boolean canBuy(int price, boolean hasItem) {
	return isCheap() && hasItem;
}

boolean isCheap(int price) {
	return price > 10000;
}

// 수정 후
boolean canBuy(int price, boolean hasItem) {
	return (price > 10000) && hasItem;
}

```


### 임시변수 내용 직접 삽입
> 간단한 수식을 대입받는 임시변수로 인해 다른 리팩토링 기법 적용이 힘들 땐 그 임시변수를 참조하는 부분을 전부 수식으로 치환하자.

```java

// 수정 전
double price = order.getPrice();
return (price > 10000);

// 수정 후
return (order.getPrice() > 10000);
```


### 임시변수를 메서드 호출로 전환
> 수식의 결과를 저장하는 임시변수가 있을 땐 그 수식을 빼내어 메서드로 만든 후, 임시변수 참조 부분을 전부 수식으로 교체하자. <br>
> 새로 만든 메서드는 다른 메서드에서도 호출 가능하다.

```java

// 수정 전
double getPrice() {
	int basePrice = _quantity * _itemPrice;
	double discountFactor;
	if (basePrice > 1000) discountFactor = 0.95;
	else discountFactor = 0.98;
	return basePrice * discountFactor;
}

// 1차 수정 (임시변수 내용 직접 삽입 실시)
double getPrice() {
	double discountFactor;
	if (basePrice() > 1000) discountFactor = 0.95;
	else discountFactor = 0.98;
	return basePrice() * discountFactor;
}

private int basePrice() {
	return _quantity * _itemPrice;
}

// 2차 수정 (메서드 추출)
double getPrice() {
	double discountFactor = discountFactor();
	return basePrice() * discountFactor;
}

private double discountFactor() {
	if (basePrice() > 1000) {
		return 0.95;
	} 
	return 0.98;
}


// 최종
double getPrice() {
	return basePrice() * discountFactor();
}

```

### 직관적 임시변수 사용
> 사용된 수식이 복잡할 땐 수식의 결과나 수식의 일부분을 용도에 부합하는 직관적 이름의 임시변수에 대입하자.

```java

// 수정 전
int price(Order order) {
	return order.getPrice() * order.getCount() + (_tax - _free) * _taxRate;
}

// 수정 후
int price(Order order) {
	int totalPrice = order.getPrice() * order.getCount();
	int taxFee = (_tax - _free) * _taxRate;
	return totalPrice + taxFee;
}

```

### 임시변수 분리 
> 루프 변수나 값 누적용 임시변수가 아닌 임시변수에 여러 번 값이 대입될 땐 각 대입마다 다른 임시변수를 사용하자.

```java

// 수정 전
double temp = _height + _width;
System.out.println(temp);
temp = _height * _width;
System.out.println(temp);

// 수정 후
double sum = _height + _width;
System.out.println(sum);
double multiply = _height * _width;
System.out.println(multiply);
```
### 매개변수로의 값 대입 제거
> 매개변수로 값을 대입하는 코드가 있을 땐 매새변수 대신 임시변수를 사용하게 수정하자.


### 메서드를 메서드 객체로 전환
> 지역변수 때문에 메서드 추출을 적용할 수 없는 긴 메서드가 있을 땐 그 메서드 자체를 객체로 전환해서 모든 지역변수를 객체의 필드로 만들자.<br>
> 그런 다음 그 메서드를 객체 안의 여러 메서드로 쪼개면 된다.


### 알고리즘 전환
> 알고리즘을 더 분명한 것으로 교체해야 할 땐 해당 메서드의 내용을 새 알고리즘으로 바꾸자.

