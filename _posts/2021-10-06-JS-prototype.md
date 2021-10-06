---
title: \[Javascript] 프로토타입
categories: 
  - javascript
tags: 
  - prototype
  - oop
  - inheritance
  - overriding
toc: true
---

자바스크립트는 객체지향 프로그래밍 패러다임을 지원한다. C++, 자바는 클래스 기반인데, 자바스크립트는 프로토타입 기반이라고 한다. ES6+ 에서는 class를 지원하고 있지만, 결국 프로토타입 기반임에는 달라지는 바가 없다. 따라서 프로토타입을 제대로 알아야겠다는 생각을 했고, 정리를 한번 해보려고 한다.

## 프로토타입이란?

> 프로토타입이란, **프로토타입 객체**를 말하며, 자바스크립트에서 객체간 상속을 구현하기 위해 사용된다.
> 

### 프로토타입 객체

우선 자바스크립트 특성에 대해 알아보자. 자바스크립트는 원시값을 제외하고 모든 것이 객체이다. 이러한 모든 객체는 `[[Prototype]]` 내부 슬롯을 가진다. 이 내부 슬롯의 값은 프로토타입 객체의 참조(주소)다. 이 내부 슬롯은 직접 접근할 수 없으며 해당 슬롯이 가리키는 참조를 `__proto__` 접근자 프로퍼티로 참조할 수 있다.

모든 객체는 하나의 프로토타입(프로토타입 객체)을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 `prototype` 프로퍼티로 연결되어 있다. 그리고 프로토타입 객체는 `constructor` 프로퍼티로 생성자 함수를 다시 가리킬 수 있다. 위 내용을 하나의 그림으로 그려보면 아래와 같다.

![prototype1](/assets/images/javascript/prototype1.png)

이게 자바스크립트에서 기본적으로 제공하는 프로토타입 구조이다. `[[prototype]]`의 값을 `__proto__`를 이용해서 접근하고, `prototype`, `constructor` 프로퍼티는 순환을 이룬다. 이점을 일단 기억해두고 다음을 읽어보며 이해해보자.

참고로 위 부분은 크롬 브라우저에서 객체에 `__proto__`, `prototype`, `constructor` 계속 까보면 구조를 이해하는데 도움이 된다!

```jsx
function Person(name) {
    this.name = name;
}

Person.prototype.getName = function() {
    return this.name;
}

const person = new Person("akgop");

console.log(person.getName());
```

![prototype2](/assets/images/javascript/prototype2.png)

재밌다.

### 상속

상속이란 다른 언어에서의 상속과 동일하다. 다른 객체의 프로퍼티, 메서드를 상속받아서 그대로 사용할 수 있는 것을 말한다. 자바스크립트에선 상속을 클래스가 아닌 프로토타입 객체를 이용해서 구현한다.

우리가 상속을 통해 얻는 것은 불필요한 중복을 제거하고 코드를 재사용할 수 있다는 것이다.

```jsx
function Person(name) {
    this.name = name;
    this.getName = function () {
        return this.name;
    }
}

const person1 = new Person("akgop");
const person2 = new Person("ttoomm");

console.log(person1.getName());
console.log(person2.getName());
```

위 코드를 보자. 생성자 함수 `Person`을 통해 `akgop`과 `ttoomm`이라는 `person`을 두 개 만들었다. 이때 메모리는 이 코드를 어떻게 관리할까?

![prototype3](/assets/images/javascript/prototype3.png)

`getName`이라는 내부 변수가 인스턴스가 생성될 때마다 중복되는 문제가 생긴다. 이는 메모리의 불필요한 낭비이다. 퍼포먼스도 덩달아 악영향을 줄것이다. 

중복되는 `getName`을 프로토타입을 이용하여 압축?해보자.

```jsx
function Person(name) {
    this.name = name;
}

Person.prototype.getName = function() {
    return this.name;
}

const person1 = new Person("akgop");
const person2 = new Person("ttoomm");

console.log(person1.getName());
console.log(person2.getName());
```

![prototype4](/assets/images/javascript/prototype4.png)

getName이 Person.prototype에 추가되었고 person1, 2 객체는 이를 상속받아 호출할 수 있음을 보았다.

![prototype5](/assets/images/javascript/prototype5.png)

아무튼 `__proto__`를 통해 인스턴스의 프로토타입 객체를 확인할 수 있고, `getName`이 인스턴스가 아닌 프로토타입 객체에 넣어서 재사용함을 알 수 있다. 그리고 `person1`과 `person2` 가 getName을 갖다 쓸 수 있으므로 `__proto__`를 통한 상속이 가능함을 알 수 있다.

위의 내용까지 알면 기본적인 프로토타입 개념을 이해할 수 있고, 왜 사용해야 하는지 이해할 수 있을 것이다. 그리고 개략적인 용어의 헷갈림에서 벗어날 수 있을 것이다.

그럼 이제는 객체 종류별로 프로토타입이 어떻게 생성되는지 알아보자

## 프로토타입 생성 시점

프로토타입은 생성자 함수가 생성되는 시점에 **더불어** 생성된다. (리터럴 표기법으로 생성한 객체도 프로토타입이 동일하게 존재한다. 또한 가상 생성자 함수가 있다.) 즉, 프로토타입과 생성자 함수는 단독으로 존재할 수 없고, 언제나 쌍으로 존재한다. 그리고 서로 순환한다.

### 사용자 정의 함수의 프로토타입 생성 시점

