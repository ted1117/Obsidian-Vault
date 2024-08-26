목차

## 준비
### 패키지 설치
```shell
pip install django
pip install djangorestframework

pip install celery
pip install django-celery-beat

pip install redis
```

```shell

docker pull redis

docker run --name test-redis -p 6379:6379 -d redis

docker exec -it redis /bin/bash
```

### django 프로젝트 생성
```shell
django-admin startproject config .
python manage.py startapp api
```

### django 파일 설정
```python
# config/settings.py

INSTALLED_APPS = [
	...
	"rest_framework",
	"django_celery_beat",
	"api.apps.ApiConfig",
]

...

# Redis를 브로커로 사용하는 경우  
CELERY_BROKER_URL = 'redis://127.0.0.1:6379/0'  
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'  
  
# Celery Configuration Options  
CELERY_TIMEZONE = "Asia/Seoul"  
CELERY_TASK_TRACK_STARTED = True  
CELERY_TASK_TIME_LIMIT = 30 * 60  
  
# 작업 대기열 이름 설정  
CELERY_TASK_DEFAULT_QUEUE = 'default'  
CELERY_TASK_QUEUES = {  
    'default': {  
        'exchange': 'default',  
        'routing_key': 'default',  
    },  
}
```

```python
# config/__init__.py

```