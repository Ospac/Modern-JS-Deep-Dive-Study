# 44장 REST API

REST<sup>REpresentational State Transfer</sup>는 아파치 HTTP 서버 프로젝트의 공동 설립자인 로이 필딩<sup>Roy Fielding</sup> 이 HTTP의 장점을 최대한 활용할 수 있는 아키텍처로서 소개했다.
- REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처
- REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미
- REST의 기본 원칙을 성실히 지킨 서비스 디자인을 "RESTful"이라고 표현
## 44.1 REST API의 구성
REST API는 자원<sup>resource</sup>, 행위<sup>verb</sup>, 표현<sup>representations</sup>의 3가지 요소로 구성된다.
REST 구조는 REST API만으로 HTTP 요청의 내용을 이해할 수 있다.

| **구성** **요소**       | **내용**            | **표현** **방법** |
| ------------------- | ----------------- | ------------- |
| 자원(resource)        | 자원                | URI(엔드포인트)    |
| 행위(verb)            | 자원에 대한 행위         | HTTP 요청 메서드   |
| 표현(representations) | 자원에 대한 행위의 구체적 내용 | 페이로드          |

## 44.2 REST API 설계 원칙

1. **URI는 리소스를 표현하는 데 집중 (명사형으로 작성)**
```shell
# bad
GET /getTodos/1
GET /todos/show/1

# good
GET /todos/1
```

2. **리소스 행위에 대한 정의는 HTTP 요청 메서드를 통해 하는 것**

| HTTP 요청 메서드 | 종류             | 목적           | 페이로드 |
| ----------- | -------------- | ------------ | ---- |
| GET         | index/retrieve | 모든/특정 리소스 취득 | X    |
| POST        | create         | 리소스 생성       | O    |
| PUT         | replace        | 리소스 전체 교체    | O    |
| PATCH       | modify         | 리소스 일부 수정    | O    |
| DELETE      | delete         | 모든/특정 리소스 삭제 | X    |

## 44.3 JSON Server를 이용한 REST API 실습


### GET 요청
```js
 // XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// todos 리소스에서 모든 todo를 취득(index)
xhr.open('GET', '/todos');

// HTTP 요청 전송
xhr.send();

// load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
xhr.onload = () => {
  // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
  if (xhr.status === 200) {
	document.querySelector('pre').textContent = xhr.response;
  } else {
	console.error('Error', xhr.status, xhr.statusText);
  }
};
```

### POST 요청
```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// todos 리소스에 새로운 todo를 생성
xhr.open('POST', '/todos');

// 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
// 새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 한다.
xhr.send(JSON.stringify({ id: 4, content: 'Angular', completed: false }));

// load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
xhr.onload = () => {
  // status 프로퍼티 값이 200(OK) 또는 201(Created)이면 정상적으로 응답된 상태다.
  if (xhr.status === 200 || xhr.status === 201) {
	document.querySelector('pre').textContent = xhr.response;
  } else {
	console.error('Error', xhr.status, xhr.statusText);
  }
};
```
