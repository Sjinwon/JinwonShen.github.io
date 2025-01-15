---
layout: post
title: "(toy-project) 미니게임 사이트 만들기 : 이벤트 핸들러 및 타이머 구현"
date: 2025-01-11 15:37:00 +09:00
lastmode: 2025-01-11 15:37:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - mouseControl
  - onmouseover
  - onmouseleave
  - modal
discription:
---

## 미니게임 사이트 만들기

## 이벤트 핸들러 및 타이머 구현

`setBoxDOM`내부에서 구현해야 하는 시작 위치의 박스에서 마우스가 도착하면 게임이 시작되거나, 끝 박스에 마우스가 도착하면 게임이 끝나고, 월 박스에 마우스가 도착하면 게임이 실패한 채로 끝나고, 길에 해당하는 박스에 마우스가 도착하면 길의 색이 바뀐다거나 하는 이벤트 핸들러를 등록하기

<br>

```js
// mouseControl.js
import {
  initMouseControlGame,
  setBoxDOM,
} from "./module/mouseControlModule.js";
import { handleModalClose } from "./utils/modal.js";

const initialize = () => {
  // modal의 버튼을 세팅
  // retryButton에 게임 상태를 완복하는 함수를 실행.
  const retryButton = document.getElementsByClassName("retry-button")[0];
  retryButton.onclick = () => handleModalClose(initMouseControlGame);
};

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

initialize();
```

<br>

```js
// mouseControlModule.js
import { makeDOMwithProperties } from "../utils/dom.js";
import { handleModalOpen } from "../utils/modal.js";
import {
  isGameStart,
  setTimer,
  startTimer,
  stopTimer,
  getResultTimeString,
  getNowTime,
} from "../utils/timer.js";
import { MOUSE_CONTROL_SCORE_KEY } from "../constants/localStorage.js";

// boxDOM들을 저장할 수 있는 전역변수
let boxDOMList = [];
let wallBoxDOMList = [];
let startBoxDOM = null;
let endBoxDOM = null;

const gameFieldDOM = document.getElementById("game-field");

export const initMouseControlGame = () => {
  startBoxDOM.innerHTML = "시작";
  endBoxDOM.innerHTML = "끝";
  boxDOMList.forEach((boxDOM) => {
    boxDOM.style.backgroundColor = "transparent";
  });
  stopTimer();
  setTimer(0);
};

const handleSuccessGame = () => {
  stopTimer();
  // 게임 성공시 타이머를 멈추고 모달을 띄워주기
  // Todo: modal 구현
  handleModalOpen({
    isSuccess: true,
    timeString: getResultTimeString(),
  });
  // 게임 성공시 localStorage에 갱신된 최고 점수 (최소 소요 시간) 저장
  const nowSpendTime = getNowTime();
  const currentSpendTime = localStorage.getItem(MOUSE_CONTROL_SCORE_KEY);
  if (!currentSpendTime || currentSpendTime < nowSpendTime) {
    localStorage.setItem(MOUSE_CONTROL_SCORE_KEY, nowSpendTime);
  }

  setTimer(0);
};

const handleFailedGame = () => {
  stopTimer();
  // 게임 실패시 타이머를 멈추고 모달을 띄워주기
  // Todo: modal 구현
  handleModalOpen({
    isSuccess: false,
  });
  setTimer(0);
};

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
    // 필트를 벗어났을 때에도 실패하기 때문에 다음 메서드를 동일하게 실행
    onmouseleave: () => {
      if (!isGameStart) return;
      handleFailedGame();
    },
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
        onmouseover,
      } = (function () {
        // 익명함수와 즉시실행함수
        if (i === start[0] && j === start[1]) {
          return {
            type: "start",
            className: "control-box start",
            innerHTML: "시작",
            onmouseover: (event) => {
              startTimer(handleFailedGame);

              event.target.innerHTML = "";
              // 게임 시작 -> 타이머가 시작
              // 게임 시작 변수 변경 !
              // 시작이 표기되어있는 innerHTML 없애기
            },
          };
        }
        if (i === end[0] && j === end[1]) {
          return {
            type: "end",
            className: "control-box end",
            innerHTML: "끝",
            onmouseover: () => {
              if (!isGameStart) return;
              event.target.innerHTML = "";
              handleSuccessGame();
              // 게임 시작 변수가 세팅 되었을 때를 기준으로 동작
              // 게임 종료 -> 타이머가 종료, 성공 모달 출력
              // 끝이 표기되어있는 innerHTML 없애기
            },
          };
        }
        for (let wall of walls) {
          if (i === wall[0] && j === wall[1]) {
            // 벽의 위치
            return {
              type: "wall",
              className: "control-box wall",
              onmouseover: (event) => {
                if (!isGameStart) return;
                handleFailedGame();
                // 게임 시작 변수가 세팅 되었을 때를 기준으로 동작
                // 게임 종료 -> 타이머가 종료, 실패 모달 출력
              },
            };
          }
        }
        return {
          type: "normal",
          className: "control-box",
          onmouseover: (event) => {
            if (!isGameStart) return;

            event.target.style.backgroundColor = "linen";
          },
          // 게임 시작 변수가 세팅 되었을 때를 기준으로 동작
          // 길의 색상이 변경
        };
      })();

      const boxDOM = makeDOMwithProperties("div", {
        className: className, // 키와 값이 같으면 생략가능
        innerHTML: innerHTML,
        id: `box-${i}-${j}`,
        onmouseover,
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

**아래 수정된 코드**

기존에 `timer.js`에서 작성한 함수 `startTimer`를 실행했을 때 웹에서 초에 오차가 있는 것을 발견했는데, `setInterval`의 특성과 타이머 실행 시간 누적의 차이 떄문이라고 한다.

`setInterval`은 기본적으로 지정된 시간 간격(예: 1000ms)마다 콜백 함수를 실행하도록 예약한다. 하지만 이 과정에서 아래와 같은 이유로 정확히 1초 간격을 유지하지 못할 수 있다.

그 외에도 `setInterval`의 비동기 특성, `setInterval`실행 지연 누적, 함수 실행 시간 등의 이유가 있었다.

```js
// 기존 startTimer
export const startTimer = (onTimeOver) => {
  isGameStart = true;
  timerId = setInterval(() => {
    time++;
    timerDOM.innerHTML = getTimeString(time); // 0 -> 00:00:00 1 -> 00:00:01
    if (MAX_TIME < time) {
      onTimeOver?.();
      clearInterval(timerId);
    }
  }, 1000);
};
```

**chatGPT를 활용해 수정한 코드**

```js
// 수정 후 startTimer
export const startTimer = (onTimeOver) => {
  isGameStart = true;
  const startTime = Date.now() - time * 1000; // 현재 시간에서 경과 시간 보정

  const updateTimer = () => {
    const currentTime = Date.now();
    time = Math.floor((currentTime - startTime) / 1000); // 실제 경과 시간 계산
    timerDOM.innerHTML = getTimeString(time);

    if (MAX_TIME < time) {
      onTimeOver?.();
      stopTimer();
    } else {
      timerId = setTimeout(
        updateTimer,
        1000 - ((Date.now() - currentTime) % 1000)
      ); // 보정된 간격으로 호출
    }
  };

  updateTimer();
};
```

<br>

```js
// timer.js
const MAX_TIME = 3600 * 24; // 최대시간은 24시간
const timerDOM = document.getElementsByClassName("game-time")[0];

