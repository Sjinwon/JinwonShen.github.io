---
layout: post
title: "(javascript/js) 마켓서비스 만들기 : 상품 목록 구현"
date: 2025-01-07 10:04:00 +09:00
lastmode: 2025-01-07 10:04:00 +09:00
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

## 상품 목록 구현

상품카드 여러개가 묶여있는 상품 목록 구현

```js
// main.js
import { getProductCard } from "./module/productCard.js";

const productCard = getProductCard({
  id: 1,
  imgSrc: "/public/assets/파프리카.jpg",
  name: "파프리카 2입",
  discountPercent: 20,
  price: 2000,
  originalPrice: 2500,
});

document.body.appendChild(productSection);
```

기존에 만들어뒀던 `getProductCard` 함수에 요소를 넣어 `body.appendChild` 로 `productCard`를 넣어준다.

하지만 우리가 만들고자 하는 것은 상품카드가 아닌 상품 카드 목록이기 때문에,
`Product Card`를 묶어 `Product list container`를 만드는 유틸함수를 만들어준다.

<br>

```js
// productList.js
export const getProductList = (productInfoList) => {
  const productListContainer = makeDOMwithProperties("div", {
    className: "product-list-con",
  });

  productInfoList.forEach((productInfo) => {
    productListContainer.appendChild(getProductCard({ ...productInfo }));
    // spread 문법을 사용해 새로운 객체를 만들어 넘겨줄 수 있다.
  });

  return productListContainer;
};
```

<br>

```js
// main.js
import { getProductList } from "./module/productSection.js";

const productList = getProductList([
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

document.body.appendChild(productCard);
```
