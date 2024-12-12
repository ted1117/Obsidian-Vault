```table-of-contents
```

## Admin
### model 등록
1. 먼저 모델 생성
2. admin.py에 model을 import 한 다음, ```admin.site.register(모델이름)```
### 검색 기능 추가
```python
# admin.py
from django.contrib import admin
from .models import Manga

class MangaAdmin(admin.ModelAdmin):
	search_fields = ["title"]

admin.site.register(Manga, MangaAdmin)
```
---
