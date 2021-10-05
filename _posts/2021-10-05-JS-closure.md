---
title: \[Javascript] 클로저
categories: 
  - javascript
tags: 
  - closure
  - encapsulation
  - information hiding
  - execution context
  - lexical environment
toc: true
---

> 자바스크립트 처음 공부하면서 여기저기 나오는데 이해하기 힘들었던 경험이 있다. 자바스크립트에서의 클로저는 스코프, 실행 컨텍스트와 함께 연계되는 개념이라고 생각한다. 블로그에 작성해놨으니 같이 보면 도움이 될지도..?


클로저는 자바스크립트 고유의 개념이 아니다. 함수를 일급 객체로 취급하는 **함수형 프로그래밍 패러다임**에서 사용되는 특성이다. 본 포스트에서는 자바스크립트에서의 클로저를 다루려고 한다!

## 클로저란?

> 외부 함수보다 중첩 함수가 더 오래 유지되는 경우, 중첩 함수는 이미 **생명 주기가 종료한 외부 함수의 변수를 참조**할 수 있다. 이러한 **중첩 함수를 클로저(closure)**라고 부른다.


사실 내가 참조한 여러 문서들에 따르면, 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이라고 설명한다. `함수 + 함수 렉시컬 환경` 이다. 무슨말인지 차근차근 알아보자.

### 렉시컬 스코프, 렉시컬 환경

```jsx
const x = 1;

function outer() {
    const x = 10;
    function inner() {  // 중첩함수!
        console.log(x); // 10
    }
    inner(); // 중첩 함수 호출
}

outer();
```

위 코드를 살펴보자. `outer` 안에 `inner`이 있다. `inner`의 `console.log(x)`는 내부 환경 레코드에서 값을 찾을 수 없다. 따라서 외부 렉시컬 스코프 참조에 의해 `outer`에서 선언된 x를 찾아서 값을 출력할 수 있게 된다. 만약 `inner`함수가 `outer`의 내부 중첩 함수가 아니었다면, outer에 접근할 수 없다. 이는 자바스크립트가 **렉시컬 스코프**를 따르기 때문이다.

**렉시컬 스코프**란, 함수를 **어디에서 정의** 했는지에 따라 상위 스코프를 결정하는 것이다. 다른말로는 정적 스코프.

외부 렉시컬 스코프 참조에는 함수 정의가 "평가" 되는 시점에 결정되기 때문에 `inner`가 `outer`를 가리키는 것이다.

### 함수 객체의 `[[Environment]]` 내부 슬롯

함수가 정의된 환경(위치)과 호출되는 환경(위치)은 다를 수 있다. 그렇기에 올바른 상위 스코프를 참조하기 위해 자바스크립트는 `[[Environment]]` 내부 슬롯을 통해 자신이 정의된 환경, 즉 상의 스코프의 참조를 저장한다.

스코프 체인을 구성하던 **외부 렉시컬 스코프 참조**(`OuterLexicalEnvironmentReference`)에는 `[[Environment]]` 에 저장된 렉시컬 환경의 참조가 할당 되는 것이다. 

```jsx
const x = 1;

function outer() {
    const x = 10;
    inner();
}

function inner() {
    console.log(x); // 1
}

outer();
```

코드로 예시를 들어보자.

앞서 설명한 코드와 다르게 `inner`는 더이상 outer 내부에 정의되어 있지 않고 전역에 정의 되어있다. 따라서 x는 1을 출력한다.

![closure1](/assets/images/javascript/closure1.png)

전역 평가 시점에 `inner` 함수 객체 내부의 `[[Environment]]` 슬롯에 상위 스코프를 전역으로 가리키고 있다. `inner` 함수 호출 시, `inner` 렉시컬 환경은 `[[Environment]]` 를 보고 외부 렉시컬 스코프 참조는 전역을 가리키게 된다. 전역에 있는 선언적 렉시컬 환경 레코드를 보고 1 을 출력하게 되는 것이다.

### 클로저 & 렉시컬 환경

이번엔 살짝 변형된 코드를 보자.

```jsx
const x = 1;

function outer() {
    const x = 10;
    const inner = function () {
        console.log(x);
    };
    return inner;
}

const innerFunc = outer();  // 여기
innerFunc();
```

자바스크립트의 모든 함수는 자신의 상위 스코프를 기억한다.

모든 함수가 기억하는 상위 스코프는 함수를 어디서 호출하든 상관없이 유지된다. 따라서 함수를 어디서 호출하든 상관없이 함수는 언제나 자신이 기억하는 상위 스코프의 식별자를 참조할 수 있고, 식별자에 바인딩 된 값을 변경할 수도 있다.

![closure2](/assets/images/javascript/closure2.png)

위 코드에 대해 `outer()`가 종료되었을 경우이다. `outer`는 종료되어 **실행 컨텍스트에서 제거**되고, 이를 `innerFunc`에 할당했다. 따라서 선언형 환경 레코드(`Declarative Environment Record`)의 `innerFunc`에 의해 `inner`함수를 참조할 수 있게 된다.

여기서 중요한 포인트는, `outer` 함수가 스택에서 제거되어도, **렉시컬 환경까지 같이 제거되는 것은 아니다**. 왜냐하면 참조 관계가 살아있기 때문에 **GC 대상이 아니기 때문이다**.

그리고 `innerFunc()`를 호출하게 되면 다음과 같은 스택, 자료구조를 갖게 된다.

![closure3](/assets/images/javascript/closure3.png)

