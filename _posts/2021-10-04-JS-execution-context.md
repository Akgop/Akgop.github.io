---
title: \[Javascript] 실행 컨텍스트
categories: 
  - javascript
tags: 
  - execution context
  - lexical environment
toc: true
---

> 자바스크립트를 공부하다보면 실행 컨텍스트를 무조건 마주하게 된다. 그리고 실제로 모르면 동작 원리를 이해하지 못한다. 자바스크립트 이해도가 반쪽짜리가 되지 않기 위해 꼭 알아야 한다. 스코프, 호이스팅, 클로저, this등의 동작 원리를 제대로 이해하기 위해 차근차근 알아보자.


## 실행 컨텍스트란?

> 실행 컨텍스트는 **소스코드를 실행**하는 데 필요한 **환경을 제공**하고 코드의 실행 결과를 실제로 관리하는 영역이다.

- 자바스크립트 엔진은 소스코드의 평가와 실행을 다른 시점에 한다. 실행 컨텍스트는 소스코드의 평가 과정에서 생성한다. 이 타이밍에 변수, 함수 선언 등이 먼저 실행되어 실행 컨텍스트가 관리하는 **스코프**에 등록한다. 이 때문에 **호이스팅**이 일어나는 것이다.
- 이때 실행 컨텍스트가 관리하는 스코프를 **렉시컬 환경 레코드** 라고 한다.
- 모든 코드는 실행 컨텍스트에 의해 관리된다.
- 식별자와 스코프는 실행 컨텍스트의 **렉시컬 환경**으로 관리하고 코드의 실행 순서는 **실행 컨텍스트 스택**으로 관리한다. 실행 컨텍스트 스택이란, 엔진이 관리하는 **콜 스택**이다.

### 실행 컨텍스트 스택(Execution Context Stack)

> 실행 컨텍스트는 "평가"될 때마다 생긴다. 이는 스택 자료구조로 관리된다. 따라서 이를 실행 컨텍스트 스택이라 부른다.

- 실행 컨텍스트 스택은 코드의 흐름, 실행 순서를 관리한다.
- 최상위에 존재하는 실행 컨텍스트를 `running execution context`라고 부른다.

### 렉시컬 환경(Lexical Environment)

> 식별자와 식별자에 바인딩된 값, 그리고 **상위 스코프에 대한 참조**를 기록하는 자료구조로 실행 컨텍스트를 구성하는 **컴포넌트**다.

- 렉시컬 환경은 스코프와 식별자를 관리한다.
- 렉시컬 환경은 키와 값을 갖는 객체의 형태(자료구조)의 스코프를 생성하여 식별자를 key로 등록하고 식별자에 바인딩 된 값을 관리한다.
- **환경 레코드**와 **외부 렉시컬 환경**에 대한 참조로 이루어진다.
- **환경 레코드**(`Environment Record`)
    - 스코프에 포함된 식별자를 등록하고 등록된 식별자에 바인딩된 값을 관리하는 저장소다. 소스코드의 타입에 따라 관리하는 내용이 달라진다.
- 외**부 렉시컬 환경에 대한 참조**(`Outer Lexical Environment Reference`)
    - 상위 스코프를 가리킨다. 상위 스코프란 외부 렉시컬 환경을 말한다. 즉 호출된 부분을 말한다. 이를 통해 스코프 체인( 단방향 링크드리스트 스코프 체인 )을 구현한다.

## 실행 컨텍스트 생성과 식별자 검색 과정

아래 예시 코드를 통해 살펴보자.

```jsx
var x = 1;
const y = 2;

function foo(a) {
    var x = 3;
    const y = 4;
    
    function bar(b) {
        const z = 5;
        console.log(a, b, x, y, z);
    }
    bar(10);
}

foo(20);
```

### 전역 객체 생성 & 코드 평가

![ec1](/assets/images/javascript/execution_context1.png)

