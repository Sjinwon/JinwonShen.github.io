---
layout: post
title: "(toy-project) 무한 스크롤"
date: 2025-01-15 19:08:00 +09:00
lastmode: 2025-01-15 19:08:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - toy project
  - infinite scroll
  - typicode
  - removeEventListener
discription:
---

## infinite scroll

- 무한스크롤 구현
- 컨텐츠의 끝이 화면 끝에 닿으면 api가 호출되게 구현
- todolist 와 달리 외부 api 연동
- fetch, async, await

## API 연동

https://jsonplaceholder.typicode.com/

- 프로토타이핑용 api
- /posts 활용
- [json-server](https://www.npmjs.com/package/json-server) 만든 곳에서 만들었기 때문에 사용 방법은 json-server와 동일

### removeEventListener

https://developer.mozilla.org/ko/docs/Web/API/EventTarget/removeEventListener

- addEventListener()로 등록했던 이벤트 리스너를 제거
- 더이상 필요가 없는데 이벤트가 계속 발생하는 경우 메모리 누수가 일어날 수 있음
- 따라서 명시적으로 해제

<br>

_**github 참고**_

[https://github.com/JinwonShen/infinite-scroll](https://github.com/JinwonShen/infinite-scroll)
