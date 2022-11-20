---
layout: post
title: 모던 자바스크립트 Deep Dive 20장 strict mode
image:
  path: /assets/img/modernjs/main-img.png
description: >
  모던 자바스크립트 Deep Dive 20장 정리
sitemap: false
hide_last_modified: true
---

📘YES24
[![yes24](/assets/img/modernjs/yes24.png)](http://www.yes24.com/Product/Goods/92742567)

# 모던 자바스크립트 Deep Dive 20장 strict mode

# `strict mode`란?

- 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 **오류를 발생시킬 가능성이 높거**나 자바스크립트 엔진의 **최적화 작업에 문제를 일으킬 수 있는 코드**에 대해 명시적인 에러를 발생시킨다

### ESLint

- 린트 도구는 정적 분석기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 도구다
- `strict mode`가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있다

### 전역의 선두에 `‘use strict’;`를 추가하면 스크립트 전체에 `strict mode`가 적용된다

```jsx
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

### 함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 `strict mode`가 적용된다

```jsx
function foo() {
  "use strict";

  x = 10; // ReferenceError: x is not defined
}
foo();
```

### 코드의 선두에 `‘use strict’;`를 위치시키지 않으면 `strict mode`가 제대로 동작하지 않는다

```jsx
function foo() {
  x = 10; // 에러를 발생시키지 않는다
  ("use strict");
}
foo();
```

# 전역에 `strict mode`를 적용하는 것은 피하자

- 전역에 적용한 `strict mode`는 스크립트 단위로 적용된다
- 스크립트 단위로 적용된 `strict mode`는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용된다

```html
<!DOCTYPE html>

<html>
  <body>
    <script>
      "use strict";
    </script>
    <script>
      x = 1;
      console.log(x); // 1
    </script>
    <script>
      "use strict";
      y = 1; // ReferenceError: y is not defined
    </script>
  </body>
</html>
```

### `strict mode` 스크립트와 `non-strict mode` 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다

- 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 `strict mode`를 적용한다

```jsx
// 즉시 실행 함수의 선두에 strict mode적용
(function() {
	'use strict';

	...
}());
```

## 함수 단위로 `strict mode`를 적용하는 것도 피하자

- `strict mode`는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다

# `strict mode`가 발생시키는 에러

### 암묵적 전역

- 선언하지 않은 변수를 참조하면 `ReferenceError`가 발생한다

```jsx
(function() {
	'use strict';

	x = 1;
	console.log(x); // ReferenceError: x is not defined
})())
```

### 변수, 함수, 매개변수의 삭제

- `delete` 연산자로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`가 발생한다

```jsx
(function() {
	'use strict';

	var x = 1;
	delete x; // SyntaxError

	function foo(a) {
		delete a // SyntaxError
	}
	delete foo // SyntaxError
})());
```

### 매개변수 이름의 중복

- 중복된 매개변수 이름을 사용하면 `SyntaxError`가 발생한다

```jsx
(function() {
	'use strict';

	function foo(x, x) { // SyntaxError
		return x + x;
	}
	console.log(foo(1,2));
})());
```

### `with`문의 사용

- `with`문은 전달된 객체를 스코프 체인에 추가한다
  - 성능과 가독성이 나빠지는 문제가 있다

```jsx
(function() {
	'use strict';

	with({ x: 1 }) { //SyntaxError
		console.log(x);
	}
})());
```

# `strict mode` 적용에 의한 변화

### 일반 함수의 `this`

- `strict mode`에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`가 바인딩된다
  - 생성자 함수가 아닌 일반함수 내부에서는 `this`를 사용할 필요가 없기 떄문이다

```jsx
(function() {
	'use strict';

	function foo(x) {
		console.log(this); //undefined
	}
	foo();

	function poo() {
		console.log(this) // poo
	}
	new poo();
})());
```

### `arguments` 객체

- `strict mode`에서는 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않는다

```jsx
(function(a) {
	'use strict';

	a = 2;

	console.log(arguments); // { 0: 1, length: 1 }
})(1));
```