- 전역 객체(`window`)는 전역 코드가 평가되기 이전에 생성된다.
- 표준 빌트인 객체가 추가되고, 전역 함수, 전역 프로퍼티 등 특정 환경을 위한 호스트 객체를 포함해서 여러 내용이 생성된다. `Object.prototype`을 상속받는다.

소스코드가 로드되면 다음과 같이 전역 코드를 평가한다. 

1. 전역 실행 컨텍스트 생성
    - 비어있는 전역 실행 컨텍스트(`Global Execution Context`) 생성 → 실행 컨텍스트 스택에 푸시.
2. 전역 렉시컬 환경 생성
    - `Global Lexical Environment` 생성, `LexicalEnvironment`를 통해 전역 실행 컨텍스트(`Global Lexical Context`) 에 바인딩
    1. 전역 환경 레코드 생성(`GlobalEnvironmentRecord`)
        1. **객체 환경 레코드 생성**: var 키워드, 함수 선언문으로 정의한 전역함수, 빌트인 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체를 관리. 
        → 전역 코드 평가 과정에서 var 키워드로 선언한 전역 변수와 함수 선언문으로 정의된 전역 함수는 전역 환경 레코드(`GlobalEnvironmentRecord`)의 객체 환경 레코드(`Object Environment Record`)에 연결된 BindingObject를 통해 전역 객체의 프로퍼티와 메서드가 된다. 
        → Global Execution Context → Global Lexical Environment → Object Environment Record → window
        2. **선언적 환경 레코드 생성**: `let`, `const` 키워드로 선언한 전역 변수를 관리.
        
        y와 x가 다른 공간에 선언되었음을 통해 var, let의 차이를 알 수 있다.
        
    2. this 바인딩
        - 전역 환경 레코드(`GlobalEnvironmentRecord`)의 `[[GlobalThisValue]]` 내부 슬롯에 this가 바인딩 된다. 일반적으로 전역 코드에서 this는 전역 객체(window)를 가리키므로 전역 객체가 바인딩 된다.
    3. 외부 렉시컬 환경에 대한 참조 결정
        - 이 부분을 통해 상위 스코프를 가리키는 단방향 링크드 리스트 스코프 체인을 구현한다. 전역 코드이므로 상위가 없다. 따라서 null이 할당된다. 스코프 체인의 종점이라는 뜻이다.

### 전역 코드 실행

- 변수 할당문 함수 호출문을 실행하기 위해서는 선언된 식별자인지 확인해야 한다. → 따라서 식별자 결정(`identifier resolution`)을 해야 한다.
- 식별자 결정을 위해 식별자를 검색할 때는 **실행 중인 실행 컨텍스트에서 식별자 검색을 시작**한다.
- 외부 렉시컬 환경에 대한 참조 결정이 가리키는 스코프 체인을 타고 올라가며 검색한다. 종점까지 없다면 `ReferenceError`를 반환한다.

### foo 함수 코드 평가

![ec2](/assets/images/javascript/execution_context2.png)

전역 코드 실행까지 마친다면 마지막줄 `foo(20);` 에서 foo 함수 코드를 평가한다. foo 함수를 호출하기 직전의 상황이다.

1. 함수 실행 컨텍스트 생성 → `foo Execution Context`를 생성한다. 스택에 푸시된다.
2. 함수 렉시컬 환경 생성(`foo Lexical Environment`)
    1. 함수 환경 레코드 생성(`FunctionEnvironmentRecord`): 매개변수, `arguments`, 중첩 함수를 등록, 관리한다. var는 undefined로, let, const 는 uninitialized로
    2. this 바인딩: 함수 환경 레코드의 `[[ThisValue]]` 내부 슬롯에 this가 바인딩 된다. 이는 함수 호출 방식에 따라 결정된다. **foo 함수는 일반 함수로 호출되었으므로 this는 전역 객체(window)를 가리킨다.** 
    3. 외부 렉시컬 환경에 대한 참조 결정: foo 함수 정의가 평가된 시점에 실행중인 실행 컨텍스트의 렉시컬 환경(여기서는 `Global Lexical Environment`)의 참조(가리키고 있다는 뜻, 즉 주소값)가 할당된다. 
    → 자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 현재 실행중인 실행 컨텍스트의 렉시컬 환경( 여기서는 `Global Lexical Envrionment` )를 **함수 객체의 내부 슬롯** `[[Environment]]`에 저장한다. 여기서 `[[Environment]]`가 렉시컬 스코프를 구현하는 메커니즘이다. 외부 참조랑`[[Environment]]`는 다르다! 

