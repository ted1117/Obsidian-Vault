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

### 사용법 정리

|                 | 기능                       | data= 입력           | data= 미입력 |
| --------------- | ------------------------ | ------------------ | --------- |
| is_valid()      | serializer 검증            | 가능                 | 불가능       |
| .initial_data   | 초기 data 반환               | 가능                 | 불가능       |
| .validated_data | 검증된 data 반환              | is_valid() 호출 후 사용 | 불가능       |
| .errors         | 검증 중 발생한 error           | (상동)               | 불가능       |
| .data           | serializer로 직렬화된 data 반환 | (상동)               | 가능        |


## 노출되는 필드의 자료형을 바꾸는 법
```python
# models.py
class Post(models.Model):
	category = models.ForeignKey(
	"Category", on_delete=models.SET_NULL, blank=True, null=True
	)
	tags = models.ManyToManyField("Tag", blank=True)
	title = models.CharField("TITLE", max_length=50)
	description = models.CharField(
	"DESCRIPTION", max_length=100, blank=True, help_text="simple one-line text."
	)
	image = models.ImageField("IMAGE", upload_to="blog/%Y/%m/", blank=True, null=True)
	content = models.TextField("CONTENT")
	create_dt = models.DateTimeField("CREATE DT", auto_now_add=True)
	update_dt = models.DateTimeField("UPDATE DT", auto_now=True)
	like = models.PositiveSmallIntegerField("LIKE", default=0)  

	class Meta:
		ordering = ("update_dt",)

	def __str__(self):
		return self.title

class Category(models.Model):
	name = models.CharField(max_length=50, unique=True)
	description = models.CharField(
		"DESCRIPTION", max_length=100, blank=True, help_text="simple one-line text."
	)

def __str__(self):
	return self.name
```
Post의 category 필드는 Category를 참조한다. 당연히 Category의 id를 참조하기 때문에 id가 아니라 이름을 쓰기 위해선 Category의 name 필드를 이용해야 한다.
```python
class PostListSerializer(serializers.ModelSerializer):
	category = serializers.CharField(source="category.name")
	
	class Meta:
		model = Post
		fields = ["id", "title", "image", "like", "category"]
```
PostListSerializer에서 serializers.CharField를 이용한다. 이때 source 인자는 Post의 category 필드이름을 쓰고 점 뒤에 참조하는 모델의 필드명을 적는다.

## 검증
### Field-level validation
#### def validate_(field)(self, value):
```python
#serializers.py
from rest_framework import serializers

class AnimalSerializer(serializers.Serializers):
	name = serializers.CharField(max_length=50)
	info = serializers.CharField()

	def validate_name(self, value):
		if not (value.isalpha()):
			raise serializers.ValidationError("Animal names must be alphabetic only.)
		return value
```

### Object-level validation
#### def validate(self, data):
```python
#serializers.py
from rest_framework import serializers

class AnimalSerializer(serializers.Serializers):
	name = serializers.CharField(max_length=50)
	info = serializers.CharField()

	def validate(self, data):
		if len(data["name"]) > len(data["info"]):
			raise serializers.ValidationError("Info is too short!")
		return data
```

### Validators
```python
from rest_framework import serializers

def name_is_alphabetic(value):
	if not (value.isalpha()):
		raise serializers.ValidationError("Animal names must be alphabetic only.")

class AnimalSerializer(serializers.Serializer):
	name = serializers.CharField(max_length=10, validators=[name_is_alphabetic])
```

## Partial updates
- partial=True 인자 사용

## Multiple objects
- 단일 오브젝트 인스턴스가 아니라 여러 개를 처리할 때, many=True 사용

