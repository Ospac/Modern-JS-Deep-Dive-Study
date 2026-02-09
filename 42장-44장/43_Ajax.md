# 43장 Ajax
## 43.1 Ajax란?
Ajax<sup>Asynchronous Javascript and XML</sup>란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 *웹페이지를 동적으로 갱신하는 프로그래밍 방식*을 말한다.
- WebAPI인 `XMLHttpRequest` 객체를 기반으로 동작한다.
- 2005년에 발표된 구글 맵스는 Ajax를 적극 활용한 대규모 웹 어플리케이션
- 웹페이지 변경에 필요한 데이터만 비동기 방식으로 전송 받아, 필요한 부분만 한정적으로 다시 렌더링하는 방식을 사용한다.
	- 비동기 방식이라 블로킹이 없다.
	- 일부분만 렌더링하기 때문에 페이지 전체가 깜박이는 현상이 없다.
<img width="511" height="380" alt="43-2" src="https://github.com/user-attachments/assets/0bc7a3bb-8ac3-4abf-9fbc-ccb0e42027ca" />

전통적인 웹페이지들은 완전한 HTML을 서버로부터 받아 페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했다.

<img width="576" height="492" alt="43-1" src="https://github.com/user-attachments/assets/6a741162-9b9c-44b5-a5f3-fb883999f5a8" />


- 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 받기 때문에 불 필요한 데이터 통신 발생
- 브라우저도 변경할 필요가 없는 부분까지 처음부터 다시 렌더링한다.
- 클라이언트-서버 통신이 동기 방식으로 동작하기 때문에, 다음 처리가 블로킹된다.

## 43.2 JSON
JSON<sup>Javascript Object Notation</sup>은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다. JS에 종속되지 않아 대부분의 프로그래밍 언어에서 사용할 수 있다.

### 43.2.1 JSON 표기 방식

자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 텍스트다.
- 키는 반드시 큰 따옴표로 묶어야 한다.
- 값은 객체 리터럴과 같은 표기법으로 사용할 수 있다. (문자열은 마찬가지로 큰 따옴표가 필수)
```js
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```
### 43.2.2 JSON.stringify

클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 직렬화<sup>serializing</sup>라 한다.
`JSON.stringify` 메서드는 객체를 JSON 포맷의 문자열로 변환한다. 


>[!NOTE] Mdn
>```js
> JSON.stringify(value[, replacer[, space]])
> ```
>`value`
> - JSON 문자열로 변환할 값.
>
> `replacer` Optional
> - 문자열화 동작 방식을 변경하는 함수, 혹은 JSON 문자열에 포함될 값 객체의 속성들을 선택하기 위한 화이트리스트(whitelist)로 쓰이는 `String` 과 `Number` 객체들의 배열. 이 값이 null 이거나 제공되지 않으면, 객체의 모든 속성들이 JSON 문자열 결과에 포함된다.
> 
> `space` Optional
> - 가독성을 목적으로 JSON 문자열 출력에 공백을 삽입하는데 사용되는 [`String`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String) 또는 [`Number`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Number) 객체. 이것이 `Number` 라면, 공백으로 사용되는 스페이스(space)의 수를 나타낸다; 이 수가 10 보다 크면 10 으로 제한된다. 1 보다 작은 값은 스페이스가 사용되지 않는 것을 나타낸다. 이것이 `String` 이라면, 그 문자열(만약 길이가 10 보다 길다면, 첫번째 10 개의 문자)이 공백으로 사용된다. 이 매개 변수가 제공되지 않는다면(또는 null 이면), 공백이 사용되지 않는다.
> 
> 
> **반환 값**
> 주어진 값과 대응하는 JSON 문자열
> 
> https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify

### 예시
```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);
console.log(typeof json, json);
// string {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}

// 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 한다.
const prettyJson = JSON.stringify(obj, null, 2);
console.log(typeof prettyJson, prettyJson);
/*
string {
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/

// replacer 함수. 값의 타입이 Number이면 필터링되어 반환되지 않는다.
function filter(key, value) {
  // undefined: 반환하지 않음
  return typeof value === 'number' ? undefined : value;
}

// JSON.stringify 메서드에 두 번째 인수로 replacer 함수를 전달한다.
const strFilteredObject = JSON.stringify(obj, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);
/*
string {
  "name": "Lee",
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/
```
### 43.2.3 JSON.parse

서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이다. 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체화해야 하는데 이를 역직렬화<sup>deserializing</sup>라 한다.`JSON.parse` 메서드는 JSON 포맷의 문자열을 객체로 변환한다.
- 배열이 JSON 포맷의 문자열로 변환된 경우, 문자열을 배열 겍체로 변환한다. 배열 요소가 객체인 경우 배열의 요소까지 객체로 변환한다.

