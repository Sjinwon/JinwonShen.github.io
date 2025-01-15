---
layout: post
title: "(toy-project) 마켓서비스 만들기 : 상품 데이터 연결"
date: 2025-01-07 11:21:00 +09:00
lastmode: 2025-01-07 11:21:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - fetch
  - async
  - await
  - try
  - catch
discription:
---

## 마켓 서비스 만들기

## 상품 데이터 연결

JSON 데이터를 구성해 둔 것에서 데이터를 받아와서 현재 구현해 둔 컴포넌트와 연결하는 과정

```js
// main.js

// 이전에 임의로 데이터를 받아왔다고 가정하고 만들었던 부분
import { getProductSection } from "./module/productSection.js";

const productSection = getProductSection("인기 상품", [
  {
    id: 1,
    imgSrc: "/public/assets/파프리카.jpg",
    name: "파프리카 2입",
    discountPercent: 20,
    price: 2000,
    originalPrice: 2500,
  },
  {
    id: 2,
    imgSrc: "/public/assets/파스타.jpg",
    name: "롱 파스타 면 2종",
    discountPercent: 5,
    price: 7600,
    originalPrice: 8000,
  },
  {
    id: 3,
    imgSrc: "/public/assets/당근.jpg",
    name: "친환경 당근 400g",
    discountPercent: 33,
    price: 2000,
    originalPrice: 3000,
  },
]);

document.body.appendChild(productSection);
```

<br>

`index.html` 페이지 완성. 각각 `product card`부터 `product list`, `product section`까지 모두 모듈로 구성해 `product section`을 데이터와 연결해 반복되는 부분들을 제작

```js
import { getProductSection } from "./module/productSection.js";

// 네트워크 요청
// fetch의 첫 번쨰 인자는 url ( 자원의 주소, 옵션(생략) )
// async await 구문 사용
// fetch에서 에러가 날 수 있으므로 try catch 를 통해 에러 핸들링
try {
  const response = await fetch("./public/mock/sectionListData.json");
  const data = await response.json();
  const sectionInfoList = data.sectionInfoList;

  sectionInfoList.forEach((sectionInfo) => {
    console.log(sectionInfo);
    const { sectionTitle, productList } = sectionInfo;
    const productSectionDOM = getProductSection(sectionTitle, productList);
    document.body.appendChild(productSectionDOM);
  });
} catch (error) {
  console.log(error);
}
```
