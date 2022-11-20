---
layout: post
title: 모던 자바스크립트 Deep Dive 15장 let, cont키워드와 블록 레벨 스코프
image:
  path: /assets/img/modernjs/main-img.png
description: >
  모던 자바스크립트 Deep Dive 15장 정리
sitemap: false
hide_last_modified: true
---

📘YES24
[![yes24](/assets/img/modernjs/yes24.png)](http://www.yes24.com/Product/Goods/92742567)

# 모던 자바스크립트 Deep Dive 15장 let, cont키워드와 블록 레벨 스코프

# var 키워드로 선언한 변수의 문제점

## ① 변수 중복 선언 허용

- var 키워드로 선언한 변수는 중복 선언이 가능하다

```jsx
var x = 1;
var y = 1;

var x = 100;
var y;

console.log(x); // 100
console.log(y); // 1
```

- var 키워드로 선언한 변수를 중복 선언하면 초기화문 유무에 따라 다르게 동작한다
- 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작
- 초기화문이 없는 변수 언언문은 무시된다 → 에러는 발생하지 않는디ㅏ

## ② 함수 레벨 스코프

- var 키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정한다
- 함수 외부에서 var 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 된다

## ③ 변수 호이스팅

- var 키워드로 변수를 선언하면 변수 호이스팅에 의해 변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작한다
- 변수 호이스팅에 의해 var 키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있다

```jsx
console.log(foo); // undefined

foo = 123;

console.log(foo); // 123

var foo;
```

# let 키워드

## ① 변수 중복 선언 금지

- let 키워드로 이름이 같은 변수를 중복 선언하면 문법 에러가 발생한다

```jsx
var foo = 123;

var foo = 456;

let bar = 123;

let bar = 456; // SyntaxError
```

## ② 블록 레벨 스코프

- let 키워드로 선언한 변수는 모든 코드 블록을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다

```jsx
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // bar is not defined
```

## ③ 변수 호이스팅

- let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다

```jsx
console.log(foo); // foo is not defined
let foo;
```

- let 키워드로 선언한 변수는 선언 단계와 초기화 단계가 분리되어 진행된다
- let 키워드로 선언한 변수는 스코프의 시작 지점부터 초기화 단계 시작 지점까지 변수를 참조할 수 없다
- 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간을 **일시적 사각지대( TDZ )**라고 부른다

```jsx
console.log(foo); // foo is not defined

let foo;
console.log(foo); // undefined

foo = 1;
console.log(foo); // 1
```

![Untitled](/assets/img/modernjs/15/15-1.png)

```jsx
let foo = 1; // 전역 변수
{
  console.log(foo); // ReferenceError
  let foo = 2; // 지역 변수
}
```

### → let 키워드로 선언한 변수도 여전히 호이스팅이 발생하기 때문에 참조 에러가 발생한다

## 전역 객체와 let

- var 키워드로 선언한 전역 변수와 전역 함수, 그리고 선언하지 않은 변수에 값을 할당할 암묵적 전역은 전역객체 window의 프로퍼티가 된다
  - 전역 객체의 프로퍼티를 참조할 때 window를 생략할 수 있다

```jsx
var x = 1; // 전역 변수

y = 2; // 암묵적 전역

function foo() {} // 전역 함수

console.log(window.x); // 1
console.log(x); // 1

console.log(window.y); // 2
console.log(y); // 2

console.log(window.foo); // f foo() {}
console.log(foo); // f foo() {}
```

### let 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다

```jsx
let x = 1;

console.log(window.x); // undefined
console.log(x); // 1
```

# const 키워드

- const 키워드는 상수를 선언하기 위해 사용
- const 키워드의 특징은 let 키워드와 대부분 동일하다

## const 선언과 초기화

- const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화 해야한다

```jsx
const foo = 1;
```

- const 키워드로 선언한 변수는 let 키워드로 선언한 변수와 마찬가지로 블록 레벨 스코프를 가지며, 변수 호이스팅이 발생하지 않는 것처럼 동작한다

```jsx
{
  console.log(foo); // ReferenceError
  const foo = 1;
  console.log(foo); //1
}

console.log(foo); // ReferenceError
```

## 재할당 금지

- const 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없다
- const 키워드는 상수를 표현하는데 사용된다

> 💡상수는 재할당이 금지된 변수를 말한다

- const 키워드로 선언된 변수에 원시 값을 할당한 경우 원시 값은 변경할 수 없는 값이고 const 키워드에 의해 재할당이 금지되므로 할당된 값을 변경할 수 있는 방법은 없다
- 상수는 프로그램 전체에서 공통적으로 사용하므로 나중에 값이 변경되면 상수만 변경하면 되기 때문에 유지보 수성이 대폭 향상된다
- 일반적으로 상수의 이름은 대문자로 선언해 상수임을 명확히 나타내고 여러 단어로 이뤄진 경우 언더 스코어( \_ )로 구분해서 스네이크 케이스로 표현하는것이 일반 적이다

```jsx
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + preTaxprice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

## const 키워드와 객체

- const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다
- const 키워드는 재할당을 금지할 뿐 불변을 의미하는 것은 아니다

```jsx
const person = {
  name: "Lee",
};

// 객체는 변경 가능한 값이다. 따라서 재할당 없이 변경 가능하다
person.name = "Kim";

console.log(person); // Kim
```

### 변수 선언에는 기본적으로 const를 사용하고 let은 재할당이 필요한 경우에 한정해 사용하는 것이 좋다

### ● ES6를 사용한다면 var 키워드는 사용하지 않는다

### ● 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다

### ● 변경이 발생하지 않고 읽기 전용으로 사용하는 원시 값과 객체에는 const 키워드를 사용한다. const 키워드는 재할당을 금지하므로 var, let 키워드보다 안전하다
