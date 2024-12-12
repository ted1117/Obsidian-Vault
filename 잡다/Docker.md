## 개요
Django + Celery(Redis) + PostgreSQL + nginx + gunicorn을 모두 도커에 올려보자  

## 방법
### Dockerfile
```
# For more information, please refer to https://aka.ms/vscode-docker-python

FROM python:3.10-slim

EXPOSE 8000

# Keeps Python from generating .pyc files in the container
ENV PYTHONDONTWRITEBYTECODE=1

# Turns off buffering for easier container logging
ENV PYTHONUNBUFFERED=1

# Install pip requirements
COPY requirements.txt .
RUN python -m pip install -r requirements.txt


WORKDIR /app
COPY . /app


# Creates a non-root user with an explicit UID and adds permission to access the /app folder
# For more info, please refer to https://aka.ms/vscode-docker-python-configure-containers
RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
USER appuser


# During debugging, this entry point will be overridden. For more information, please refer to https://aka.ms/vscode-docker-python-debug
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "config.wsgi"]
```

```
services:
web:
build: .
container_name: comic_web
command: gunicorn config.wsgi:application --bind 0.0.0.0:8000
volumes:
- static_data:/app/static
env_file:
- .env
depends_on:
- db
- redis
 
worker:
build: .
container_name: comic_worker
command: celery -A config worker -l info
env_file:
- .env
depends_on:
- web
- redis
- db

beat:
build: .
container_name: comic_beat
command: celery -A config beat -l info #--schedule=/app/beatdata/beat.db
env_file:
- .env
depends_on:
- web
- redis
- db

db:
image: postgres:14-alpine
container_name: comic_db
environment:
POSTGRES_USER: ${DB_USER}
POSTGRES_PASSWORD: ${DB_PASSWORD}
POSTGRES_DB: ${DB_NAME}
volumes:
- db_data:/var/lib/postgresql/data

redis:
image: redis:7-alpine
container_name: comic_redis
command: redis-server --appendonly yes
volumes:
- redis_data:/data

nginx:
image: nginx:alpine
container_name: comic_nginx
depends_on:
- web
volumes:
- ./nginx/conf.d:/etc/nginx/conf.d:ro
- static_data:/app/static
ports:
- "80:80"

pgadmin:
image: dpage/pgadmin4:latest
container_name: pgadmin-container
ports:
- "5050:80"
environment:
- PGADMIN_DEFAULT_EMAIL=admin@example.com
- PGADMIN_DEFAULT_PASSWORD=admin123
depends_on:
- db

  

volumes:
db_data:
redis_data:
static_data:
beat_data:
```

