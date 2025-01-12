---
layout: post
title: "(javascript/js) 미니게임 사이트 만들기 : 숫자 클릭 게임 구현"
date: 2025-01-12 17:11:00 +09:00
lastmode: 2025-01-12 17:11:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - Math.floor()
  - Math.random()
discription:
---

## 미니게임 사이트 만들기

## 숫자 클릭 게임 구현

마우스를 이용해 랜덤한 위치의 숫자를 1부터 10까지 순서대로 클릭해 적은 소요시간을 기록하는 게임 구현

```js
import { handleModalOpen, handleModalClose } from "./utils/modal.js";
import {
  getNowTime,
  getResultTimeString,
  startTimer,
  stopTimer,
  setTimer,
} from "./utils/timer.js";
import { TOUCH_NUMBER_SCORE_KEY } from "./constants/localStorage.js";
const numberButtonsList = document.getElementsByClassName("number-button");
const maxId = numberButtonsList.length;
let currentNumber = 1;

const handleSuccessGame = () => {
  // 타이머를 멈추고 성공모달
  stopTimer();
  handleModalOpen({
    isSuccess: true,
    timeString: getResultTimeString(),
  });

  const nowSpendTime = getNowTime();
  const currentSpendTime = localStorage.getItem(TOUCH_NUMBER_SCORE_KEY);
  if (!currentSpendTime || currentSpendTime > nowSpendTime) {
    localStorage.setItem(TOUCH_NUMBER_SCORE_KEY, nowSpendTime);
  }

  setTimer(0);
};

const handleFailedGame = () => {
  stopTimer();
  handleModalOpen({
    isSuccess: false,
  });
  setTimer(0);
};

const setButtonDOM = () => {
  // 1. HTML상에서 domList를 받아온 뒤
  // 2. 순회하면서 dom의 위치를 조정 (랜덤으로)
  // 3. dom 클릭 시 핸들러를 등록

  for (let numberButton of numberButtonsList) {
    // 범위 지정하기 0 ~ 100% 사이의 수
    // 범위를 넘어가는 것을 방지하기 위해 0 ~90%
    numberButton.style.display = "block";
    numberButton.style.top = `${Math.floor(Math.random() * 100 * 0.9)}%`;
    numberButton.style.left = `${Math.floor(Math.random() * 100 * 0.9)}%`;
    numberButton.onclick = () => {
      // 1. 클릭한 수를 찾아옴
      // 2. 수가 현재 클릭되어야 하는 순서가 맞는지 판단 ->
      //    현재 클릭되어야 하는 순서가 아니라면 로직을 무시한다. 맞다면 해당 numberButton을 없앤다.
      // 3. 1을 클릭 했을 때는 타이머를 시작하고
      // 4. 10을 클릭 했을 떄는 타이머를 멈춘다. -> 성공 모달
      const numId = Number(event.target.innerHTML);
      if (isNaN(numId)) return;
      if (numId !== currentNumber) return;
      event.target.style.display = "none";
      if (numId === maxId) {
        // 성공 모달을 띄우고,
        handleSuccessGame();
        return;
      }
      if (numId === 1) {
        startTimer(handleFailedGame);
      }
      // numId가 currentNumber와 같을 때
      currentNumber++;
    };
  }
};

const initializeTouchNumberGame = () => {
  // 타이머를 다시 세팅
  // 숫자의 위치를 다시 세팅
  // 숫자가 다시 나타날 수 있게 세팅
  setTimer(0);
  stopTimer();
  setButtonDOM();
  currentNumber = 1;
};

const initialize = () => {
  // modal -> retry, header - retry 세팅
  // 클릭 시 모달 닫기, 모든 상태를 원상복구
  const [headerRetryButton, modalRetryButton] =
    document.getElementsByClassName("retry-button");
  headerRetryButton.onclick = () => {
    handleModalClose(initializeTouchNumberGame);
  };
  modalRetryButton.onclick = () => {
    handleModalClose(initializeTouchNumberGame);
  };
};

setButtonDOM();
initialize();
```