이를 보면 `inner`함수는 외부 렉시컬 환경 참조에 의해 `outer` 함수의 렉시컬 환경을 참조할 수 있고, 이처럼 외부 함수보다 더 오래 생존한 중첩함수는 외부함수의 생존 여부(실행 컨텍스트)와 상관없이 자신이 정의된 위치에 의해 결정된 상위 스코프를 기억한다.(inner → outer)

이론상, 자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다. 하지만 그렇게 부르지 않는다. 상위 스코프의 식별자를 기억해야 클로저라고 부른다. 

모던 브라우저는 최적화를 통해 식별하고 있는 상위 스코프의 식별자만 기억한다고 한다. 따라서 아무것도 참조하지 않는 함수를 클로저라고 할 수 없다. 메모리 관점에서 필요없는 것을 기억할 필요가 없기 때문이라고 한다.

클로저에 의해 참조되는 상위 스코프의 변수를 **자유 변수(free variable)**라고 한다.

## 클로저의 활용

> 상태를 안전하게 변경하고 유지하기 위해 사용. 정보 은닉(information hiding).


클로저의 개념을 알아보았다. 이제 언제, 어떻게 사용하면 좋을지 알아보자.

클로저를 사용하면 상위 스코프의 식별자를 기억한다. 이 말은 상위 렉시컬 스코프의 식별자가 GC에 의해 소멸되지 않고 남아 있으면서, 상위 렉시컬 스코프에 직접 접근할 수 있는 수단이 없어졌다는 뜻이다. 즉, 상위 렉시컬 환경에 직접 접근할 방법을 없애며, 특정 함수에만 접근 권한을 허용할 수 있다는 뜻이다.

변수 값은 언제든지 변경될 수 있어 오류 발생의 근본적 원인이 될 수 있다. 함수형 프로그래밍 패러다임에서는 부수효과(side effect)를 최대한 억제하여 프로그램의 안정성을 높이기 위해 클로저가 적극 사용된다. 즉, 변수의 변경을 억제하고자 사용한다.

아래 두 코드의 차이를 알아보자.

```jsx
function makeCounter(predicate) {
    let counter = 0;
    return function() {
        counter = predicate(counter);
        return counter;
    };
}

function increase(n) { return ++n; }
function decrease(n) { return --n; }

const increaser = makeCounter(increase);
const decreaser = makeCounter(decrease);

console.log(increaser());  // 1
console.log(decreaser());  // -1
```

makeCounter를 increaser와 decreaser 식별자로 "각각" 호출하여 클로저를 반환 받았다. 클로저란, 상위 렉시컬 환경을 참조하는 것인데,,, 위 코드같이 구현하면 개별적인 렉시컬 환경 2개가 따로 놀게 된다. 이 점에 유의하자.

```jsx
const counter = (function() { 
    let counter = 0;
    return function(predicate) {
        counter = predicate(counter);
        return counter;
    };
}());

function increase(n) { return ++n; }
function decrease(n) { return --n; }

console.log(counter(increase));  // 1
console.log(counter(decrease));  // 0
```

이 코드는, `counter`라는 하나의 식별자가 즉시실행함수의 렉시컬 환경을 참조한다. 따라서 식별자 `counter`는 하나로 유지되고, `increase`, `decrease`와 같은 함수가 인자로 넘어간다. 인수로 전달받은 보조 함수(`predicate`)에 상태 변경을 위임하는 클로저가 구현이 되는 것이다. `predicate`라는 매개변수(함수)도 즉시실행함수 렉시컬 환경에 보관된다.

**왜 프로토타입으로 하지 않는가?**

프로토타입으로도 위 increase, decrease와 동일한 기능을 갖도록 할 수 있다. **다만 차이점은, num이 외부에서 접근이 가능한 public 프로퍼티가 된다. 즉시 실행 함수는 외부에서도 접근할 수 없는 은닉된 private 변수라는 점이 가장 큰 차이이다.**

따라서 외부 상태 변경이나 가변 데이터를 피하고 불변성을 지향하는 "함수형 프로그래밍"에서는 **부수 효과를 최대한 억제**하여 **오류를 피하고 프로그램의 안정성을 높이기 위해** 클로저를 적극적으로 사용한다.

### 캡슐화 & 정보 은닉(encapsulation & information hiding)

캡슐화(`Encapsulation`)는 객체의 상태(`state`)를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다. 

캡슐화는 객체의 특정 프로퍼티나 메서드를 **감출 목적**으로 사용하기도 한다. 이를 정보 은닉(`infomation hiding`)이라 한다.

정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호한다. 객체간의 상호 의존성, 즉 결합도(`coupling`)을 낮추는 효과가 있다.

자바스크립트에서는 완벽한 `private` 정보 은닉을 구현할 수는 없다. 왜냐하면 각 인스턴스가 `[[Environment]]` 내부 슬롯을 통해 **같은 상위 스코프를 참조**하기 때문에 `private` 변수가 덮어씌워진다.

결론적으로, 자바스크립트에서는 다른 언어에서처럼 완벽한 캡슐화와 정보 은닉은 불가능하니, 이점을 참고해서 개발자가 코드를 작성한다면 좋을 것이다. 하지만 아직 내 자바스크립트 실력이 부족한건지 모르겠지만, 클로저를 꼭 사용해서 코드를 구현해야 하는 상황을 맞아본적이 없다... 그래도 중요한 개념이니 필요할 때 쓸 수 있도록 알아두자.