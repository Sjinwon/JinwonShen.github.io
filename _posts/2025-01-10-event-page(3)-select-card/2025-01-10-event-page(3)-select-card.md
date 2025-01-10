---
layout: post
title: "(javascript/js) 이벤트 페이지 만들기 : 선택 카드 만들기 및 결과 영역"
date: 2025-01-10 16:00:00 +09:00
lastmode: 2025-01-10 16:00:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - Object.assign()
  - localStorage.getItem()
  - localStorage.setItem()
discription:
---

## 이벤트 페이지 만들기

## 선택 카드 만들기 및 버튼 구현과 결과 영역 구현

과자뽑기 영역에서 `cardInfoList`에 과자의 정보를 객체로 만들어 과자 카드를 ui에 표시하고 `selectCard`를 선택했을 때 해당 카드에 `selectClass`가 추가되어 ui가 표시되는 코드

```js
// setSelectCards.js
import { SELECT_RESULT_KEY } from "../constants/result.js";
import { appendChildrenList, makeDOMwithProperties } from "../utils/dom.js";

// JSON -> fetch : 서버에서 내려준다고 가정하거나, 데이터 양이 너무 많아 코드에 작성하기 부담스러운 경우 별도의 JSON파일로 가져오는 식으로 구성하게 된다.
// 클라이언트에서 정의하는 변수도 있음. -> 위의 상황이 아닌 경우

const cardInfoList = [
  {
    id: 1,
    imgSrc: "public/assets/초코꼬북칩.jpeg",
    name: "초코꼬북칩",
    description: "맛있는 초코꼬북칩",
  },
  {
    id: 2,
    imgSrc: "public/assets/나쵸.jpeg",
    name: "나쵸",
    description: "맛있는 나쵸",
  },
  {
    id: 3,
    imgSrc: "public/assets/홈런볼.jpeg",
    name: "홈런볼",
    description: "맛있는 홈런볼",
  },
  {
    id: 4,
    imgSrc: "public/assets/허니버터칩.jpeg",
    name: "허니버터칩",
    description: "맛있는 허니버터칩",
  },
];

const snackCardList = document.getElementsByClassName("snack-card-list")[0];
const selectButtonDOM =
  document.getElementsByClassName("participate-button")[0];
const [notyetContainerDOM, resultContainerDOM] =
  document.getElementsByClassName("result-container");
const [
  ,
  resultImageDOM,
  resultNameDOM,
  resultDescriptionDOM,
  selectRetryButtonDOM,
] = resultContainerDOM.children;

const getSelectedCard = () => {
  return document.getElementsByClassName("select")[0];
};
const getCardById = (id) => {
  return document.getElementById(`select-${id}`);
};

const handleSelectCart = (cardId) => {
  // 선택된 카드를 표시를 하는 함수
  // 1. 이미 선택 되어있던 카드는 선택 해지
  // 2. 현재 선택한 카드를 선택

  const originalSelectedCard = getSelectedCard();
  originalSelectedCard?.classList.remove("select");

  const newSelectedCard = getCardById(cardId);
  newSelectedCard?.classList.add("select");
};

const getSelectCardDOM = ({ id, imgSrc, name, description }) => {
  const snackCardDOM = makeDOMwithProperties("button", {
    id: `select-${id}`,
    className: "snack-card",
    onclick: () => handleSelectCart(id),
  });

  const imageDOM = makeDOMwithProperties("img", {
    src: imgSrc,
    alt: name,
  });

  const descriptionContainerDOM = makeDOMwithProperties("div", {
    className: "snack-description",
  });

  const nameDOM = makeDOMwithProperties("div", {
    innerHTML: name,
  });

  const descriptionDOM = makeDOMwithProperties("div", {
    innerHTML: description,
  });

  appendChildrenList(descriptionContainerDOM, [nameDOM, descriptionDOM]);
  appendChildrenList(snackCardDOM, [imageDOM, descriptionContainerDOM]);

  return snackCardDOM;
};

export const setSelectCards = () => {
  // 기존의 snackCardList의 자식 요소들을 받아와서 -> 순회하며 없애주기
  const originalSnackCards = Object.assign([], snackCardList.children);
  originalSnackCards.forEach((snackCard) => {
    snackCard.remove();
  });

  cardInfoList.forEach((cardInfo) => {
    const selectCardDOM = getSelectCardDOM(cardInfo);
    snackCardList.appendChild(selectCardDOM);
  });
  // localStorage에서 이미 선택되어 있는 과자 id를 가져와 표시
  const cardId = Number(localStorage.getItem(SELECT_RESULT_KEY));
  if (!cardId || isNaN(cardId)) return;

  handleSelectCart(cardId);
};

export const setSelectButton = () => {
  // 1. 버튼 DOM받아오기
  // 2. DOM의 onclick 핸들러 등록
  //  1) 선택된 카드의 id를 찾기
  //  2) localStorage에 해당 id를 저장
  //  3) 1번에서 선택된 카드의 id가 없을 때는 선택된 카드가 없다는 경고창을 띄워줌
  selectButtonDOM.onclick = () => {
    const selectedCard = getSelectedCard(); // DOM | undefined
    if (!selectedCard) {
      alert("선택된 카드가 없습니다.");
      return;
    }
    const cardId = selectedCard.id?.split("-")[1];
    // localStorage에 id값 저장
    localStorage.setItem(SELECT_RESULT_KEY, cardId);
    // `select-1`
    // '-' [select, 1] | split
    setResultContainer();
  };
};

// 다시하기
const initialize = () => {
  // 과자가 선택되기 전의 상태로 되돌려주는 함수
  // 1. localStorage의 선택된 cardId를 삭제하기
  // 2. selectCard의 DOM 다시 구현
  // 3. resultContainer의 DOM도 다시 구현

  localStorage.removeItem(SELECT_RESULT_KEY);
  setSelectCards();
  setResultContainer();

  const selectSectionDOM = document.getElementById("participate-section");
  const scrollTargetY = selectButtonDOM.offsetTop;

  window.scroll({
    top: scrollTargetY,
    left: 0,
    behavior: "smooth",
  });
};

// result container 결과창
export const setResultContainer = () => {
  // result 구역에 선택된 과자를 노출시키는 함수
  // 과자버튼 클릭 시 페이지 랜딩 시 동작

  // 1. 선택된 과자의 id를 localStorage로 부터 받아오기
  // 2. 선택된 id가 저장되어 있다면, notyetContainer를 없애고 resultContainer를 보여주기
  // 3. cardInfoList에서 선택된 카드의 정보를 찾아서 그 정보를 ResultContainer에 연결시키기

  const selectedId = Number(localStorage.getItem(SELECT_RESULT_KEY));
  const isSelected = !!selectedId;
  if (!isSelected) {
    // notyetContainer를 드러내고, resultContainer를 숨기기
    notyetContainerDOM.style.display = "block";
    resultContainerDOM.style.display = "none";
    return;
  }

  // notyetContainer를 숨기고, resultContainer를 드러내기
  // resultContainer에 선택된 과자의 정보를 주입하기

  notyetContainerDOM.style.display = "none";
  resultContainerDOM.style.display = "flex";

  const cardInfo = cardInfoList.find((info) => info.id === selectedId);
  // Array.find((a) => { 매개변수로 이용한 코드를 작성하는데 그 값이 true이면 a를 반환 })

  resultImageDOM.src = cardInfo.imgSrc;
  resultImageDOM.alt = cardInfo.name;
  resultNameDOM.innerHTML = cardInfo.name;
  resultDescriptionDOM.innerHTML = cardInfo.description;

  // 다시하기 버튼 구현
  selectRetryButtonDOM.onclick = initialize;
};
```