### foo 함수 코드 실행

- 위 자료구조대로 참조하며 코드를 실행한다. 현재 실행중인 컨텍스트에 없다면 스코프 체인을 통해 상위 스코프로 이동한다. a에 20을, x에 3을, y에 4를 할당한다.

### bar 함수 코드 평가

현재 bar함수를 호출하기 직전 상황이다. bar 함수가 호출되면 bar 함수 코드를 평가한다.

![ec3](/assets/images/javascript/execution_context3.png)

- 여기도 마찬가지로 this는 전역 객체로 바인딩 된다. **this는 호출 시점에 결정된다는점**을 유의하자. 일반 함수로 호출되었으니까 foo와 마찬가지로 전역객체로 바인딩 되는 것이다.

### bar 함수 코드 실행

변수 할당을 하고 `console.log(a, b, x, y, z);` 가 실행된다.

1. console 식별자 검색
    - 우선 console 식별자를 `스코프 체인`에서 검색한다. `bar Lexical Environment` → `foo Lexical Environment` → `global Lexical Environment` → `BindingObject` → `window` 순으로 탐색하며 console을 찾을 수 있다.
2. log 메서드 검색
    - console 객체로부터 log 메서드를 검색한다. 바로 존재하기 때문에 사용한다. 만약 없었다면 상위 `prototype`을 검색하며 프로토타입 체인을 탐색한다.
3. a, b, x, y, z
    - 각 변수별로 `Lexical Environment`를 찾아다니며 값을 얻어온다. 현재 실행중인 컨텍스트 위치부터 `스코프 체인`을 이용해 찾는다.
4. `console.log()` 메서드 호출

### bar → foo → 글로벌 함수 코드 실행 종료

- 스택에서 제거된다.

## 실행 컨텍스트, 블록 레벨 스코프 개념 연결하기

실행 컨텍스트를 공부한 시점에서 블록 레벨 스코프와 개념이 충돌할 수 있다. let, const는 블록 레벨 스코프를 갖는다고 했다. 선언전 환경 레코드는 하나인데,,, 아래와 같은 경우 x는 실행 컨텍스트가 어떻게 구별할 것인가에 대한 개념 혼선이다.

```jsx
let x = 1;

if (condition) {
    let x = 10;
    // ... 1
}

// ... 2
```

블록 레벨 스코프를 구현하기 위해서 선언적 환경 레코드를 갖는 **렉시컬 환경(BLOCK Lexical Envrionment)을 새롭게 생성**하여 **기존의 전역 렉시컬 환경을 교체**한다.

![ec4](/assets/images/javascript/execution_context4.png)

위 코드에서 if 문을 통해 새로운 코드 블록이 생기는 경우 **블록 렉시컬 환경**이 생성되며 전역 컨텍스트가 가리키는 렉시컬 환경이 순간적으로 달라지는 것이다. 따라서 선언적 환경 레코드가 달라지고 let으로 선언된 변수 이름이 중복됨에도 각기 다른 값을 유지할 수 있는 것.

for 문의 변수 선언문에 let 키워드를 사용한 for문은 코드 블록이 반복해서 실행될 때마다 코드 블록을 위한 새로운 렉시컬 환경을 생성한다.