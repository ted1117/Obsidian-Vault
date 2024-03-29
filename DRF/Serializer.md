```table-of-contents
title: 
style: nestedList # TOC style (nestedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
debugInConsole: false # Print debug info in Obsidian console
```
- [[#Form vs. Model|Form vs. Model]]
- [[#직렬화와 역직렬화|직렬화와 역직렬화]]
	- [[#직렬화와 역직렬화#메모리 내부와 외부|메모리 내부와 외부]]
	- [[#직렬화와 역직렬화#복원 시 정보 유지|복원 시 정보 유지]]
	- [[#직렬화와 역직렬화#JSON|JSON]]
	- [[#직렬화와 역직렬화#과정|과정]]
		- [[#과정#직렬화|직렬화]]
		- [[#과정#역직렬화|역직렬화]]

## Form vs. Model
Form^[Django에서 HTML의 form을 다루기 위함]과 Model^[DB의 테이블을 다루기 위함]은 직렬화와 유효성 확인 기능을 모두 제공하기 때문에 둘은 비슷함. 그리고 DRF에선 Serializer가 이 역할을 대신함.

---
## 직렬화와 역직렬화
### 메모리 내부와 외부
프로그램을 실행하면 코드가 메모리 위로 올라감. '홍길동'을 메모리 내부에서 외부 장치로 전송할 때 메모리 내외의 환경이 다르기 때문에 변환해야 함.
### 복원 시 정보 유지
예를 들어 아래와 같은 코드가 있다.
```python
class Person:
    name = 'Yang'
    age = 25
```
클래스 Person이 파일로 저장됐다가 다시 복원될 때, name과 age가 Person의 변수임, name과 age가 각각 Person에 속한 string과 integer라는 것 등의 정보가 그대로 담겨있어야 함.
### JSON
JSON은 자료형만 변환하는 수준으로 (역)직렬화함. 허용하는 자료형도 Number, String, Object, List, Null 정도만 사용. Python의 Dict 자료형과 비슷한 모습.
### 과정
#### 직렬화
직렬화: c0 -> dict -> bytes(바이트스트링) -> 클라이언트에 전송
Read Operation: GET
#### 역직렬화
직렬화의 역순.
클라이언트에게 받은 데이터를 Serializer에 넣음
```python
# 이때 인자가 instance가 아니라 data임에 주목
dsr = CommentSerializer(data=ddata0)

# 클라이언트에게 받은 데이터에 오류 여부를 확인하는 과정 필요
dsr.is_valid()
```

유효성 확인을 통과한 데이터는 validated_data에 담김 (dsr.validated_data)
```python
instance = Comment(**dsr.validated_data)
instance.save()    # DB에 저장
```
Write Operation: POST UPDATE DELETE PATCH