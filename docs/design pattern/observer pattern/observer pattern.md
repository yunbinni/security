---
layout: default
title: 옵저버 패턴
parent: 디자인 패턴
---

# 옵저버 패턴(Observer Pattern)
{: .no_toc }

여러 객체에게 연락하고(notify), 내용을 갱신(update)하기

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 하나하나 구현체에 의존한다면...

날씨 데이터들을 각각 디스플레이에 보여주는 앱이 있다.  
```WeatherData```는 날씨 데이터가 바뀔때마다 ```measurementsChange()``` 메서드를 호출해  
각각의 디스플레이 - 날씨조건, 날씨통계, 날씨예보를 업데이트한다.

![measurementsChanged](./measurementsChanged.drawio.svg)

```java
public class WeatherData {

    // 멤버변수 선언

    public void measurementsChanged() {

        float temp = getTemperature();
        float humidity = getHumidity();
        float pressure = getPressure();

        currentConditionDisplay.update(temp, humidity, pressure);
        statisticsDisplay.update(temp, humidity, pressure);
        forecastDisplay.update(temp, humidity, pressure);
    }

    // 기타 메서드
}
```

각각의 디스플레이 구현체에 ```update()``` 메서드를 하나하나 실행하고 있다.  
이 코드의 문제점은 다음과 같다.

1. 디스플레이 종류가 많아질때마다 ```update()```도 추가해야 한다.
2. 프로그램 실행중에 디스플레이를 추가/제거할 수 없다.
3. ```update()```의 파라미터들이 변할 수 있다. (바뀌는 부분을 캡슐화하지 않았다)

각 디스플레이들을 한번에 관리하도록 **Observer**로 만들고,
```WeatherData```는 그런 Observer들을 관리하는 주제 - **Subject**로 만들어보자.

---

## 옵저버 패턴의 도입

옵저버 패턴은 ```Subject```, ```Observer```를 인터페이스로 가진다.  
나중에 다른 주제/옵저버로 확장을 할 수가 있다.  

![observer pattern](./observer%20pattern.drawio.svg)

이것이 기본적인 옵저버 패턴의 클래스 구조라고 할 수 있다.  

이를 토대로 ```WeatherData```는 ```Subejct```를 구현하고,  
각각의 디스플레이들은 ```Observer```를 구현하도록 한다.

![weather station](./weather%20station.drawio.svg)

이제 코드로 구현해보자.  
먼저 기본적인 인터페이스부터 보자.

```java
public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers();
}

public interface Observer {
    public void update();
}

public interface DisplayElement {
    public void display();
}
```

```WeatherData```는 이제 ```Subject```의 구현체이다.  
날씨데이터에 특화된 주제이다. 구현해보자.  

```java
public class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;

    // 생성자
    public WeatherData() {
        observers = new ArrayList<Observer>();
    }

    // 인터페이스 구현부
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }

    // 날씨 데이터가 갱신되면 옵저버들에게 알림
    public void measurementsChanged() {
        notifyObservers();
    }

    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}
```

이제 각 디스플레이도 코딩하자.  
```CurrentConditionsDisplay```만 보자.  

```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    private float temperature;
    private float humidity;
    private WeatherData weatherData;

    public CurrentConditionsDisplay(WeatherData weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }

    public void update() {
        this.temperature = weatherData.getTemperature();
        this.humidity = weatherData.getHumidity();
        display();
    }
}
```

### push 방식과 pull 방식

[처음](#하나하나-구현체에-의존한다면)부분에 있었던 코드에서  
각 날씨 데이터들은 ```WeatherData```가 각 디스플레이에 보내고 있다.  
이는 주제가 옵저버에게 알리는 방식으로 **push**했다고 볼 수 있다.  

반면, 조금전 수정한 ```CurrentConditionsDisplay```의 ```update()```를 보면,  
주제로부터 데이터를 당겨오는 **pull**방식이라고 볼 수 있다.  

**pull** 방식은 필요할때마다 데이터를 가져오므로  
조금더 일반적이고 확장성있다고 볼 수 있다.