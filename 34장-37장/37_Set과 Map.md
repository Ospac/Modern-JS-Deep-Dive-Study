# 37장 Set과 Map
## 37.1 Set
Set 객체는 중복되지 않는 유일한 값들의 집합이다.
- 배열과의 차이

|구분|배열|Set 객체|
|------|:---:|:---:|
|동일한 값을 중복하여 포함할 수 있다.| O |X|
|요소 순서에 의미가 있다.|O|X|
|인덱스로 요소에 접근할 수 있다. |O|X|

- Set을 통해 교집합, 합집합, 차집합, 여집합 등을 구현할 수 있다.

### 37.1.1 Set 객체의 생성
- `Set` 생성자 함수로 생성한다.
- 인수를 전달하지 않으면 빈 Set 객체가 생성된다.
- 이터러블을 인수로 전달받아 생성한다, 이터러블의 중복된 값은 요소로 저장되지 않는다.
    - 이러한 특성을 활용하여 배열에서 중복된 요소를 제거할 수 있다.

```js
const set = new Set();
console.log(set); // Set(0) {}

const set1 = new Set([1, 2, 3, 3]);
console.log(set1); // Set(3) {1, 2, 3}

const set2 = new Set('hello');
console.log(set2); // Set(4) {"h", "e", "l", "o"}
```

- 중복 제거
```js
// 배열의 중복 요소 제거
const uniq = array => array.filter((v, i, self) => self.indexOf(v) === i);
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]

// Set을 사용한 배열의 중복 요소 제거
const uniq = array => [...new Set(array)];
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
```

### 37.1.2 요소 개수 확인 
`Set.prototype.size` 사용
```js
const { size } = new Set([1, 2, 3, 3]);
console.log(size); // 3
```

### 37.1.3 요소 추가
`Set.prototype.add` 사용
add 메서드는 새로운 요소가 추가된 Set 객체를 반환한다 (메소드 체이닝 사용 가능)
```js
const set = new Set();

set.add(1).add(2);
console.log(set); // Set(2) {1, 2}
```
- NaN과 NaN을 같다고 평가하여 중복 추가 허용 X
- 객체나 배열과 같이 JS의 모든 값을 요소로 저장 가능하다.

### 37.1.4 요소 존재 여부 확인
`Set.prototype.has` 사용
```js
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 37.1.5 요소 삭제
`Set.prototype.delete` 사용
- delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환한다.
- 인덱스가 아니라 요소값을 인수로 전달해야 한다. (Set은 인덱스 없음)
```js
const set = new Set([1, 2, 3]);

// 요소 2를 삭제한다.
set.delete(2);
console.log(set); // Set(2) {1, 3}

// 요소 1을 삭제한다.
set.delete(1);
console.log(set); // Set(1) {3}

// 존재하지 않는 요소 0을 삭제하면 에러없이 무시된다.
set.delete(0);

// delete는 불리언 값을 반환한다.
// 메서드 체이닝 불가능
set.delete(1).delete(2); // TypeError: set.delete(...).delete is not a function
```

### 37.1.6 요소 일괄 삭제
`Set.prototype.clear` 메서드 사용
- 언제나 `undefined` 반환
```js
const set = new Set([1, 2, 3]);

set.clear(); // undefined
console.log(set); // Set(0) {}
```

### 37.1.7 요소 순회
`Set.prototype.forEach` 메서드 사용
- 첫 번째 인수: 현재 순회 중인 요소값
- 두 번째 인수: 현재 순회 중인 요소값 (Array에서는 index를 전달 받는다. 인터페이스를 통일하기 위해 Set의 forEach도 비슷한 형태가 되었음)
- 세 번째 인수: 현재 순회 중인 Set 객체 자체

```js
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));
/*
1 1 Set(3) {1, 2, 3}
2 2 Set(3) {1, 2, 3}
3 3 Set(3) {1, 2, 3}
*/
```
- Set 객체는 **이터러블**이라서 `for...of`문 순회, 스프레드 문법, 배열 디스트럭처링의 대상이 될 수도 있다.
```js
const set = new Set([1, 2, 3]);

// Set 객체는 Set.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in set); // true

// 이터러블인 Set 객체는 for...of 문으로 순회할 수 있다.
for (const value of set) {
  console.log(value); // 1 2 3
}

// 이터러블인 Set 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...set]); // [1, 2, 3]

