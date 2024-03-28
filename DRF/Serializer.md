```table-of-contents
title: 
style: nestedList # TOC style (nestedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
debugInConsole: false # Print debug info in Obsidian console
```
## Form vs. Model
Form^[Django에서 HTML의 form을 다루기 위함]과 Model^[DB의 테이블을 다루기 위함]은 직렬화와 유효성 확인 기능을 모두 제공하기 때문에 둘은 비슷함. 그리고 DRF에선 Serializer가 이 역할을 대신함.
## 직렬화와 역직렬화
### 메모리 내부와 외부
프로그램을 실행하면 코드가 메모리 위로 올라감. '홍길동'을 메모리 내부에서 외부 장치로 전송할 때 메모리 내외의 환경이 다르기 때문에 변환해야 함.