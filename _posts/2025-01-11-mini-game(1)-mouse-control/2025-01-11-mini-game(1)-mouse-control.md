---
layout: post
title: "(toy-project) 미니게임 사이트 만들기 : 마우스 컨트롤 구현"
date: 2025-01-11 14:53:00 +09:00
lastmode: 2025-01-11 14:53:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - switch
  - mouseControl
discription:
---

## 미니게임 사이트 만들기

미니게임 사이트로 게임 종류가 표시되어 있고, 게임 카드를 클릭했을 때 각각의 게임 페이지로 이동하게 된다. 새롬게 만들어진 게임은 New 뱃지가 붙어있고, 기록이 있는 게임은 최고 기록이 표시가 되고, 아닌 게임은 '도전해 보세요!' 라는 문구가 표시되어 있다.

해당 미니게임 사이트에서는 배열메서드, location객체, 이벤트 핸들링(키보드), localStorage, DOM 다루기, 랜덤 수 생성, setInterval, 콜백함수 등을 다뤄볼 예정입니다.

## 마우스 컨트롤 구현

이전 프로젝트에서 구현했던 `makeDOMwithProperties`, `appendChildrenList` 유틸 함수를 재사용 할 예정으로

- makeDOMwithProperties(domType, propertyMap) : `domType`을 받아 해당 `div`, `a`, `li` 등 `domType`을 받아 해당 태그에 해당하는 `element`를 생성해 객체로 넘어온 `property` 들를 할당해 리턴해주는 함수였다.

- appendChildrenList(target, childrenList) : `target DOM`을 입력하고 해당 `target DOM`에 `childrenList` 배열에서 `children` 을 하나씩 빼서 `appendChild`해주는 함수, 그리고 `if(!children) return` 을 추가해 자식요소가 없으면 바로 리턴해주는 구문을 추가했다.

<br>

```js
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
    if (!children) return; // 추가
    target.appendChild(children);
  });
};
```

<br>

```js
// mouseControlModule.js
import { makeDOMwithProperties } from "../utils/dom.js";

// boxDOM들을 저장할 수 있는 전역변수
let boxDOMList = [];
let wallBoxDOMList = [];
let startBoxDOM = null;
let endBoxDOM = null;

const gameFieldDOM = document.getElementById("game-field");

export const setBoxDOM = ({
  row, // 행이 몇갠지
  col, // 열이 몇갠지
  start, // 시작 위치[ 행,열 ]
  end, // 종료 위치 [ 행, 열 ]
  walls, // 벽의 위치들 [ 행, 열 ]
}) => {
  // control-box-container를 만들고,
  // box들을 채우기
  const controlBoxContainer = makeDOMwithProperties("div", {
    id: "control-box-container",
  });
  controlBoxContainer.style.display = "grid";
  controlBoxContainer.style.gridTemplateRows = `repeat(${row}, 1fr)`;
  controlBoxContainer.style.gridTemplateColumns = `repeat(${col}, 1fr)`;
  // display: grid;
  // grid-template-rows: repeat(3, 1fr) // 3행
  // grid-template-columns: repeat(4, 1fr) // 4행

  for (let i = 0; i < row; i++) {
    // 행을 1씩 늘려가면서
    for (let j = 0; j < col; j++) {
      // 열을 1씩 늘려가면서
      const {
        type,
        className,
        innerHTML = "",
      } = (function () {
        // 익명함수와 즉시실행함수
        if (i === start[0] && j === start[1]) {
          return {
            type: "start",
            className: "control-box start",
            innerHTML: "시작",
          };
        }
        if (i === end[0] && j === end[1]) {
          return {
            type: "end",
            className: "control-box end",
            innerHTML: "끝",
          };
        }
        for (let wall of walls) {
          if (i === wall[0] && j === wall[1]) {
            // 벽의 위치
            return {
              type: "wall",
              className: "control-box wall",
            };
          }
        }
        return {
          type: "normal",
          className: "control-box",
        };
      })();

      const boxDOM = makeDOMwithProperties("div", {
        className: className, // 키와 값이 같으면 생략가능
        innerHTML: innerHTML,
        id: `box-${i}-${j}`,
      });

      switch (type) {
        case "start":
          startBoxDOM = boxDOM;
          break;
        case "end":
          endBoxDOM = boxDOM;
          break;
        case "wall":
          wallBoxDOMList.push(boxDOM);
          break;
        default:
          boxDOMList.push(boxDOM);
      }

      controlBoxContainer.appendChild(boxDOM);
    }
  }
  gameFieldDOM.appendChild(controlBoxContainer);
};
```

<br>

```js
// mouseControl.js
import { setBoxDOM } from "./module/mouseControlModule.js";

setBoxDOM({
  row: 5,
  col: 5,
  start: [0, 0],
  end: [4, 4],
  walls: [
    [1, 0],
    [1, 1],
    [1, 2],
    [1, 3],
    // [3, 0],
    [3, 1],
    [3, 2],
    [3, 3],
    [3, 4],
  ],
});
```
