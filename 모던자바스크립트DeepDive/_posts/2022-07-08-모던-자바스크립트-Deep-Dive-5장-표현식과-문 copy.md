---
layout: post
title: 모던 자바스크립트 Deep Dive 5장 표현식과 문
image:
  path: /assets/img/modernjs/main-img.png
description: >
  모던 자바스크립트 Deep Dive 5장 정리
sitemap: false
hide_last_modified: true
---

📘YES24
[![yes24](/assets/img/modernjs/yes24.png)](http://www.yes24.com/Product/Goods/92742567)

# 모던 자바스크립트 Deep Dive 5장 표현식과 문

# 값

- 값은 식(표현식)이 평가되어 생성된 결과를 말한다

```jsx
10 + 20; // 10 + 20은 평가되어 숫자 값 30을 생성한다
```

### 변수는 하나의 값을 저장하기 위해 확보한 메모리 공간 자체 또는 그 메모리 공간을 식별하기 위해 붙인 이름이다 따라서 변수에 할당되는 것은 식이 아니라 값이다

```jsx
// 변수 sum에 저장되는 것은 표현식 10 + 20이 아닌 평가되어 생성된 값 30이 할당된다
var sum = 10 + 20;
```

# 리터럴

- 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기법을 말함
- 자바스크립트 엔진은 코드가 실행되는 시점인 런타임에 리터럴을 평가해 값을 생성한다

| 리터럴             | 예시                              | 비고      |
| ------------------ | --------------------------------- | --------- |
| 정수 리터럴        | 100                               |           |
| 부동소수점 리터럴  | 10.5                              |           |
| 2진수 리터럴       | 0b01000001                        | 0b로 시작 |
| 8진수 리터럴       | 0o101                             | 0o로 시작 |
| 16진수 리터럴      | 0x41                              | 0x로 시작 |
| 문자열 리터럴      | 'Hello’                           |
| ”World”            |                                   |
| 불리언 리터럴      | true                              |
| false              |                                   |
| null 리터럴        | null                              |           |
| undefiend 리터럴   | undefined                         |           |
| 객체 리터럴        | { name: ‘Lee’, address: ‘Seoul’ } |           |
| 배열 리터럴        | [ 1, 2, 3 ]                       |           |
| 함수 리터럴        | function() {}                     |           |
| 정규 표현식 리터럴 | /[A-Z]+/g                         |           |

# 표현식

- 표현식은 값으로 평가될 수 있는 문
- 표현식이 평가되어 새로운 값을 생성하거나 기존값을 참조한다

```jsx
// 리터럴 표현식
10;
("Hello");

// 식별자 표현식(선언이 이미 존재한다고 가정)
sum;
person.name;
arr[1];

// 연산자 표현식
10 + 20;
sum = 10;
sum !== 10;

// 함수/메서드 호출 표현식(선언이 이미 존재한다고 가정)
square();
person.getName();
```

# 문

- 문은 프로그램을 구성하는 기본 단위이자 최소 실행 단위
- 문은 여러 토큰으로 구성된다
  - 토큰이란 문법적인 의미를 가지며, 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소를 의미한다
    ![Untitled](/assets/img/modernjs/5/5.png)
- 문을 명령문이라고도 부른다 문은 선언문, 할당문, 조건문, 반복문 등으로 구분할 수 있다

# 세미콜론과 세미콜론 자동 삽입 기능

- 세미콜론( ; )은 문의 종료를 나타낸다
- 자바스크립트 엔진이 **세미콜론 자동 삽입기능(ASI)**이 암묵적으로 수행되기 때문에 세미콜른은 생략 가능하다

<iframe width="560" height="315" src="https://www.youtube.com/embed/hJjYvVOEO7s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# 표현식인 문과 표현식이 아닌 문

- 표현식은 문의 일부일 수도 있고 그 자체로 문이 될 수도 있다

```jsx
var x; // 변수 선언문은 값으로 평가될 수 없으므로 표현식이 아니다

x = 1 + 2;
// 1, 2, 1 + 2, x = 1 + 2는 모두 표현식이다
// x = 1 + 2는 표현식이면서 완전한 문이기도 하다
```

### 표현식이 아닌 문은 값처럼 사용할 수 없다

```jsx
var foo = var x; // SyntaxError
```

### 표현식인 문은 값처럼 사용할 수 있다

```jsx
var foo = (x = 100);
console.log(foo); // 100
```
