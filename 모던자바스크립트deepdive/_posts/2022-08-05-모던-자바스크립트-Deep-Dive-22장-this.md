---
layout: post
title: 모던 자바스크립트 Deep Dive 22장 this
image:
  path: /assets/img/modernjs/main-img.png
description: >
  모던 자바스크립트 Deep Dive 22장 정리
sitemap: false
hide_last_modified: true
---

📘YES24
[![yes24](/assets/img/modernjs/yes24.png)](http://www.yes24.com/Product/Goods/92742567)

# 모던 자바스크립트 Deep Dive 22장 this

# this 키워드

- 객체는 **상태를 나타내는 프로퍼티**와 **동작을 나타내는 메서드**를 하나의 **논리적인 단위로 묶은 복합적인 자료구조**다
- 동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 변경할 수 있어야 한다
  - 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야한다

### 객체 리터럴 방식으로 생성한 객체의 경우 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조할 수 있다

```jsx
const circle = {
  radius: 5, // 프로퍼티: 객체 고유의 상태

  // 메서드: 프로퍼티를 참조하고 조작하는 동작
  getDiameter() {
    // 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 자신이 속한 객체인 circle을 참조해야 한다
    return 2 * circle.radius;
  },
};
```

### → 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지도 않다

## 생성자 함수 방식으로 인스턴스를 생성하는 경우

```jsx
function Circle(radius) {
  Circle.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * Circle.radius;
};

const circle1 = new Circle(5);
console.log(circle1.getDiameter()); // 10

const circle2 = new Circle(10);
console.log(circle2.getDiameter()); // 20
onsole.log(circle1.getDiameter()); // 20
```

- 생성자 함수를 정의하는 시점에는 인스턴스를 생성하기 이전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없다
  - 자신이 속한 객체 또는 자신이 생성한 인스턴스를 가리키는 특수한 식별자가 필요하다
- this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다
- this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다
- this는 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조할 수 있다
- 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달된다
- 함ㅁ수 내부에서 arguemnts 객체를 지역 변수처럼 사용할 수잇는 것처럼 this도 지역 변수처럼 사용할 수 있다
- this가 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다

> 💡 this 바인딩
> 바인딩이란 식별자오 값을 연결하는 과정을 의미한다

### 객체 리터럴의 메서드 내부에서의 this는 메서드를 호출한 객체를 가리킨다

```jsx
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다
    return 2 * this.radius;
  },
};
```

### 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다

- this는 상황에 따라 가리키는 대상이 다른다
- 자바스크립트의 this는 함수가 호출되는 방식에 따라 this에 바인딩될 값, 즉 this 바인딩이 동적으로 결정된다
- this는 코드 어디에서든 참조 가능하다 전역에서도 함수 내부에서도 참조할 수 있다

```jsx
console.log(this); // window

function square(number) {
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: "Kim",
  getName() {
    console.log(this); // person
    return this.name;
  },
};
console.log(person.getName()); // Kim

function Person(name) {
  this.name = name;
  console.log(this); // Person{name: 'Kim'}
}

const me = new Person("Kim");
// new연산자를 사용해서 객체를 안만들면 생성자 함수가 아닌 일반 함수가 실행
// 그래서 this에는 window가 바인딩된다
Person(); // window
```

# 함수 호출 방식과 this 바인딩

- this 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다

<aside>
💡 함수 상위 스코프를 경정하는 방식인 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다. 하지만 this 바인딩은 함수 호출 시점에 결정된다

</aside>

## ① 일반 함수 호출

- 기본적으로 this에는 전역 객체가 바인딩된다

```jsx
function foo() {
  console.log("foo's this: ", this); // foo's this: window
  function bar() {
    console.log("bar's this: ", this); //bar's this: window
  }
  bar();
}
foo();
```

- 일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다
- this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로 객체를 생성하지 않는 일반 함수에서는 this가 의미가 없다
  - strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩된다

```jsx
function foo() {
  "use strict";
  console.log("foo's this: ", this); // undefinde
  function bar() {
    console.log("bar's this: ", this); // undefinde
  }
  bar();
}
foo();
```

- 메서드 내에 정의된 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 객체가 바인딩된다

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // foo's this: {value: 100, foo: ƒ}
    console.log("foo's this.value: ", this.value); // foo's this: 100

    function bar() {
      console.log("bar's this: ", this); //bar's this: window
      console.log("bar's this: ", this); //bar's this: 1
    }
    bar();
  },
};

obj.foo();
```

- 콜백 함수가 일반 함수로 호출된다면 콜백 함수 내부의 this에도 전역 객체가 바인딩 된다
- 어떠한 함수라도 일반 함수로 호출되면 this에 전역 객체가 바인딩 된다

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // foo's this: {value: 100, foo: ƒ}

    setTimeout(function () {
      console.log("callback's this: ", this); //callback's this: window
      console.log("callback's this: ", this); //callback's this: 1
    }, 100);
  },
};

obj.foo();
```

> 💡 setTimeout 함수
> setTimeout 함수는 두 번째 인수로 전달한 시간(ms)만큼 대기한 다음, 첫번째 인수로 전달한 콜백 함수를 호출하는 타이머 함수다

### 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩 된다

