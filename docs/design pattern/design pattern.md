---
layout: default
title: 디자인 패턴
has_children: true
---

## 객체지향의 기초

- 캡슐화
- 상속
- 추상화
- 다형성


## 디자인 원칙

1. 애플리케이션에서 **달라지는 부분을 찾아내고(캡슐화)**, 달라지지 않는 부분과 **분리**한다.

2. 구현보다는 **인터페이스**에 맞춰서 프로그래밍한다.

3. 상속보다는 **구성(composition)**을 이용한다.

4. 상호작용하는 객체 사이에는 가능하면 **느슨한 결합(Loose Coupling)**을 사용해야 한다.

5. 클래스는 **확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다.** (OCP - Open-Closed Principle)


## 객체지향 패턴

- 생성
- 구조
  - [데코레이터 패턴](./decorator%20pattern/decorator%20pattern.html) : 특수 래퍼(Wrapper)로 객체를 추가하는 구조 패턴
- 행위
  - [전략 패턴](./strategy%20pattern.html) : 알고리즘군을 정의하고 각각을 캡슐화하여 수정해서 쓸 수 있도록 하는 패턴
  - [옵저버 패턴](./observer%20pattern/observer%20pattern.html) : 주제(Subject)가 옵저버(Observer)들에게 연락하고, 내용을 갱신하는 패턴