export let isGameStart = false;

let time = 0;
let timerId = null;

const convertToTwoNumber = (num) => {
  const stringNum = `${num}`; // String(num)도 가능
  if (stringNum.length === 1) return `0${stringNum}`;
  else return stringNum;
};

const getTimeString = (time) => {
  // e.g. time = 0; return "00:00:00"
  // 60s -> 00:01:00
  // 3600s -> 01:00:00
  // time / 60

  // 1시간 1분 1초 -> 1 + 60 + 3600 -> 3661초
  // 1과 나머지 61, hours : 1;
  const hours = Math.floor(time / 3600); // time을 3600으로 나누면 1.03??? 등으로 나올 수 있기 때문에 Math.floor(내림)
  time = time - hours * 3600; // 61
  const minutes = Math.floor(time / 60); // 몫1과 나머지1
  time = time - minutes * 60; // 61 - 60 * 1
  const seconds = time;

  // 0이 넘어왔을 때, 0:0:0으로 넘어옴
  // convertToTwoNumber함수를 만들어 활용
  return `${convertToTwoNumber(hours)}:${convertToTwoNumber(
    minutes
  )}:${convertToTwoNumber(seconds)}`;
};

export const startTimer = (onTimeOver) => {
  isGameStart = true;
  timerId = setInterval(() => {
    time++;
    timerDOM.innerHTML = getTimeString(time); // 0 -> 00:00:00 1 -> 00:00:01
    if (MAX_TIME < time) {
      onTimeOver?.();
      clearInterval(timerId);
    }
  }, 1000);
};

export const stopTimer = () => {
  isGameStart = false;
  if (timerId == null) return;
  clearInterval(timerId);
};

export const setTimer = (initTime) => {
  time = initTime;
  timerDOM.innerHTML = getTimeString(time);
};
```

<br>

## 모달 구현

성공 및 실패 시 모달 창 띄우기 (홈 및 다시하기 버튼)

```js
// modal.js
const modalDOM = document.getElementsByClassName("modal")[0];
const modalTitleDOM = document.getElementsByClassName("modal-title")[0];
const modalDescriptionDOM =
  document.getElementsByClassName("modal-description")[0];

export const handleModalOpen = ({
  isSuccess,
  timeString, // "00:00:00" | undefined
}) => {
  modalDOM.classList.add("open");
  // 성공 모달 -> title -> 성공 !, description -> 몇 초만에 성공했습니다 !
  // 실패 모달 -> title -> 실패 !, description -> 다시 시도해보세요 ~
  if (isSuccess) {
    modalTitleDOM.innerHTML = "성공!";
    modalDescriptionDOM.innerHTML = `${timeString}만에 성공했습니다 !`;
  } else {
    modalTitleDOM.innerHTML = "실패!";
    modalDescriptionDOM.innerHTML = "다시 시도해보세요 ~";
  }
};

export const handleModalClose = (onModalClose) => {
  modalDOM.classList.remove("open");
  onModalClose?.();
};
```
