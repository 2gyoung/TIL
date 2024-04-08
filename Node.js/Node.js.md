# 1. Node.js의 정의와 특성
## 노드의 정의
Node.js = 크롬 V8 자바스크립트 엔진으로 빌드된 자바스크립트 런타임
>자바스크립트 런타임: 특정 언어로 만든 프로그램들을 실행할 수 있게 해주는 가상 머신(크롬 V8 엔진 사용)의 상태. 즉, 자바스크립트를 실행해 주는 실행 환경. 
>런타임의 대표적인 예: 웹 브라우저

노드를 통해 자바스크립트로 작성된 서버를 실행할 수 있어 서버의 역할도 수행한다. 자바스크립트 런타임이므로 플랫폼에 상관없이 자바스크립트 코드 기반이면 서버 뿐만 아니라 어떤 것이든 실행시킬 수 있어 웹, 모바일, 데스크탑 애플리케이션에도 사용된다. 

## 노드의 특성
Node.js는 `V8`과 `libuv`를 내부적으로 포함한다.
V8 엔진: 엔진의 속도 문제를 개선한 오픈 소스 자바스크립트 엔진. js 코드를 인터프리터 방식으로 해석하지 않고 즉시 기계어로 컴파일한다. 
libuv: 노트의 특징인 `이벤트 기반`, `논블로킹 입출력 모델`을 구현한 라이브러리.

### 이벤트 기반
이벤트 기반(event-driven)은 요청이 발생하는 이벤트를 처리하는 시스템. 
이벤트가 발생했을 때 미리 지정해둔 작업을 수행하는 방식으로, 
1) 이벤트 리스너가 직접 처리하거나,
2) 이벤트 리스너에 콜백 함수를 등록해 놓았다가 이벤트가 발생하면 콜백 함수가 호출되도록 한다.
#### 1. 호출 스택
함수의 호출, 자료구조의 스택. 함수의 호출 순서대로 쌓이고 역순으로 실행되는 LIFO(Last In First Out) 구조. 노드는 호출 스택 + 이벤트 루프로 돌아간다.
#### 2. 이벤트 루프
이벤트 루프: 이벤트 발생 시 호출할 콜백함수들을 관리하고, 호출할 순서를 결정
태스크 큐: 이벤트 발생 후 호출되어야 할 콜백함수들이 순서대로 기다리는 공간
백그라운드: 타이머나 I/O 작업 콜백, 이벤트 리스너들이 대기하는 공간. 여러 작업이 동시에 실행될 수 있다. 
![그림1-6](./Pasted%20image%2020240401175242.png)
![그림1-7](./Pasted%20image%2020240401175308.png)
![그림1-8](./Pasted%20image%2020240401180015.png)
호출 스택에 쌓인 이벤트(setTimeout)가 발생하면 그에 맞는 콜백 함수를 백그라운드로 보낸다. -> 백그라운드에서 타이머가 끝난(그 외 각종 I/O 작업) 콜백 함수는 태스크 큐로 보내진다. -> 호츨 스택에 쌓여있던 작업 실행이 끝나 호출 스택이 완전히 비워진 후에 콜백 함수가 호출 스택으로 올라간다. -> 콜백 함수가 호출 스택에서 실행된 후 호출 스택에서 나간다.

이 과정에서 호출 스택에 함수가 많이 있으면 실제로는 3초보다 더 대기하게 될 수 있어 타이머가 정확하진 않다.
### 논-블로킹 입출력
Node.js 동작 방식의 가장 큰 특징으로, 오래 걸리는 함수를 백그라운드로 보내 다음 코드가 먼저 실행되게 하고, 오래 걸리는 함수를 나중에 실행한다. 일부 코드는 백그라운드에서 병렬로 실행되어 시간적 이득을 얻는다. I/O 작업이 논 블로킹 방식으로, 나머지 코드는 블로킹 방식으로 실행되어 I/O 작업이 많을 때 활용성이 극대화된다. 
```javascript
let first = () => {
    //second(); // 제어권이 second() 함수로 넘어감
    setTimeout(second, 3000);
    console.log('첫번째');
} 

let second = () => {
    //third(); // 제어권이 third() 함수로 넘어감
    setTimeout(third, 5000);
    console.log('두번째');
}

let third = () => {
    console.log('세번째');
}

first();
```
본래대로라면 `세번째 > 두번째 > 첫번째` 순으로 출력되어야 하나 오래 걸리는 작업을 뒤로 미루고 실행하여 `첫번째 > 두번째 > 세번째` 순으로 출력된다.

