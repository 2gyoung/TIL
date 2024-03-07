## 리스트
### sort()와 sorted()의 차이
**sort()**
리스트에 내장된 메서드(함수) 중 하나. return값이 None이다. 리스트 자체의 순서를 바꾼다.
사용 예: `sample.sort()`
**sorted()**
외장함수. 리스트를 return한다.
사용 예: `sorted(sample)`
## 함수
### 함수의 파라미터
함수의 파라미터 순서 = 일반 변수, `args`, `kwargs`
	순서 어기면 에러
#### 매개변수
##### 포지셔널 매개변수 (positional) argument = `args` 
인자를 ','로 구분하여 위치로 받는 포지셔널 아규먼트(args)를 몇 개나 받을지 알 수 없을 때, 매개변수에 `*`을 붙여 하나의 `tuple` 형태로 묶어 args에 할당한다.

##### 키워드 매개변수 keyword argument = `kwargs`
키워드 아규먼트(kwargs)를 몇 개나 받을지 알 수 없을 때, 매개변수에 `**`을 붙여 하나의 `dictionary` 형태로 묶어  kwargs에 할당한다.
`key값 = 'value값'`에서 key값에는 따옴표('')가 들어가면 안 됨

### 리턴
- return (값): (값)을 반환한다.
- return (빈 값) = 생략하는 경우 : None을 반환한다.
- return이 아예 없는 경우 : None을 반환한다.

### Lambda 함수
Javascript의 화살표 함수와 비슷하다.
JS의 화살표 함수 : 주로 콜백 함수에서 사용
```javascript
function 함수명(매개변수, 매개변수 ) {}

함수명 (매개변수, 매개변수) ⇒ {}
```

Python의 람다 함수 : 주로 정렬할 때 사용. map(), filter() 등과 같이 사용
```python
def 함수명(매개변수, 매개변수):

함수명 = lambda 매개변수, 매개변수:
```
## 파일 입/출력
### 파일 생성
`open()`은 항상 `close()`와 함께 쓴다
```python
f = open("note.txt", '모드')
f.close()
```

| 파일 모드 | 설명                                          |
| ----- | ------------------------------------------- |
| r     | 읽기 모드 : 읽기만 할 때. 기본값                        |
| w     | 쓰기 모드 : 내용 입력. 파일이 이미 존재하면 파일 내용을 비우고 새로 입력 |
| x     | 쓰기 모드 : 내용 입력. 파일이 이미 존재하면 에러 발생            |
| a     | 추가 모드 : 내용 추가. 파일이 이미 존재하면 마지막에 이어서 입력      |
### 파일 쓰기
`write()`로 내용 입력. 반복문을 사용할 수도 있다
```python
f = open("hello10.txt", 'w') 
for i in range(10):
	f.write("Hello, world{}!\n".format(i+1)) 
f.close()
```
### 파일 읽기
1. `readline()`: 기본적으로 한 라인씩 출력하다가 더 이상 받아올 라인이 없다면 None을 출력한다.
```python
f = open("hello.txt", "r", encoding='utf-8')

while True: # 즉, 무한루프동안 라인을 출력하다가
    line = f.readline()
    if line:    # line == (값) != 0 == true
        print(line)
    else:   # 더 이상 출력할 라인이 없어 none을 출력하게 되면 무한루프 종료
        break

f.close()
```
2. `read()`: 파일의 내용 전체를 가져와서 시간 소모가 좀 더 크다.
```python
f = open('hello.txt', 'r', encoding='utf-8')

data = f.read()

print(data)

f.close()
```
3. `for line in file`: 반복문과 파일 객체를 함께 사용한다.
```python
f = open('hello.txt', 'r', encoding='utf-8')

for line in f:
    print(line.strip())    # strip()은 줄바꿈 때문에

f.close()
```
### 파일에 내용 추가하기
```python
with open('with.txt', 'w', encoding='utf-8') as f:
    f.write("현재 블록을 벗어나는 순간, f는 자동으로 close 됩니다.")
```