일반 함수(함수 선언문, 표현식)으로 정의한 함수 객체는 `new` 연산자와 함께 생성자 함수로서 호출할 수 있다. 화살표 함수처럼 `non-constructor`는 프로토타입이 생성되지 않는다. 이 때 프로토타입도 더불어 생성된다.

함수 선언문은 호이스팅에 의해 런타임 이전에 코드가 평가되는 때, 엔진에 의해 먼저 실행되고, 이때 프로토타입도 같이 생성되는 것이다.

프로토타입 객체 또한 객체이다. 모든 객체는 `[[prototype]]` 내부 슬롯을 갖는다. 이때 프로토타입 객체가 참조하는 상위 프로토타입은 `Object.prototype`이다. 이는 모든 프로토타입 객체의 최상위 객체이다.

아래와 같이 크롬 브라우저 콘솔에 작성해보자. (`Person`은 앞서 만든 예제에서 가져옴)

```jsx
Person.prototype.__proto__ === Object.prototype;    // true
Object.prototype.__proto__;    // null <- 이 위는 없다.
```

### 빌트인 생성자 함수의 프로토타입 생성 시점

Object, String, Number, Array 등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다. 빌트인 생성자 함수는 전역객체가 생성되는 시점에 생성된다.

## 객체 생성 방식과 프로토타입의 결정

### 객체 리터럴

객체 리터럴로 생성하면 Object 생성자 함수, Object.prototype, 생성된 객체 사이에 연결이 만들어진다.

![prototype6](/assets/images/javascript/prototype6.png)

위 사진이 어떻게 가능한가?? - 상속

`Object.prototype` 객체는 `constructor` 프로퍼티, `hasOwnProperty, get __proto__, set __proto__` 등 여러 메서드를 갖고 있다. 여기서 생성한 객체 `obj`는 `Object.property`로부터 **상속받기 때문**에 `constructor`를 사용할 수 있다.

### Object 생성자 함수에 의해 생성된 객체

기본적으로 객체 리터럴과 동일하다. 차이점은, 프로퍼티를 추가하는 방식이다. 객체 리터럴 방식은 객체 리터럴 내부에 프로퍼티를 추가하지만, Object 생성자 함수 방식은 일단 빈 객체를 생성하고 프로퍼티를 따로 추가해야 한다.

### 생성자 함수에 의해 생성된 객체

new 연산과 함께 생성자 함수를 호출하여 인스턴스를 생성하면 프로토타입은 해당 함수에 대한 프로토타입이다. 

```jsx
function Person(name) {
    this.name = name;
}

Person.prototype.getName = function() {
    return this.name;
}

const person1 = new Person("akgop");
const person2 = new Person("ttoomm");

console.log(person1.getName());
console.log(person2.getName());
```

즉, `Person` 예제를 다시 끌고와서,,, `Person` 생성자 함수, `Person.prototype, person1, person2` 객체가 생성되는데, 여기서 `person1, person2`는 `Person.prototype`으로부터 상속 받는다. 앞서 정리한 내용에 해당한다. 그럼 여기서 한번 더 생각해서 `Person.prototype`은? `Object.prototype`으로부터 상속을 받는다!

## 프로토타입 체인

자바스크립트 객체는 프로퍼티에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면, `[[Prototype]]` 내부 슬롯의 참조를 따라서 자신의 부모 프로토타입의 프로퍼티를 순차적으로 검색한다.

맨 처음 작성한 상속에 대한 내용이 결국 프로토타입이 단방향 링크드리스트를 이루기 때문이다. 이를 프로토타입 체인이라고 부른다. 상속의 원리이니 잘 알아두자. 그리고 최상위 객체는 다시 한번 말하지만 `Object.prototype`이다.

스코프 체인과 헷갈리지 말자! 스코프체인은 식별자를 찾는 과정이고, 프로토타입 체인은 상속관계에서 내부 메서드, 프로퍼티를 찾는 과정이다.

## 프로퍼티 섀도잉(Shadowing)과 오버라이딩

앞서 사용했던 Person 예제를 또 소환해보자.

```jsx
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.printInfo = function() {
    console.log(`Hi I'm ${this.name}`);
}

const person1 = new Person("akgop", 26);
const person2 = new Person("ttoomm", 26);

person1.printInfo = function() {
    console.log(`Hi I'm ${this.name} and I'm ${this.age} years old`);
}

person1.printInfo();    // Hi I'm akgop and I'm 26 years old
person2.printInfo();    // Hi I'm ttoomm
```

person1은 printInfo를 인스턴스 메서드로 추가했고, 해당 메서드가 사용된다. person2는 여전히 프로토타입 객체에서 상속받은 메서드를 사용하는 것을 확인했다.

![prototype7](/assets/images/javascript/prototype7.png)

위 그림처럼 인스턴스에 상위 프로토타입과 같은 메서드의 이름을 추가해도 프로토타입 체인을 따라올라가는 것이 아니라 바로 그 위치에 생성되는 것을 알 수 있다. 따라서 person1의 기준으로는 상위 프로퍼티가 가려지는 효과를 얻게 되는데 이를 프로퍼티 섀도잉이라고 한다. 이 원리를 이용하면 위 코드와 같이 **오버라이딩**을 구현할 수 있다.  `person1`객체는, 이름 뿐 아니라 나이까지 출력하는 새로운 함수로 `printInfo`를 오버라이딩 한 것이다.