### 프로세스와 스레드
프로세스 : 운영체제에서 할당하는 작업의 단위. 프로세스 간 자원 공유하지 X
	예: Zoom, VSCode, CMD 등 각각의 프로그램.
스레드 : 프로세스 내에서 실행되는 작업의 단위. 부모 프로세스의 자원 공유.
노드는 `멀티 스레드`이지만 직접 다룰 수 있는 스레드는 하나이므로 `싱글 스레드`라고 표현한다. 대신 `멀티 프로세스`를 활용한다. (노드 14버전부터 멀티 스레드 사용 가능)

싱글 스레드일 경우 주어진 일을 하나밖에 처리하지 못해 블로킹이 발생하면 나머지 작업이 전부 대기해야 하므로 비효율적이지만, 논 블로킹 모델을 채택해 일부 코드를 백그라운드(다른 프로세스)에서 실행한다.

각 특성별 비교

| 싱글 스레드            | 멀티 스레드                                              | 멀티 프로세싱              |
| ----------------- | --------------------------------------------------- | -------------------- |
| 프로그래밍 난이도 쉬움      | 프로그래밍 난이도 어려움                                       | 프로그래밍 비교적 쉬움         |
| CPU, 메모리 자원 적게 사용 | 스레드 수만큼 자원 많이 사용,<br>새 스레드 생성이나 놀고 있는 스레드 처리에 비용 발생 |                      |
| 에러를 처리하지 못할 경우 멈춤 | 에러 발생 시 새 스레드를 생성하여 극복                              |                      |
|                   | 하나의 프로세스 안에서 여러 개의 스레드 사용                           | 여러 개의 프로세스 사용        |
|                   | CPU 작업이 많을 때 사용                                     | I/O(입출력) 요청이 많을 때 사용 |
# 2. 자바스크립트 ES2015(ES6)
## 1. Const, let
`var`로 변수를 선언하던 이전과 다르게 `const`와 `let`이 대체하게 되었다.
`함수 스코프`인 `var`과 다르게 `const`와 `let`은 `블록 스코프`이다.
```javascript
if (true) {
    var num1 = 3;
}
console.log(num1);  // 3

if (true) {
    const num2 =3;
}
console.log(num2); // ReferenceError: num2 is not defined
```
함수 스코프는 function() {}이 스코프의 기준점이다.(if, for, while에는 영향 미치지 X) 즉, if문 등의 블록과 관계없이 접근이 가능하다.
블록 스코프는 함수 및 블록`{}`에도 별도의 스코프를 가진다. 중괄호로 묶여있는 해당 블록 안에서만 쓸 수 있고 블록 밖에서는 접근 불가능. `const`는 상수, `let`은 변수에 사용한다. (*즉, 자료형이 유연하던 이전 var와 달리 어느 정도 제한을 준 것*)
javascript에서 한 번 초기화했던 변수에 다른 값을 할당하는 경우는 의외로 적으므로 기본적으로 const를 사용하고, 다른 값을 할당해야 하는 상황이 생겼을 때 `let`을 사용하면 된다.

## 2. 템플릿 문자열
문자열을 합칠 때 `+ 기호` 대신 `백틱`을 사용해 가독성을 높이고, `${변수}`를 사용한다.
```javascript
var num1 = 1;
var num2 = 2;
var sum = num1 + num2;
var str = num1 + '더하기' + num2 + '는? "' + sum + '"'
console.log(str);

let str2 = `${num1} 더하기 ${num2}는 "${sum}"`
console.log(str2);
```

## 3. 객체 리터럴
이전에 비해 간결한 문법으로 객체 리터럴을 표현할 수 있게 되었다.
- 객체의 메서드에 `:function`을 붙이지 않아도 된다.
- { sayNode: sayNode }를 {sayNode}로 축약 가능하다.
- `변수 + 값` 등으로 동적 속성명을 객체 속성명으로 사용할 수 있다.
ES2015 전
```javascript
var sayHello = function () {
    console.log('안녕하세요');
}
var subject = "node";
var person = {
    sayBye : function ()  {
        console.log('안녕히 가세요');
    },
    sayHello : sayHello,
    subject : subject,
}

person.sayHello();
person.sayBye();
console.log(person.subject);
person[subject + "Info"]= '노드는 자바스크립트 런타임';
=> person["nodeInfo"]= '노드는 자바스크립트 런타임'; 이렇게도 사용 가능
console.log(person.nodeInfo);
```
ES2015+ 이후
```javascript
let sayHello = function () {
    console.log('안녕하세요');
}
let subject = "node";
let person = {
    sayBye : function ()  {
        console.log('안녕히 가세요');
    },
    sayHello,
    subject,
    [subject + 'Info'] : '노드는 자바스트립트 런타임!'
}

person.sayHello();
person.sayBye();
console.log(person.subject);
console.log(person.nodeInfo);
```

