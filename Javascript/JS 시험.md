자바스크립트 원시형, 기본형 [string, boolean, number, undefined, null, symbol]
객체형 [object, function, array]

```
let score = 10

function add() {
	let score = 20 <= 함수 내에서 지역변수로 선언한 것이므로 글로벌 score에 영향 없음
}

add();

console.log(score); // 10
```

조건
if - else
switch - case (break)
삼항연산자 ?:
```
let 평가 = a > 10? "a" : "b"
```

템플릿리터럴 ${}
코드 안에 선언된 변수 관리를 위해

화살표 함수 
function이 없다. 

웹페이지 구조 DOM을 나타내느 요소
document.getElementById()
document.getElementByClass() : 이름
document.getElementByTagName() : 태그 이름
document.getElementByqueryselector()

버블링 : 가장 안쪽에서 바깥쪽으로 이벤트 전파
피쳐링 : ?

Map, Set
map = ?
set = 중복 불가능

fetch API에서 요청을 보낼 때는 fetch() 이용

논리연산자는 &&(and), ||(or) 등

```
console.log(12 == "12")
console.log(12 === "12") <- 타입까지 검사
```

...[배열이름] : 배열을 여러 요소로 분리하거나 하나의 배열로 합칠 때 사용한다