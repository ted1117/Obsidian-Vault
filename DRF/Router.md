```table-of-contents
title: 
style: nestedList # TOC style (nestedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
debugInConsole: false # Print debug info in Obsidian console
```
## DefaultRouter vs. SimpleRouter
### 공통점
```python
router = routers.DefaultRouter()
router.register(r'users', UserViewSet)
```

|    URL    |  메소드   |       액션       |
| :-------: | :----: | :------------: |
|  users/   |  GET   |      list      |
|  users/   |  POST  |     create     |
| users/pk/ |  GET   |    retrieve    |
| users/pk/ |  POST  |     update     |
| users/pk/ | DELETE |    destroy     |
| users/pk/ | PATCH  | partial_update |
### 차이점
* API Root
* format suffix (users.json, users.api, users/99.json, users/99.api)
 


