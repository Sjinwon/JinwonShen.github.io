---
layout: post
title: "(toy-project) 이벤트 페이지 만들기 : 공유하기"
date: 2025-01-11 00:23:00 +09:00
lastmode: 2025-01-11 00:23:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - navigator.clipboard.writeText()
discription:
---

## 이벤트 페이지 만들기

## 공유하기

공유하기 버튼 클릭 시 현재 접속 해 있는 url을 사용자의 클립보드에 복사해주는 기능

```js
const shareURLButton = document.getElementById("url-share-button");

export const setShareURLButton = () => {
  shareURLButton.onclick = () => {
    // 현재 url을 클립보드에 복사
    navigator.clipboard.writeText(location.href);
  };
};
```