>[!NOTE] Mdn
>```js
> JSON.parse(text[, reviver])
> ```
>`text`
> - JSON으로 변환할 문자열
>
> `replacer` Optional
> - 함수라면, 변환 결과를 반환하기 전에 이 인수에 전달해 변형함.
> 
> **반환 값**
> 주어진 JSON 문자열에 대응하는 Object
> 
> https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse

```js
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false }
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos);

// JSON 포맷의 문자열을 배열로 변환한다. 배열의 요소까지 객체로 변환된다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
/*
 object [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false }
]
*/
```
## 43.3 XMLHttpRequest

브라우저는 주소창이나 `form`, `a`태그를 통해 HTTP 요청 전송 기능을 기본 제공한다.
자바스크립트를 사용하여 HTTP 요청을 전송할때는 WebAPI인 `XMLHttpRequest`객체를 사용한다. 
- 해당 객체는 HTTP 요청 전송과 응답 수신을 위한 메서드와 프로퍼티를 제공한다.

### 43.3.1  XMLHttpRequest 객체 생성
`XMLHttpRequest` 생성자 함수를 호출하여 생성한다. WebAPI이므로 브라우저 환경에서만 동작한다.
```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();
```

### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드

### 43.3.3 HTTP 요청 전송

1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화한다.
2. `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값을 설정한다.
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송한다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('GET', '/users');

// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```

```js
xhr.open(method, url[, async])
```
`open`메서드는 서버에 전송할 HTTP요청을 초기화 한다.
- `method`: HTTP 요청 메서드("GET", "POST", "PUT", "DELETE" 등)
- `url`: HTTP 요청을 전송할 URL
- `async`: 비동기 요청 여부, 기본 값은 `true` 

```js
xhr.send(JSON.stringfy({id:1, content:'html', completed: false}));
```
`send` 메서드는 `open`메서드로 초기화된 HTTP 요청을 서버에 전송한다. 
- GET 메서드의 경우 데이터를 URL의 일부분인 쿼리 문자열<sup>query string</sup>로 서버에 전송한다.
	- 페이로드로 전달한 인수는 무시되고 몸체<sup>request body</sup>는 `null`로 설정된다.
- POST 메서드의 경우 데이터(페이로드)를 몸체<sup>request body</sup>에 담아 전송한다.  
    페이로드가 객체인 경우 반드시 `JSON.stringfy` 메서드를 사용하여 직렬화한 다음 전달해야 한다.

```js
// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');
```

`setRequstHeader` 메서드는 특정 HTTP 요청의 헤더 값을 설정한다.  
- 반드시 `open` 메서드를 호출한 이후에 호출해야한다.  
- 자주 사용하는 HTTP 요청 헤더에는 `Content-type`과 `Accept`가 있다.

`Content-type`은 request body에 담아 전송할 페이로드의 MIME 타입의 정보를 표현한다.
`Àccept`헤더 값은 서버가 응답할 데이터의 MIME 타입을 지정할 수 있다.
	- 설정하지 않으면 `send` 메서드가 호출될 때 `Accept` 헤더가 `*/*`으로 전송된다.

**MIME 타입 종류**
- text: `text/plain`, `text/html`, `text/css`, `text/javascript`
- application: `appication/json`, `application/x-www-form-urlencode`
- multipart: `multipart/fored-data`

### 43.3.4 HTTP 응답 처리

서버가 전송한 응답을 처리하려면 `XMLHttpRequest` 객체가 발생시키는 `readystatechange` 이벤트를 캐치해야 한다.
- `readystatechange`이벤트는 HTTP 요청의 현재 상태를 나타내는 `readyState`프로퍼티 값이 변경된 경우 발생한다.
- 해당 이벤트 핸들러에서, `readyState` 값을 체크하고, `status` 프로퍼티 값을 체크하여 응답을 체크한다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// https://jsonplaceholder.typicode.com은 Fake REST API를 제공하는 서비스다.
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

// readystatechange 이벤트는 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티가
// 변경될 때마다 발생한다.
xhr.onreadystatechange = () => {
  // readyState 프로퍼티는 HTTP 요청의 현재 상태를 나타낸다.
  // readyState 프로퍼티 값이 4(XMLHttpRequest.DONE)가 아니면 서버 응답이 완료되지 상태다.
  // 만약 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않는다.
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  // status 프로퍼티는 응답 상태 코드를 나타낸다.
  // status 프로퍼티 값이 200이면 정상적으로 응답된 상태이고
  // status 프로퍼티 값이 200이 아니면 에러가 발생한 상태다.
  // 정상적으로 응답된 상태라면 response 프로퍼티에 서버의 응답 결과가 담겨 있다.
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
    // {userId: 1, id: 1, title: "delectus aut autem", completed: false}
  } else {
    console.error('Error', xhr.status, xhr.statusText);
  }
};
```

>[!NOTE] onLoad 이벤트 핸들러 사용하기
>`load` 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생한다.
>따라서 `load`이벤트를 캐치하는 경우 `xhr.readyState`가 `XMLHttpRequest.DONE`인지 확인할 필요가 없다.
