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

## Django
### django 기본 설정
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
from .celery import app as celery_app  
  
__all__ = ('celery_app',)
```

```python
# config/celery.py
import os

from celery import Celery

# Set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')

app = Celery('config')

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
# - namespace='CELERY' means all celery-related configuration keys
#   should have a `CELERY_` prefix.
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django apps.
app.autodiscover_tasks()

@app.task(bind=True, ignore_result=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
```

## worker
```python
# api/tasks.py
from celery import shared_task


@shared_task  
def add(x, y):  
    return x + y
```

```shell
$ celery -A config worker -l INFO
```

## Celery-beat
```python
# config/celery.py
...

app.conf.timezone = 'Asia/Seoul'  

# test-periodic-job은 실행할 태스크 이름
# task의 value는 실행할 메소드 경로 및 이름
app.conf.beat_schedule = {  
    "test-periodic-job": {  
        "task": "api.tasks.test_periodic_task",  
        "schedule": crontab()  
    }  
}
```

```shell
$ celery -A config worker --beat -l INFO
```