## 4. 화살표 함수
- 함수의 본문이 return만 있는 경우 return 생략 가능. return이 생략될 경우 본문을 소괄호`()`로 감싸줄 수 있음.
```javascript
== 기존 function 함수 ==
function add1(x,  y){
    return x + y
}

== 화살표 함수 ==
let add3 = (x, y) => {
    return x + y;
}
```
- 매개변수가 한 개일 때 괄호 생략
- 화살표 함수에서 `this` 사용 시 자신을 포함하는 함수의 `this`를 물려받음. => 이 때는 기존의 `function() {}`을 사용.
## 5. 구조분해 할당
- `const{ 변수 } = 객체`로 객체 안의 속성을 변수명으로 사용할 수 있으며, 속성 안의 속성도 사용 가능함.
- 배열도 구조분해 할당이 가능해졌다.
```javascript
const person = {
    status: {
        name: '짱구',
        age: 5
    },
    getAge() {
        this.status.age++
        return this.status.age;
    }
};

== 과거의 경우 하나하나 할당해줘야 했음 ==

var getAge = person.getAge;
var name = person.status.name;
var age = person.status.age;

== 속성을 찾아 들어가지 않아도 된다 ==

const{getAge, status: {name, age}} = person;


console.log(name, age, getAge);
```
## 6. 클래스
- class 내부에 관련된 코드들이 묶이고 그룹화되어 가독성이 향상되었다.
- Constructor(생성자), Extends(상속) 등을 깔끔하게 처리할 수 있다.
- Super로 부모 클래스 호출
- Static 키워드로 클래스 메서드 생성

## 7. 프로미스
- 콜백이 계속 중첩되던 문제를 해결.
- `promise` : 내용이 실행은 되었지만 결과를 아직 반환하지 않은 객체로, `then`을 붙이면 결과 반환. 실행이 완료되지 않았으면 완료된 후 `then` 내부 함수 실행.
- `Resolve`: 성공리턴값. `then`으로 연결.
- `Reject`: 실패리턴값. `catch`로 연결.
- `Finally`: 무조건 실행되는 부분
- `프로미스 체이닝`: `promise`의 `then`을 연달아 사용할 수 있다. `then` 안에서 return한 값이 다음 `then`으로 넘어가며, return 값이 `promise`일 경우 `resolve` 후 넘어간다. 에러가 발생하면 바로 `catch`로 이동시켜 한 번에 처리한다.

## 8. async / await
- `async 함수`을 도입해 `promise` 패턴 코드를 한 번 더 축약 가능하다.
- 에러 처리를 위해선 각각 `try`, `catch`로 감싸주어야 한다.
- 화살표 함수도 async/await 사용 가능.
- `async 함수`는 항상 `promise`를 return한다. 이 때 `then`이나 `await`을 붙일 수 있다.

## 9. Map / Set
- `map`은 객체와 유사한 자료구조
- `set`은 배열과 유사한 자료구조. 기존 배열의 중복을 제거할 때도 사용한다.

## 10. 널 병합, 옵셔널 체이닝
**??(널 병합, nullish coalescing) 연산자**
`||` 대용으로 사용되며, falsy 값 중 `null`과 `undefined`만 따로 구분한다.

**?.(옵셔널 체이닝, optional chaining) 연산자**
`null`이나 `undefined`의 속성을 조회할 때 에러가 발생하는 것을 막는다.

# FrontEnd 자바스크립트
## 1. Axios
서버로 요청을 보내는 코드. 라이브러리가 없다면 `XMLHttpRequest` 객체 이용.
### GET 요청 보내기
- `axios.get`함수의 인수로 요청을 보낼 주소 삽입.
- `promise`기반 코드이므로 `async/await`사용 가능함.
```javascript
axios.get('https://koreanjson.com/users/')
.then((res) => {
	console.log(res.data);
     })
     .catch((err) => {
         console.error(err.message);
     });

        (async () => {
            try {
                const res = await axios.get('https://koreanjson.com/users/')
                console.log(res.data);
            }   catch(err) {
                console.error(err);
            }
        })();
```
### POST 요청 보내기
- 전체적인 구조는 GET 요청을 보내는 것과 비슷하지만 두 번째 인수로 데이터를 포함한다.
```javascript
(async () => {
            try {
                const res = await axios.post('https://koreanjson.com/users/', {
                    "name": "이주희",
                    "username": "jh1122",
                    "email": "lee.jh@gmail.com",
                    "phone": "010-1234-5678",
                    "website": "https://leejuhee.com",
                    "province": "",
                    "city": "서울특별시",
                    "district": "성북구",
                    "street": "성북로 123",
                    "zipcode": "02879",
                })
                console.log(res.data);
            }   catch(err) {
                console.error(err);
            }
        })();
```
## 2. FormData
HTML form 태그에 담긴 데이터를 `Axios` 요청으로 보내고 싶은 경우 `FormData 객체`를 이용한다.
### FormData 메서드 목록
- `Append` : 데이터 하나씩 추가
- `Has` : 데이터 존재 여부 확인
- `Get` : 데이터 조회
- `getAll` : 데이터 모두 조회
- `delete` : 데이터 삭제
- `set` : 데이터 수정
### FormData POST 요청으로 보내기
`Axios`의 `data` 자리에 `FormData`를 넣는다.

