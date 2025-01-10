---
layout: post
title: "(javascript/js) 이벤트 페이지 만들기 : DOM 제어 및 참여자 수 영역 구현"
date: 2025-01-10 11:55:00 +09:00
lastmode: 2025-01-10 11:55:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - createElement()
  - appendChild()
  - countUp()
  - setInterval()
  - Math.floor()
discription:
---

## 이벤트 페이지 만들기

최애 과자 뽑기, 과자 유형 검사 등 질문에 따라 과자를 선택 시 그에 따른 결과가 노출되는 이벤트 페이지 제작.

해당 이벤트 페이지는 배열 메서드, navigator 객체, 이벤트 핸들링, localStorage, DOM 다루기, setTimeout, scrollIntoView 등을 다룰 예정이다.

<br>

## DOM 제어

이전 프로젝트에서 구현했던 `makeDOMwithProperties`, `appendChildrenList` 유틸 함수를 재사용 할 예정으로

- makeDOMwithProperties(domType, propertyMap) : `domType`을 받아 해당 `div`, `a`, `li` 등 `domType`을 받아 해당 태그에 해당하는 `element`를 생성해 객체로 넘어온 `property` 들를 할당해 리턴해주는 함수였다.

- appendChildrenList(target, childrenList) : `target DOM`을 입력하고 해당 `target DOM`에 `childrenList` 배열에서 `children` 을 하나씩 빼서 `appendChild`해주는 함수

<br>

```js
// dom.js
export const makeDOMwithProperties = (domType, propertyMap) => {
  // TS의 필요성
  const dom = document.createElement(domType);
  Object.keys(propertyMap).map((key) => {
    dom[key] = propertyMap[key];
  });
  return dom;
};

export const appendChildrenList = (target, childrenList) => {
  // childrenList가 배열일 지 아닐 지 모름 -> 타입스크립트 필요
  if (!Array.isArray(childrenList)) return;

  childrenList.forEach((children) => {
    target.appendChild(children);
  });
};
```

<br>

## 참여자 수 영역 구현

countUp.js에서 `countUp()` 함수를 만들어 갱신될 노드, 목표숫자, second, term을 인수로 받아 `setInterval()` 함수를 사용해 참여자수가 늘어나는 타이머를 만들어준다.

```js
// main.js
import { countUp } from "./utils/countUp.js";

const data = {
  participate: 123141251,
};

const participateDOM = document.getElementById("participate-number");
countUp(participateDOM, data.participate, 3);
```

<br>

```js
// dom : innerHTML이 갱신될 노드
// target : 목표 숫자(end)
// second : 몇 초가 걸릴 지 -> 5s
// term : 몇 초마다 함수가 실행할 지 -> 15ms
// countTerm : 한 term에 몇이 증가해야 하는지 -> second, term으로 계산해서 넣어주기

export const countUp = (dom, target, second, term = 15) => {
  // dom.innerHTML을 갱신
  // innerHTML이 n초를 간격으로 갱신되고
  // value += 5 , 10 등
  if (!dom || isNaN(target) || isNaN(second) || isNaN(term)) return;
  const countTerm = Math.floor((target / second) * (term / 1000));
  console.log(countTerm);
  let nowNumber = 0;

  const timerID = setInterval(() => {
    // 타이머가 돌아감 -> 자원을 사용함
    if (nowNumber >= target) {
      nowNumber = target;
      clearInterval(timerID); // 인터벌 정지 -> 자원 사용 정지
      return;
    }
    nowNumber += countTerm;
    dom.innerHTML = `${nowNumber.toLocaleString()}`;
  }, term); // 1초마다 실행
};
```
