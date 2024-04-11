
## 개요
ListSerializer는 다수의 객체를 한 번에 직렬화하고 검증하기 위해 사용된다. 보통 사용자가 직접 사용할 필요는 없으며, 시리얼라이저를 인스턴스화 할 때 ```many=True```를 전달하면 된다.
시리얼라이저가 인스턴스화 되고 ```many=True```를 전달하면, ListSerializer 인스턴스가 생성된다. 그 후에 시리얼라이저 클래스가 부모 ListSerializer의 자식이 된다.
다음의 인자가 ListSerializer 혹은 ```many=True```가 전달되는 시리얼라이저 내부에서 사용된다.
* ```allow_empty```
	* True가 기본값이나 빈 리스트를 입력할 때 False로 지정할 수 있다.
* ```max_length```
	* None이 기본값으로, 검증하고 싶은 요소의 수만큼 정수로 지정할 수 있다.
* ```min_length```
	* None이 기본값으로, 검증하고 싶은 요소의 최소 수만큼 정수로 지정할 수 있다.
### 커스터마이징
사용자는 다음과 같이 ListSerializer를 커스터마이징 할 수 있다.
* 리스트 내부에서 요소끼리 충돌 여부를 확인하는 등의 특정 검증 과정
* 다수의 객체를 생성하거나 업데이트
이러한 경우, Meta 클래스에서 ```list_serializer_class``` 옵션을 사용하여 ```many=True```가 전달된 클래스를 수정할 수 있다.
```python
class CustomListSerializer(serializers.ListSerializer):
	...

class CustomSerializer(serializers.Serializer):
	class Meta:
		list_seriliaer_class = CustomListSerializer
```
