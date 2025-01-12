---
layout: post
title: "(javascript/js) 미니게임 사이트 만들기 : 방향키 게임"
date: 2025-01-12 21:37:00 +09:00
lastmode: 2025-01-12 21:37:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - keydown event
  - includes
discription:
---

## 미니게임 사이트 만들기

## 방향키 게임 구현

랜덤으로 생성되는 방향키를 클릭해 적은 소요 시간을 기록하는 게임 구현

```js
import { makeDOMwithProperties } from "./utils/dom.js";
import { handleModalClose, handleModalOpen } from "./utils/modal.js";
import {
  getNowTime,
  startTimer,
  stopTimer,
  setTimer,
  getResultTimeString,
} from "./utils/timer.js";
import { ARROW_SPEED_SCORE_KEY } from "./constants/localStorage.js";

const MAX_ARROW = 8;
const MAX_ROUND = 3;

let arrowDOMList = [];
let currentIndex = 0;
let round = 1;

const arrowFieldDOM = document.getElementById("arrow-field");

const clearArrowDOM = () => {
  arrowDOMList.forEach((arrowDOM) => {
    arrowDOM.remove();
  });
  arrowDOMList = [];
};

const setArrowDOM = () => {
  // 1. 기존에 존재하고 있던 arrow 돔이 있으면 삭제
  // 2. 새로 DOM을 만들어서 세팅
  // 3. 세팅하는 과정에서 랜덤으로 왼쪽 오른쪽을 결정
  clearArrowDOM();

  for (let i = 0; i < MAX_ARROW; i++) {
    // 0 ~ 0.999..99 까지의 랜덤한 수
    const direction = Math.random() < 0.5 ? "left" : "right";
    const arrowDOM = makeDOMwithProperties("span", {
      className: `arrow arrow-${direction}`,
      innerHTML: direction === "left" ? "&lt;" : "&gt;",
    });
    arrowDOMList.push(arrowDOM);
    arrowFieldDOM.appendChild(arrowDOM);
  }
};

const handleSuccessGame = () => {
  console.log("성공");
  // 타이머를 멈추고 -> 모달을 띄워서 성공 시간을 노출 -> 타이머를 0으로 세팅
  // localStorage 최소소요시간을 저장
  stopTimer();
  handleModalOpen({
    isSuccess: true,
    timeString: getResultTimeString(),
  });
  const nowSpendTime = getNowTime();
  const currentSpendTime = localStorage.getItem(ARROW_SPEED_SCORE_KEY);
  if (!currentSpendTime || currentSpendTime > nowSpendTime) {
    localStorage.setItem(ARROW_SPEED_SCORE_KEY, nowSpendTime);
  }
  setTimer(0);
};
const handleFailedGame = () => {
  console.log("실패");
};

const setKeyboardEvent = () => {
  // 이벤트 핸들러 등록 -> keydown
  // 왼쪽 방향키 || 오른쪽 방향키가 클릭되면
  // 현재 눌려져야 할 방향키와 같다면, 해당방향키가 없어지는

  const handleCorrect = () => {
    // 방향키가 잘 눌렸다면 방향키 돔을 삭제(안보이게)
    // currentIndex를 1 증가 ++
    // 모든 방향키가 다 눌렸다면 다음 라운드로 ()

    arrowDOMList[currentIndex].style.display = "none";
    currentIndex++;
    if (currentIndex === MAX_ARROW) {
      // 다음 라운드로 !
      if (round === MAX_ROUND) {
        // 게임 종료
        handleSuccessGame();
        return;
      }
      currentIndex = 0;
      setArrowDOM();
      round += 1;
    }
  };

  window.addEventListener("keydown", (event) => {
    // 키보드가 눌렸을 때 왼쪽 오른쪽이 아니라면 전부 무시
    if (!["ArrowLeft", "ArrowRight"].includes(event.code)) return;
    const isFirst = currentIndex === 0 && round === 1;
    if (isFirst) startTimer(handleFailedGame);

    const isLeft = arrowDOMList[currentIndex].innerHTML === "&lt;";
    if (isLeft && event.code === "ArrowLeft") {
      handleCorrect();
    }
    if (!isLeft && event.code === "ArrowRight") {
      handleCorrect();
    }
  });

  // arrowDOMList(currentIndex).innerHTML === "&lt"; // 왼쪽 방향키를 입력해야 함
};

const onArrowSpeedGameEnd = () => {
  stopTimer();
  setTimer(0);
  currentIndex = 0;
  round = 1;
  setArrowDOM();
};

const initialize = () => {
  // retrybutton에 timer 세팅과 모달 달기, 상태 원복 코드를 삽입
  const [headerRetryButton, modalRetryButton] =
    document.getElementsByClassName("retry-button");
  headerRetryButton.onclick = () => {
    handleModalClose(onArrowSpeedGameEnd);
  };
  modalRetryButton.onclick = () => {
    handleModalClose(onArrowSpeedGameEnd);
  };
};

setArrowDOM();
setKeyboardEvent();
initialize();
```
