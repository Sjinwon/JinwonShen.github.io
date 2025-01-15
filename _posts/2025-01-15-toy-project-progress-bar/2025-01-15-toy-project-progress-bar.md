---
layout: post
title: "(toy-project) 프로그레스 바"
date: 2025-01-15 17:12:00 +09:00
lastmode: 2025-01-15 17:12:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - toy project
  - drum kit
  - Array.from
  - keycode
  - TransitionEvent
discription:
---

## progress bar

- 프로그레스바 구현
- 스크롤을 내리면 상단 바가 스크롤한 길이만큼 늘어남

### 요소 사이즈와 스크롤

https://ko.javascript.info/size-and-scroll

![요소 사이즈와 스크롤](https://media.vlpt.us/images/wiostz98kr/post/dec62e7b-1432-4db2-8154-8539fb0b3689/image.png)

- 자바스크립트로 요소 사이즈나 스크롤 높이 등을 알 수 있음
- 주황색 보더를 기준
  - 보더 바깥쪽: offsetTop, offsetLeft
  - 보더 사이: clientTop, clinetLeft
  - 콘텐츠: clientWidth, clientHeight
  - 보더 포함한 콘텐츠: offsetWidth, offsetHeight
  - 콘텐츠의 전체 길이: scrollHeight
  - 스크롤바의 수직 위치: scrollTop

### throttle & debounce

https://css-tricks.com/debouncing-throttling-explained-examples/

- 시간이 지남에 따라 함수가 실행되도록 허용하는 횟수를 제어하는 방법

#### debounce

- debounce: 특정 시간이 지난 후 하나의 이벤트만 발생시키는 방법
- 예시) 버튼 중복 클릭 방지

![debounce](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F4rclV%2Fbtq0ApFD65V%2F34Jw0Gdel1hvvHohbxG2tk%2Fimg.png)

#### throttle

- throttle: 일정한 주기마다 이벤트를 발생시키는 방법
- 예시) 스크롤 이벤트

![throttle](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbkUExC%2Fbtq0zZf9s1c%2FQ559Kyka5nQc0dZL8OL1W1%2Fimg.png)

<br>

_**github 참고**_

[https://github.com/JinwonShen/infinite-scroll](https://github.com/JinwonShen/infinite-scroll)