```javascript
        (async () => {
            try {
                const res = await axios.post('https://koreanjson.com/users/', {
                    "name": "이주희",
                    "username": "jh1122",
                    "email": "lee.jh@gmail.com",
                    "phone": "010-1234-5678",
                    "website": "https://leejuhee.com",
                    "province": "",
                    "city": "서울특별시",
                    "district": "성북구",
                    "street": "성북로 123",
                    "zipcode": "02879",
                })
                console.log(res.data);
            }   catch(err) {
                console.error(err);
            }
        })();
```
## 3. encodeURIComponent, decodeURIComponent
가끔 주소창에 한글을 입력하면 서버가 처리하지 못하는 경우가 발생하는데, 이때 `encodeURIComponent`로 한글을 감싼 뒤 처리한다. 
예시)
1. 단어 '노드'를 `encodeURIComponent`를 이용해 '%EB%85%B8%EB%93%9C'로 만들어 서버에 보낸다.
2. `decodeURIComponent`로 서버에서 '%EB%85%B8%EB%93%9C'를 한글 '노드'로 해석한다. 

## 4. data attribute와 dataset
- HTML 태그에 데이터를 저장하는 방법으로, 서버의 데이터를 프론트엔드로 내려줄 때 사용한다.
- 태그 속성으로 `data- 속성명`을 사용한다.
- 자바스크립트에서 `태그.dataset.속성명`으로 접근 가능하다.
	- 예시
		- `data-user-job` => `dataset.userJob`
		- `data-id` => `dataset.id`
- 자바스크립트에서 `dataset`에 값을 넣으면 `data-속성`이 생긴다.
	- 예시
		- `dataset.monthSalary = 10000` => `data-month-salary = '10000'` 추가

# 3. Node.js의 기능
## 1. REPL
자바스크립트는 스크립트 언어이므로 즉석에서 코드를 실행할 수 있는데, 이 때 REPL이라는 콘솔을 제공한다. 윈도우에서는 cmd, 리눅스에서는 터미널에 `node`를 입력하여 실행한다.
> R(Read), E(Evaluate), P(Print), L(Loop)

입력한 값의 결과값이 바로 출력되어 간단한 코드를 테스트하기 적합하며, 긴 코드를 입력하기에는 부적합하다. 
## 2. 모듈
`모듈`은 특정한 기능을 하는 함수나 변수들의 집합으로, 노드는 자바스크립트 코드를 모듈로 만들 수 있다. 모듈로 만들게 되면 여러 프로그램에서 필요한 기능만 재사용할 수 있다.
1. 자바스크립트 파일 끝에 `module.exports`로 모듈로 만들 값을 지정한다.
2. 다른 파일에서 `require(파일 경로)`로 해당 모듈의 내용을 가져올 수 있다.

```javascript
const odd = '홀수입니다'; 
const even = '짝수입니다'; 

== exports 지정 ==
module.exports = { odd, even, };
```

```javascript
== require로 모듈 불러옴 ==
const {odd, even} = require('./var');

exports.checkOddOrEven = (num) => {
    if (num % 2 ) {
        return odd
    } else {
        return even;
    }
}
```
### module, exports
`module.exports`와 `exports`는 참조 관계이므로 `module.exports` 외에도 `exports`로 모듈을 만들 수 있다.
```javascript
const odd = '홀수입니다'; 
const even = '짝수입니다'; 

== exports로 변경 ==
exports.odd='홀수입니다.';
exports.even = '짝수입니다.';
```
### this
- 최상위 스코프의 `this`는 `module.exports`를 뜻한다.
- 그 외에는 브라우저의 자바스크립트와 동일함.
- 함수 선언문 내부의 `this`는 `global(전역) 객체`를 뜻한다.