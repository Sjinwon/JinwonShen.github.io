---
layout: post
title: "(javascript/js) 이벤트 페이지 만들기 : 탭 메뉴 구현"
date: 2025-01-10 15:00:00 +09:00
lastmode: 2025-01-10 15:00:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - window.scroll
discription:
---

## 이벤트 페이지 만들기

## 탭 메뉴 구현

상단의 탭 메뉴를 클릭했을 때 해당 위치로 이동하는 액션(스크롤 이동)

<br>

```js
// tabMenu.js
const selectAnchorMenuDOM = document.getElementById("anchor-to-select");
const resultAnchorMenuDOM = document.getElementById("anchor-to-result");
const mbtiAnchorMenuDOM = document.getElementById("anchor-to-mbti");

const selectSectionDOM = document.getElementById("participate-section");
const resultSectionDOM = document.getElementById("result-section");
const mbtiSectionDOM = document.getElementById("mbti-section");

// setScrollHandler 함수를 만들어 사용
const setScrollHandler = (anchorDOM, targetDOM) => {
  anchorDOM.onclick = () => {
    const scrollTargetY = targetDOM.offsetTop;
    window.scroll({
      top: scrollTargetY,
      left: 0,
      behavior: "smooth",
    });
  };
};

export const setTabMenu = () => {
  // selectAnchorMenuDOM 클릭하면 selectSectionDOM으로 스크롤 이동.
  // 1) 직접 구현
  //  1. selectSectionDOM의 element 위치를 받아옴
  //  2. window.scrollTo를 이용해 해당 위치로 이동
  //  window.scroll
  // 2) scrollIntoView 메서드

  setScrollHandler(selectAnchorMenuDOM, selectSectionDOM);
  setScrollHandler(resultAnchorMenuDOM, resultSectionDOM);
  setScrollHandler(mbtiAnchorMenuDOM, mbtiSectionDOM);
};
```
