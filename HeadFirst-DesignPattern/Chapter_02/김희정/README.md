# 2장 옵저버 패턴

## 옵저버 패턴



> 💡 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many) 의존성을 정의합니다.


신문사 + 구독자 = 옵저버 패턴

- 주제(Subject) : 신문사 (one)
- 옵저버(Observer) : 구독자 (many)

옵저버는 주제에 딸려 있으며, 주제의 상태가 바뀌면 옵저버에게 정보가 전달된다.

**📌 의존성**

옵저버는 데이터가 변경되었을 때 주제에서 갱신해 주기를 기다리는 입장이기에 의존성을 가진다.

⇒ 여러 객체가 동일한 데이터를 제어하는 방식보다 더 깔끔한 객체지향 디자인 가능

**📌 출판-구독 (Publish-Subscribe) 패턴과의 연관성**

출판-구독 패턴과 유사하지만 옵저버와 같은 패턴은 아니다. 출판-구독 패턴은 구독자가 서로 다른 유형의 메시지에 관심 가질 수 있고, 출판사와 구독자를 더 세세히 분리할 수 있는 복잡한 패턴이다. (ex. 미들웨어 시스템)

## 옵저버 패턴의 구조

- 하나의 주제마다 여러 개의 옵저버 존재 (one-to-many)

**Subject**

`Interface Subject`

- 주제를 나타내는 Subject 인터페이스
- 옵저버 등록, 탈퇴, 알림 메소드 존재

  `Class ConcreteSubject`

- Subject Interface를 구현하는 주제 역할의 구상 클래스
- 옵저버 등록, 탈퇴, 알림(상태가 바뀔 때마다 모든 옵저버에게 알림) 메소드 구현

**Observer**

`Interface Observer`

- 옵저버를 나타내는 Observer 인터페이스
- update() 메소드
    - 주제의 상태가 바뀌었을 때 호출되는 함수

`Class Observer`

- Observer Interface를 구현하는 옵저버 클래스
- 각 옵저버는 특정 주제에 등록해 연락받을 수 있음

## 옵저버 패턴에서의 느슨한 결합 (Loose Coupling)

> 💡 디자인 원칙 : 상호작용하는 객체 사이에는 가능하면 느슨한 결합을 사용해야 한다.

**📌 느슨한 결합**

- 객체들이 상호작용할 수는 있지만, 서로를 잘 모르는 관계
- 유연성
    - 느슨한 결합을 활용하면 객체 사이 상호의존성을 최소화할 수 있기에 변경 사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축할 수 있다.

**📌 옵저버 패턴에서의 느슨한 결합**

1. 주제는 옵저버가 Observer 인터페이스를 구현한다는 사실만 안다.
    1. 옵저버의 구상 클래스가 무엇인지, 옵저버의 행위는 알 필요가 없다.
2. 옵저버는 언제든지 새로 추가할 수 있다.
    1. 주제는 Observer인터페이스를 구현하는 객체 목록 (`List<Observer>`)에만 의존하므로 언제든지 새로운 옵저버 추가 할 수 있다.
    2. 실행 중에도 다른 옵저버로 교체되거나 제거해도 주제는 계속해서 다른 옵저버에 데이터를 보낼 수 있다.
3. 새로운 형식의 옵저버를 추가할 때도 주제를 변경할 필요가 없다.
    1. 새로운 옵저버 클래스가 생겨도 Observer 인터페이스만 구현한다면 주제는 어떤 객체에도 연락할 수 있다.
4. 주제와 옵저버는 서로 독립적으로 재사용할 수 있다.
    1. 주제나 옵저버를 다른 용도로 활용해도 서로 느슨하게 결합되어 있기에 손쉽게 재사용할 수 있다.
5. 주제나 옵저버가 달라져도 서로에게 영향을 미치지 않는다.
    1. 주제 인터페이스나 옵저버 인터페이스를 구현한다는 조건만 만족하면 서로 어떻게 고쳐도 문제가 생기지 않는다.

## 예시 : 기상 모니터링 애플리케이션

### 요구사항

![image.png](https://github.com/user-attachments/assets/ff6b5dbd-daa4-41b2-8904-cd2114baabbc)


> 💡 WeaterData 객체로 (1) 현재 조건, (2) 기상 통계, (3) 기상 예보를 디스 플레이 장비에 갱신해 가며 보여주는 애플리케이션

**📌 제공하는 부분**

- 기상 스테이션 : 실제 기상 정보를 수집하는 물리 장비
- WeatherData 객체 : 기상 스테이션으로부터 오는 정보를 추적하는 객체

**📌구현해야 하는 부분**

- 디스플레이 장비 : 사용자에게 현재 기상 조건을 보여 주는 디스플레이 장비
- 새로운 기상 측정 데이터가 올 때마다 WeatherData 의 `measermentsChanged()` 메소드 호출됨

⇒ 디스플레이를 업데이트하도록 `measermentsChanged()`에 코드 추가

- 확장성 고려
    - 3가지 디스플레이 외 다른 디스플레이가 추가된다는 것 고려

```java
class WeatherData {

	public void measermentsChanged(){
	  float temp = getT();
	  float humidity = getH();
	  float pressure = getP();
	  
	  //각 디스플레이 갱신
	  currentDisplay.update(temp, humidity, pressure);
	  statisticsDisplay.update(temp, humidity, pressure);
	  forecastDisplay.update(temp, humidity, pressure);
 }
```

**📌 위 코드의 문제점**

- 인터페이스가 아닌 구체적인 구현을 바탕을 코딩
- 새로운 디스플레이 항목이 추가될 때마다 코드 변경 필요
- 실행 중 디스플레이 항목을 추가하거나 제거할 수 없음
- 바뀌는 부분을 캡슐화하지 않음

### 설계

- Subject : WeatherData
- Observer : 각각의 Display

![image](https://github.com/user-attachments/assets/007e7499-ba6c-437c-81e3-447483fb6c5b)

## 라이브러리 속 옵저버 패턴

### 스윙 라이브러리

AbstractButton 내 리스너 추가 or 삭제 메소드

- 스윙 구성 요소에서 일어나는 다양한 유형의 이벤트를 감시하는 옵저버(리스너)를 추가하거나 제거하는 것

## Pull 방식의 옵저버

> 옵저버가 주제로부터 데이터를 당겨오는 풀(pull) 방식이 푸시보다 일반적으로 더 ‘옳은’ 방식이라고 간주한다.
>

**📌 Push 방식의 단점**

- 하나의 데이터만 갱신되도 update() 메소드에 모든 데이터를 보냄

⇒ 나중에 새로운 데이터가 추가되면 모든 옵저버 구현 클래스의 update()메소드 인자를 수정해야 한다.

⇒ 확장성이 좋지 않음

**📌 Pull 방식의 코드**

- Subject 구현 클래스의 getter 메소드로 데이터를 가져오게 한다.

```java
// Observer의 update()

public void update(){
	this.temp = subject.getTemp();
	this.humidity = subject.getHumidity();
	display();
}
```