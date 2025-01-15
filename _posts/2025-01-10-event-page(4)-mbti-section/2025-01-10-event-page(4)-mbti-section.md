---
layout: post
title: "(toy-project) 이벤트 페이지 만들기 : 과자 유형 검사"
date: 2025-01-10 23:50:00 +09:00
lastmode: 2025-01-10 23:50:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - switch()
discription:
---

## 이벤트 페이지 만들기

## 과자 유형 검사

과자 유형 검사 영역을 구현한다. 질문에 따라 yes, no를 선택할 수 있고 버튼을 클릭 시 다음 질문이 표시된다. 그에 따른 결과가 출력되는 기능

```js
// mbti 섹션
// 1. 질문이 표시, Yes / No 버튼
// 2. yes -> 점수 +1, no -> 점수 +0
// 3. 버튼을 눌렀을 때 다음 질문이 표시
// 4. n개의 질문이 끝나면 "잠시만 기다려주세요.. 결과분석중" 안냇말이 3초간 출력
// 5. 분석된 결과가 result에 출력됨

const mbtiQuestionDOM = document.getElementsByClassName("mbti-question")[0];
const [yesButton, noButton] =
  document.getElementsByClassName("mbti-select")[0].children;
const [selectDOM, pendingDOM, resultDOM] =
  document.getElementsByClassName("mbti-container");
const mbtiResultTitleDOM = document.getElementsByClassName("mbti-result")[0];
const mbtiResultDescriptionDOM =
  document.getElementsByClassName("mbti-description")[0];
const mbtiRetryButtonDOM =
  document.getElementsByClassName("mbti-retry-button")[0];

const mbtiQuestionList = [
  "짠 과자가 단 과자보다 좋다",
  "봉지 과자가 박스 과자보다 좋다",
  "과자를 뜯으면 한 번에 다 먹는다.",
];

// 질문 3가지 -> 4가지 경우의 수
// 질문 n가지 -> n+1가지 경우의 수
// 0
// 1
// 2
// 3
const getMbtiResult = (resultValue) => {
  // 결과를 받아 결과 정보를 반환
  // 결과 정보의 형태는 title과 description으로 넘겨줌
  switch (resultValue) {
    case 0:
      return {
        title: "과자 어린이 (A유형)",
        description: "과자 어린이 (A유형) 설명",
      };
    case 1:
      return {
        title: "과자 초심자 (B유형)",
        description: "과자 초심자 (B유형) 설명",
      };
    case 2:
      return {
        title: "과자 중급자 (C유형)",
        description: "과자 중급자 (C유형) 설명",
      };
    case 3:
    default:
      return {
        title: "돼지 (D라인)",
        description: "돼지 (D라인) 설명",
      };
  }
};

let currentRound = 0;
let resultValue = 0; // 0 ~ n
const maxRound = mbtiQuestionList.length;

const setPendingSection = () => {
  selectDOM.style.display = "none";
  pendingDOM.style.display = "block";

  setTimeout(() => {
    pendingDOM.style.display = "none";
    resultDOM.style.display = "block";
  }, 3000);
};

const initialize = () => {
  // currentRound = 0; resultValue = 0;
  // setMbtiSection
  // result -> none
  // select -> block

  currentRound = 0;
  resultValue = 0;
  selectDOM.style.display = "block";
  pendingDOM.style.display = "none";
  resultDOM.style.display = "none";
};

const setResultSection = () => {
  // result section에 들어갈 결과 정보들을 DOM에 주입
  const { title, description } = getMbtiResult(resultValue);
  mbtiResultTitleDOM.innerHTML = title;
  mbtiResultDescriptionDOM.innerHTML = description;

  mbtiRetryButtonDOM.onclick = initialize;
};

export const setMbtiSection = () => {
  // 질문 표시
  // 버튼이 눌렸을 때 다음 질문으로 넘어감
  if (currentRound === maxRound) {
    setPendingSection();
    setResultSection();
    return; // 끝 ! -> pending을 3초간 표시 -> result 표시
  }

  selectDOM.style.display = "block";

  mbtiQuestionDOM.innerHTML = mbtiQuestionList[currentRound++]; // 줄이 실행된 후 증감연산자 실행
  yesButton.onclick = () => {
    resultValue++;
    setMbtiSection();
  };
  noButton.onclick = () => {
    setMbtiSection();
  };
};
```