- 메서드 내부의 중첩함수나 콜백 함수의 this 바인딩을 메서드의 this 바인딩과 일치시키기 위한 방법은 화살표함수, apply, call, bind 메서드를 사용한다

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    const a = this; // this 바인딩을 변수 that에 할당한다

    setTimeout(function () {
      console.log(a.value); // 100
    }, 100);
  },
};

obj.foo();
```

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(
      function () {
        console.log(this.value); // 100
      }.bind(this),
      100
    ); // 콜백 함수에 명시적으로 this를 바인딩한다
  },
};

obj.foo();
```

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다
    setTimeout(() => console.log(that.value), 100); // 100
  },
};

obj.foo();
```

## ② 메서드 호출

- 메서드 내부의 this에는 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩된다
- 메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩된다는 것이다

```jsx
const person = {
  name: "Kim",
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다
    return this.name;
  },
};

// 메서드 getName을 호출한 객체는 person이다
console.log(person.getName()); // 'Kim'
```

![Untitled](/assets/img/modernjs/22/22-1.png)

### → person 객체의 getName 프로퍼티가 가리키는 함수 객체는 person 객체에 포함된 것이 아니라 독립적으로 존재하는 별도의 객체다

### → getName 프로퍼티가 가리키는 함수 객체, 즉 getName메서드는 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수도 있고 변수에 할당하여 일반 함수로 호출될 수도 있다

```jsx
const person = {
  name: "Kim",
  getName() {
    return this.name;
  },
};

const anotherPerosn = {
  name: "Lee",
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerosn.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다
console.log(anotherPerosn.getName()); // Lee

// getName 메서들를 변수에 할당
const getName = person.getName();

// getName메서르를 일반 함수로 호출
console.log(getName); // ''
// 일반 함수로 호출된 getName함수 내부의 this는 window다
// window.name은 빌트인 프로퍼티이며 기본값은 ''이다
```

### → 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩된다

![Untitled](/assets/img/modernjs/22/22-2.png)

- 프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 **해당 메서드를 호출한 객체에 바인딩**된다

```jsx
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Kim");

console.log(me.getName()); // Kim

Person.prototype.name = "Lee";

console.log(Perosn.prototype.getName()); // Lee
```

![Untitled](/assets/img/modernjs/22/22-3.png)

## ③ 생성자 함수 호출

- 생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩된다

```jsx
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);

const circle2 = new Circle(10);

console.log(circle1.gerDiamerter()); // 10
console.log(circle2.gerDiamerter()); // 20

// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다
const circle3 = Circle(15);
// 일반 함수로 호출된 Circle에는 반환문이 없으므로 암묵적으로 undefined를 반환한다
console.log(circlr3); // undefined
// 일반 함수로 호출된 Circle 내부의 this는 전역 객체를 가리킨다
console.log(radius); // 15
```

## ④ Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- apply, call, bind 메서느는 Function.prototype의 메서드다. 즉, 이들 메서드는 모든 함수가 상속받아 사용할 수 있다

![Untitled](/assets/img/modernjs/22/22-4.png)

- Function.prototype.apply/call/bind 메서드는 this로 사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출한다

### apply메서드와 call 메서드의 본질적인 기능은 함수를 호출하는 것이다

- apply와 call 메서드는 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다
- apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다

```jsx
function getThisBinding() {
  return this;
}

// this로 사용될 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // window

console.log(getThisBinding.apply(thisArg)); // {a:1}
console.log(getThisBinding.call(thisArg)); // {a:1}

console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
console.log(getThisBinding.call(thisArg, 1, 2, 3));
```

### apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다

### call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다

### → apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식 만 다를 뿐 this로 사용할 객체를 전달하면서 함수를 호출하는 것은 동일하다

### Function.prototype.bind메서드는 함수를 호출하지 않는다

- 첫 번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성해 반환한다

```jsx
function getThisBinding() {
  return this;
}
// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this바인딩이 교체됨
// getThisBinding 함수를 새롭게 생성해 반환한다
console.log(getThisBinding.bind(thisArg)); //ƒ getThisBinding()
// bind 메서드는 함수를 호출하지 않으므로 명시적으로 호출해야 한다
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

- bind메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용된다

```jsx
const person = {
  name: "Kim",
  foo(callback) {
    setTimeout(callback, 100);
  },
};

person.foo(function () {
  console.log(`Hi my name is ${this.name}.`); // Hi my name is .
});
```

```jsx
const person = {
	name: 'Kim',
	foo(callback) {
		/bind 메서드로 callback 함수 내부의 this 바인딩을 전달
		setTimeout(callback.bind(this),100);
	}
};

person.foo(function() {
	console.log(`Hi my name is ${this.name}.`); // Hi my name is .
});
```

| 함수 호출 방식                                             | this 바인딩                                                            |
| ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| 일반 함수 호출                                             | 전역 객체                                                              |
| 메서드 호출                                                | 메서드를 호출한 객체                                                   |
| 생성자 함수 호출                                           | 생성자 함수가 생성할 인스턴스                                          |
| Function.prototype.apply/call/bind 메서드에 의한 간접 호출 | Function.prototype.apply/call/bind 메서드에 첫 번째 인수로 전달한 객체 |
