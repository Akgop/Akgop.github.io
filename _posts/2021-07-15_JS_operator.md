---
title: \[Javascript] 연산자 (operator)
categories: 
  - javascript
tags: 
  - nullish coalescing operator
toc: true
---

## 논리 연산자(Logical Operator)

나는 프로그래밍을 C언어로 시작했다. 그래서 기본적으로 논리 연산자에 대해서 알고 있었다. 그래서 js 공부중에 논리 연산자 부분을 소홀히 넘어갈뻔 했다. 그러나 신기한 성질이 있어서 정리해보려고 한다.

### OR

```jsx
let firstName = "";
let lastName = "";
let nickName = "Akgop";

console.log(firstName || lastName || nickName || "익명");
>>> Akgop

nickName = "";
console.log(firstName || lastName || nickName || "익명");
>>> 익명
```

js에서의 OR 연산은 왼쪽에서부터 오른쪽으로 탐색하며, 첫 번째 truthy 값을 형 변환하기 이전 값으로 반환한다. 기존 C를 기준으로 사고한다면 결과는 nickName이나 "익명"이 truthy하기 때문에 True가 반환이 되어야 할 것 같지만, js에서는 형 변환하기 이전 값인 nickName에 해당하는 Akgop을 반환한다는 것이다.

```jsx
console.log( console.log(1) || 2 );
>>> 1
>>> 2
```

여기서는 `console.log(1)` 의 반환값이 `undefined`이기 때문에 truthy하지 않기 때문에 넘어가고 2가 출력된다. 그러나 `console.log(1)`자체가 실행되어 1도 실행된다. 즉, 1, 2가 순서대로 실행된다.

이 성질을 이용해서 단락 평가(short circuit evaluation)을 할 수 있다.

```jsx
console.log(true || "이문장 절대 출력 안됨");
>>> true

console.log(false || "이 문장 무조건 출력");
>>> 이 문장 무조건 출력
```

만약 모든 연산자가 false라면 마지막 피연산자를 반환한다.

### AND

반대로 `&&` 는 자연스럽게 첫 번째 falsy 값을 찾는다고 생각하면 된다. 왼쪽부터 시작해서, falsy값을 만나면, 형 변환하기 이전의 해당 값을 반환하는 것이다. 또한, 모든 연산자가 true라면 마지막 피연산자를 반환한다.

```jsx
console.log(true && "이 문장은 출력되나? NO" && false);
>>> false

console.log(true && "이 문장은 출력되나? NO" && undefined);
>>> undefined

console.log(true && "이 문장은 출력되나? YES");
>>> 이 문장은 출력되나? YES
```

AND는 OR보다 연산자 우선순위가 높다.

```jsx
console.log( null || 2 && 3 || 4 );
>>> 3
```

`2 && 3` 에서 마지막 피연산자인 3이 반환되고, 식은 `null || 3 || 4` 로 바뀐다. 가장 첫 번째 truthy인 3이 반환된다.

### NOT

NOT 연산도 js에서 독특한 점이 하나 있어서 정리한다. !는 boolean으로 형변환을 하고, 이에 대해 역을 수행한다.

```jsx
console.log(!!"불린으로 변환되서 true 출력됨.");
>>> true
```

이 코드에서 `!"불린으로 변환되서 true 출력됨."` 이 부분에서 문자열이 비어있지 않기 때문에 true로 형 변환을 하고, 이에 대해 역을 수행한다. 따라서 false가 된다. 그러나 !가 하나 더 붙어 있어서 `!false` 가 되고, 결국 true를 출력한다. NOT 연산자 2개를 이용하면 boolean형으로 형변환을 할 수 있다.

## 널 병합 연산자(Nullish Coalscing Operator)

사실 이 부분이 생소해서 연산자를 정리하게 되었다. 심지어 OR과 헷갈릴 요소도 존재해서 정리한다.

`a ?? b` 는 a가 `null` 도 아니고 `undefined`도 아니면 a를, 아니면 b를 반환.

```jsx
let nco;
nco = nco ?? 100;
console.log(nco);
>>> 100
```

위 코드에서 nco는 `undefined`이다. 따라서 b에 해당하는 100을 반환하는 것을 알 수 있다.

```jsx
let nco = 0;
console.log(nco ?? 100);
>>> 0

console.log(nco || 100);
>>> 100
```

or 연산자와 헷갈릴 요소가 있다. 0으로 할당하는 경우인데, || 입장에서 0은 falsy한 값이므로 건너뛰고 100을 출력하게 된다. 반면 ?? 입장에서는 0으로 할당이 되어 null과 undefined가 모두 아니기 때문에 0을 반환하게 된다.

## 연산자 우선순위 (precedence)

연산자에는 여러 종류가 있지만 js를 공부하며 신기했던 점이 하나 더 있다. 단항 연산자를 통한 형변환 연산이다.

```jsx
let price1 = "1000";
let price2 = "2000";

console.log(price1 + price2);
>>> 10002000

console.log(+price1 + +price2);
>>> 3000
```

`console.log(+price1 + +price2);` 이 부분에서 price1, price2 앞에 붙은 연산자는 단항(unary) 연산자이다. 그리고 그 둘 사이의 + 는 이항(binary) 연산자이다. 대부분의 단항 연산자는 이항 연산자보다 우선순위가 높다. 그래서 먼저 실행된다. 따라서 `+price1` 은 `Number(price1)` 과 동일하게 형변환을 먼저 수행하게 된다. 그래서 string concat이 아니라 실제로 더하기 연산이 된 것이다.