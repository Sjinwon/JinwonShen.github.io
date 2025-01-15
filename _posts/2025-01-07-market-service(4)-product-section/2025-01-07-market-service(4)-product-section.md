---
layout: post
title: "(toy-project) 마켓서비스 만들기 : 상품 섹션 구현"
date: 2025-01-07 10:52:00 +09:00
lastmode: 2025-01-07 10:52:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - module
  - createElement()
  - appendChild()
  - early return
discription:
---

## 마켓 서비스 만들기

## 상품 섹션 구현

상품 섹션 구현

```js
// productSection.js
const getProductSection = (sectionName, productInfoList) => {
  const productListSection = makeDOMwithProperties("div", {
    className: "product-list-section",
  });

  const sectionTitle = makeDOMwithProperties("div", {
    className: "section-title",
  });
  const titleHeighLight = makeDOMwithProperties("span", {
    className: "section-title-highlight",
  });
  const title = makeDOMwithProperties("span", {
    innerHTML: sectionName,
  });

  appendChildrenList(sectionTitle, [titleHeighLight, title]);
  const productListContainer = getProductList(productInfoList);
  appendChildrenList(productListSection, [sectionTitle, productListContainer]);

  return productListSection;
};
```

<br>

```js
// main.js
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
