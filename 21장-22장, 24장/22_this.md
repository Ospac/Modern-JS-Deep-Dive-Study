# 22장 this
## 22.1 this 키워드
객체지향 프로그래밍에서 동작을 나타내는 메서드는 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다. 이때 **자신이 속한 객체를 가리키는 식별자를 참조하는 키워드**가 필요하다.

- 객체 리터럴 방식으로 생성한 객체는, 속한 객체를 재귀적으로 참조할 수 있다.
- 생성자 함수 방식으로 인스턴스를 생성하는 경우에는, 재귀 참조가 불가능하다.
    - 함수를 정의하는 시점에서는, 인스턴스를 생성하기 이전이므로 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
```js
//Circle은 인스턴스가 아니라 생성자 함수의 식별자라서 재귀 참조가 불가능
function Circle(radius) { 
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  ????.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```

- `this`는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. 이를 통해 프로퍼티나 메서드를 참조할 수 있다.
- 함수를 호출하면, `arguments` 객체와 `this`가 암묵적으로 함수 내부에 전달된다.
- this가 가리키는 값, 즉 **this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.**
     - 바인딩: 식별자와 값을 연결하는 과정, `this`와 `this가 가리키는 객체`를 연결하는 과정
    1. 전역에서 `this`는 전역 객체 `window`를 가리킨다.
    2. 일반 함수 내부에서 `this`는 전역 객체 `window`를 가리킨다.
    3. 메서드 내부에서 `this`는 메서드를 호출한 객체를 가리킨다.
    4. 생성자 함수 내부에서 `this`는 생성자 함수가 생성할 인스턴스를 가리킨다.
```js
// this는 어디서든지 참조 가능하다.
// 1.전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window

function square(number) {
  // 2.일반 함수 내부에서 this는 전역 객체 window를 가리킨다.(strict 모드에서는 undefined 할당, 일반 함수 내부에서는 this 사용 X)
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: 'Lee',
  getName() {
    // 3.메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
    console.log(this); // {name: "Lee", getName: ƒ}
    return this.name;
  }
};
console.log(person.getName()); // Lee

function Person(name) {
  this.name = name;
  // 4.생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  console.log(this); // Person {name: "Lee"}
}

const me = new Person('Lee');
```
## 22.2 함수 호출 방식과 this 바인딩

**this 바인딩**(this에 바인딩될 값)은 함수 호출 방식, 어떻게 호출되었는지에 따라 **동적으로 결정**된다.

> **렉시컬 스코프는 정적, this 바인딩은 동적**
> 함수의 상위 스코프를 결정하는 렉시컬 스코프는 함수 정의가 평가되어  함수 객체가 생성되는 시점에 상위 스코프를 결정한다. 하지만 this 바인딩은 함수 호출 시점에 결정된다.

1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

### 22.2.1 일반 함수 호출
기본적으로 `this`에는 전역 객체가 바인딩된다.
- 일반 함수로 호출된 모든 함수(중첩, 콜백 함수 포함) 내부의 `this`에는 전역 객체가 바인딩 된다.
- 메서드와 콜백 함수의 `this`가 일치하지 않는 것은 콜백 함수를 헬퍼 함수로서 동작하기 어렵게 만든다. `this` 바인딩을 일치시킬 필요가 있다.
    - this를 복사해서 사용하거나, `Function.prototype.apply`, `Function.prototype.call`, `Function.prototype.bind` 메서드를 사용해서 명시적 바인딩을 이용한다.
```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value: 100, foo: ƒ}
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  }
};

obj.foo();
```
- 또는 화살표 함수를 사용해서 this 바인딩을 일치시킬 수 있다.
```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  }
};

obj.foo();
```

### 22.2.2 메서드 호출
메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 `this`에 바인딩된다.
- 주의할 점은 메서드를 소유한 객체가 아니라, 메서드를 호출한 객체에 바인딩된다는 것
- 메서드는 주소값이 복사 가능한 객체기 때문에, 다른 객체의 메서드로 복사될 수 있다.

그림 22-2

```js
const anotherPerson = {
  name: 'Kim'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
// Node.js 환경에서 this.name은 undefined다.
```

- 프로토타입 메서드 내부에서 사용된 `this`도 해당 메서드를 호출한 객체에 바인딩된다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person('Lee');

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // ① Lee

Person.prototype.name = 'Kim';

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // ② Kim
```

### 22.2.3 생성자 함수 호출
생성자 함수 내부 `this`에는 생성자 함수가 생성할 인스턴스가 바인딩된다.
- `new`연산자와 함께 호출하지 않으면 일반 함수로 동작하여 `this`는 전역객체를 가리킨다.

```js
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체를 생성
const circle1 = new Circle(5);
// 반지름이 10인 Circle 객체를 생성
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

### 22.2.4 `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출
#### apply, call
`apply`, `call`, `bind` 메서드는 `Function.prototype`의 메서드다. 따라서 모든 함수가 상속받아 사용할 수 있다.
- `apply`와 `call`의 기본 기능은 함수를 호출하는 것이다.
    - 함수를 호출하면서 '첫 번째 인수로 전달한 객체'를 호출한 함수의 `this`에 바인딩한다.
    - 함수 내부의 `this`는 전달한 객체를 가리킨다.
    - 두 함수는 인수를 전달하는 방식만 다르다.
```js
/**
 * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
 * @param thisArg - this로 사용할 객체
 * @param argsArray = 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
 * @returns 호출된 함수의 반환값
 */
Function.prototype.apply(thisArg[, argsArray])

/**
 * 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출한다.
 * @param thisArg - this로 사용할 객체
 * @param arg1, arg2, ... = 함수에게 전달할 인수 리스트
 * @returns 호출된 함수의 반환값
 */
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

- `apply` 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
```js
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
```
- `call` 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
```js
console.log(getThisBinding.call(thisArg, 1, 2, 3));
```

- 두 메서드의 대표적인 용도는 `arguments` 객체 같은 유사 배열 객체에 배열 매서드를 사용하는 경우다.
```js
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

#### bind

`Function.prototype.bind` 메서드는 함수를 호출하지 않고 `this`로 사용할 객체만 전달한다.

- 메서드의 `this`와 메서드 내부의 중첩 함수 또는 콜백 함수의 `this`가 불일치하는 문제를 해결할 때 유용하게 사용된다.

```js
const person = {
  name: 'Lee',
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    // bind 메서드는 해당 함수를 반환한다.
    setTimeout(callback.bind(this), 100);
  }
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // Hi! my name is Lee.
});
```