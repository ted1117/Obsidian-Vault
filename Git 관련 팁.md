- [[#.gitignore 파일 생성|.gitignore 파일 생성]]
- [[#env 파일|env 파일]]
	- [[#env 파일#생성|생성]]
	- [[#env 파일#Django에 적용|Django에 적용]]

## .gitignore 파일 생성
https://www.toptal.com/developers/gitignore

## env 파일
### 생성
* 최상위 디렉토리에 .env 파일 생성
* 변수, 등호, 값은 공백 없이 입력
```bash
DEBUG=on
SECRET_KEY="asdf"
API_KEY="fdsa"
```
### Django에 적용
```python
# settings.py
from pathlib import Path
import environ

env = environ.Env(DEBUG=(bool, True))

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent

environ.Env.read_env(
	BASE_DIR
	/ ".env"
)

SECRET_KEY = env("SECRET_KEY")

DEBUG = env("DEBUG")
```