// 이터러블인 Set 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, ...rest] = [...set];
console.log(a, rest); // 1, [2, 3]
```

### 37.1.8 집합 연산

**교집합**
집합 A와 집합 B의 공통요소로 구성된다.
```js
Set.prototype.intersection = function (set) {
  const result = new Set();

  for (const value of set) {
    // 2개의 set의 요소가 공통되는 요소이면 교집합의 대상이다.
    if (this.has(value)) result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 교집합
console.log(setA.intersection(setB)); // Set(2) {2, 4}
// setB와 setA의 교집합
console.log(setB.intersection(setA)); // Set(2) {2, 4}
```
`filter`를 사용한 예시
```js
Set.prototype.intersection = function (set) {
  return new Set([...this].filter(v => set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 교집합
console.log(setA.intersection(setB)); // Set(2) {2, 4}
// setB와 setA의 교집합
console.log(setB.intersection(setA)); // Set(2) {2, 4}
```

**합집합**
집합 A와 집합 B의 중복 없는 모든 요소로 구성된다.

```js
Set.prototype.union = function (set) {
  // this(Set 객체)를 복사
  const result = new Set(this);

  for (const value of set) {
    // 합집합은 2개의 Set 객체의 모든 요소로 구성된 집합이다. 중복된 요소는 포함되지 않는다.
    result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 합집합
console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
// setB와 setA의 합집합
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```
스프레드 문법을 사용하여 통합하는 방법
```js
Set.prototype.union = function (set) {
  return new Set([...this, ...set]);
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 합집합
console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
// setB와 setA의 합집합
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```

**차집합**
집합 A에는 존재하지만 집합 B에는 존재하지 않는 요소로 구성된다.
```js
Set.prototype.difference = function (set) {
  // this(Set 객체)를 복사
  const result = new Set(this);

  for (const value of set) {
    // 차집합은 어느 한쪽 집합에는 존재하지만 다른 한쪽 집합에는 존재하지 않는 요소로 구성된 집합이다.
    result.delete(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA에 대한 setB의 차집합
console.log(setA.difference(setB)); // Set(2) {1, 3}
// setB에 대한 setA의 차집합
console.log(setB.difference(setA)); // Set(0) {}
```
`fitler`를 사용하는 예시
```js
Set.prototype.difference = function (set) {
  return new Set([...this].filter(v => !set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA에 대한 setB의 차집합
console.log(setA.difference(setB)); // Set(2) {1, 3}
// setB에 대한 setA의 차집합
console.log(setB.difference(setA)); // Set(0) {}
```

**부분 집합과 상위 집합**
집합 A가 집합 B에 포함되는 경우, 집합 A는 집합 B의 부분집합<sup>subset</sup>이고, 집합 B는 집합 A의 상위 집합<sup>superset</sup>이다.

```js
// this가 subset의 상위 집합인지 확인한다.
Set.prototype.isSuperset = function (subset) {
  for (const value of subset) {
    // superset의 모든 요소가 subset의 모든 요소를 포함하는지 확인
    if (!this.has(value)) return false;
  }

  return true;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA가 setB의 상위 집합인지 확인한다.
console.log(setA.isSuperset(setB)); // true
// setB가 setA의 상위 집합인지 확인한다.
console.log(setB.isSuperset(setA)); // false
```
`every`를 사용하는 예시
```js
// this가 subset의 상위 집합인지 확인한다.
Set.prototype.isSuperset = function (subset) {
  const supersetArr = [...this];
  return [...subset].every(v => supersetArr.includes(v));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA가 setB의 상위 집합인지 확인한다.
console.log(setA.isSuperset(setB)); // true
// setB가 setA의 상위 집합인지 확인한다.
console.log(setB.isSuperset(setA)); // false
```

## 37.2 Map

Map 객체는 키와 값의 쌍으로 이루어진 컬렉션이다. 
일반 객체와 Map의 차이점

|구분|객체|Map 객체|
|------|:---:|:---:|
|키로 사용할 수 있는 값| 문자열 또는 심벌 값 | 객체를 포함한 모든 값|
|이터러블|X|O|
|요소 개수 확인| Object.keys(obj).length | map.size|

### 37.2.1 Map 객체의 생성

Map 생성자 함수로 생성한다.
```js
const map = new Map();
// 인수를 전달하지 않으면 빈 Map 객체가 생성된다.
console.log(map); // Map(0) {}
```

Map 생성자 함수는 **이터러블을 인수**로 전달받아 Map 객체를 생성한다.
- 인수로 전달하는 이터러블은 **키와 값의 쌍으로 이루어진 요소**로 구성되어야 한다.
- 중복된 키를 갖는 요소가 존재하면 값이 덮어써진다.
```js
const map1 = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(map1); // Map(2) {"key1" => "value1", "key2" => "value2"}

const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

### 37.2.2 요소 개수 확인
`Map.prototype.size` 프로퍼티 사용 (getter만 존재하는 접근자 프로퍼티)
```js
const { size } = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(size); // 2
```

### 37.2.3 요소 추가
`Map.prototype.set` 메서드 사용
- 새로운 요소가 추가된 Map 객체를 반환한다(메서드 체이닝 가능)
- 중복된 키를 갖는 요소를 추가하면 값을 덮어 쓴다.
- NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
```js
const map = new Map();
map
  .set('key1', 'value1')
  .set('key2', 'value2');
console.log(map); // Map(2) {"key1" => "value1", "key2" => "value2"}
```
- 문자열 또는 심벌 값만 키로 사용하는 객체와 달리 Map 객체는 **키 타입에 제한이 없다.**

### 37.2.4 요소 취득

`Map.prototype.get` 메서드를 사용한다. 
- 인수로 키를 전달하면, 연결된 값을 반환한다. (없으면 `undefined` 반환)
```js
const map = new Map();

const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

map
  .set(lee, 'developer')
  .set(kim, 'designer');

console.log(map.get(lee)); // developer
console.log(map.get('key')); // undefined
```

### 37.2.5 요소 존재 여부 확인
`Map.prototype.has` 메서드를 사용한다. 불리언 값을 반환한다.
```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

console.log(map.has(lee)); // true
console.log(map.has('key')); // false
```

### 37.2.6 요소 삭제
`Map.prototype.delete` 메서드를 사용한다. 삭제 성공 여부를 나타내는 불리언 값을 반환한다.
- 존재하지 않는 키로 요소를 삭제하려 하면 에러 없이 무시된다.
- 메서드 체이닝 불가능
```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// 키를 인수로 전달한다.
map.delete(kim);
console.log(map); // Map(1) { {name: "Lee"} => "developer" }
```

### 37.2.7 요소 일괄 삭제
`Map.prototype.clear` 메서드를 사용한다. 언제나 `undefined`를 반환한다.
```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

map.clear();
console.log(map); // Map(0) {}
```

### 37.2.8 요소 순회
`Map.prototype.forEach` 메서드를 사용한다.
- 첫 번째 인수: 현재 순회 중인 요소값
- 두 번째 인수: 현재 순회 중인 요소키
- 세 번째 인수: 현재 순회 중인 Map 객체 자체

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

map.forEach((v, k, map) => console.log(v, k, map));
/*
developer {name: "Lee"} Map(2) {
  {name: "Lee"} => "developer",
  {name: "Kim"} => "designer"
}
designer {name: "Kim"} Map(2) {
  {name: "Lee"} => "developer",
  {name: "Kim"} => "designer"
}
*/
```

Map 객체는 이터러블이기 때문에 `for...of`문, 스프레드 문법, 배열 디스트럭처링 할당을 사용할 수 있다.
```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// Map 객체는 Map.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in map); // true

// 이터러블인 Map 객체는 for...of 문으로 순회할 수 있다.
for (const entry of map) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}

// 이터러블인 Map 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...map]);
// [[{name: "Lee"}, "developer"], [{name: "Kim"}, "designer"]]

// 이터러블인 Map 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, b] = map;
console.log(a, b); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
```


Map 객체는 **이터러블이면서 동시에 이터레이터인 객체**를 반환하는 메서드를 제공한다.

- `Map.prototype.keys` : 요소 키를 값으로 갖는다.
- `Map.prototype.values` : 요소 값을 값으로 갖는다.
- `Map.prototype.entries` : 요소 키와 요소 값을 값으로 갖는다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// Map.prototype.keys는 Map 객체에서 요소키를 값으로 갖는 이터레이터를 반환한다.
for (const key of map.keys()) {
  console.log(key); // {name: "Lee"} {name: "Kim"}
}

// Map.prototype.values는 Map 객체에서 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const value of map.values()) {
  console.log(value); // developer designer
}

// Map.prototype.entries는 Map 객체에서 요소키와 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const entry of map.entries()) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}
```