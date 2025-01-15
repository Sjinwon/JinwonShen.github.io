---
layout: post
title: "(toy-project) 마켓서비스 만들기 : 상품 카드 구현"
date: 2025-01-06 23:44:00 +09:00
lastmode: 2025-01-06 23:44:00 +09:00
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

## 상품 카드 구현

`DOM`으로 `Product Card`를 생성하고 페이지에 보여주는 것까지 진행.

```js
// list.js
// 유틸함수 적용 전 예시
const productCard = document.createElement("div");
productCard.className = "product-card";

const productImageCon = document.createElement("div");
productImageCon.className = "product-image-con";
const productImage = document.createElement("img");
productImage.src = "/public/assets/파프리카.jpg";
productImage.alt = "파프리카";
const
```

`document.createElement()`, `className`, `src`, `alt` 등 할당해야 할 것들이 반복이 되기 때문에 재사용을 위해 하나의 `util`로 만들어 사용

<br>

```js
// list.js

// 해당 위치에 append 해준다.
const sectionDOM = document.getElementByTagName("section");

// 유틸로 만들어 사용할 때
const productCard = makeDOMwithProperties(div, {
  className: "product-card",
});

// product-image-container --
const productImageCon = makeDOMwithProperties(div, {
  className: "product-image-con",
});
const productImage = makeDOMwithProperties(img, {
  src: "./public/assets/파프리카.jpg",
  alt: "파프리카",
});
const cartToggleBtn = makeDOMwithProperties(button, {
  className: "cart-toggle-btn",
  type: "button",
});
const cartImage = makeDOMwithProperties(img, {
  className: "cart-image",
  src: "./public/assets/cart.png",
});
// 유틸 함수를 사용하기 전
// cartToggleBtn.appendChild(cartImage);
// productImageCon.appendChild(productImage);
// productImageCon.appendChild(cartToggleBtn);
// ...
// 이 부분도 재사용이 필요할 것 같아 유틸 함수를 만들어 준다.

// 유틸 함수를 통해 재사용
appendChildrenList(productImageCon, [productImage, cartToggleBtn]);

// product-description --
const productDescription = makeDOMwithProperties("div", {
  className: "product-description",
});
const productName = makeDOMwithProperties("div", {
  className: "product-name",
  innerHTML: "파프리카",
});

const productPriceContainer = makeDOMwithProperties("div", {
  className: "product-price-con",
});
const productDiscount = makeDOMwithProperties("div", {
  className: "product-discount-percent",
  innerHTML: "20%",
});
const productPrice = makeDOMwithProperties("div", {
  className: "product-price",
  innerHTML: "200원",
});
const productOriginalPrice = makeDOMwithProperties("div", {
  className: "product-original-price",
  innerHTML: "2500원",
});

appendChildrenList(productPriceContainer, [productDiscount, productPrice]);
appendChildrenList(productDescription, [
  productName,
  productPriceContainer,
  productOriginalPrice,
]);
appendChildrenList(productImageCon, [productImageCon, productDescription]);

// 해당 위치에 append 해준다.
sectionDOM.appendChild(productCard);
```

<br>

```js
// utils folder
// dom.js 유틸로 만들어 사용하기 위해
export const makeDOMwidthProperties = (domType, propertyMap) => {
  // domType : div, a, li...
  // propertyMap : { "className : "product-card" ...} 등의 오브젝트
  // Object.keys(propertyMap) -> [ "className", "alt" ... ]
  const dom = document.createElement(domType);
  Object.keys(propertyMap).forEach((key) => {
    dom[key] = propertyMap[key];
  });
  // object 객체 안에있는 keys라는 매서드로 오브젝트라는 객체의 키만 모아 배열로 넘겨주는 메서드

  return dom;
};

// target으로 전달된 node에 child를 붙여주는데 childrenList로 전달된 배열에 들어있는 children을 하나씩 붙여주는 함수
export const appendChildrenList = (target, childrenList) => {
  if (!Array.isArray(childrenList)) return;
  // early return : childrenList가 배열이 아닌경우 바로 반환

  childrenList.forEach((children) => {
    target.appendChild(children);
  });
};
```

<br>

모듈화 작업을 위해 모듈 폴더 내 새로운 파일을 만들고, 기존 `list.js` 에서 요소를 만들고 속성을 추가하는 `makeDOMwithProperties` 메서드를 그대로 `getProductCard` 함수안에 넣어 구조분해할당으로 요소를 받아 사용한다.

```js
// module folder
// product-card.js
export const getProductCard = () => {
  const { imgSrc, name, discountPercent, price, originalPrice } = productInfo;
  // product card
  const productCard = makeDOMwithProperties("div", {
    className: "product-card",
  });

  // product image cont
  const productImageCon = makeDOMwithProperties("div", {
    className: "product-image-con",
  });

  const productImage = makeDOMwithProperties("img", {
    src: imgSrc,
    alt: name,
  });

  const cartToggleBtn = getCartToggleButton(productInfo);

  appendChildrenList(productImageCon, [productImage, cartToggleBtn]);

  // product desc
  const productDescription = makeDOMwithProperties("div", {
    className: "product-description",
  });

  const productName = makeDOMwithProperties("div", {
    className: "product-name",
    innerHTML: `${name}`,
  });

  const productPriceContainer = makeDOMwithProperties("div", {
    className: "product-price-con",
  });

  const productDiscount = makeDOMwithProperties("div", {
    className: "product-discount-percent",
    innerHTML: `${discountPercent}%`,
  });

  const productPrice = makeDOMwithProperties("div", {
    className: "product-price",
    innerHTML: `${price.toLocaleString()}원`,
  });

  const productOriginalPrice = makeDOMwithProperties("div", {
    className: "product-original-price",
    innerHTML: `${originalPrice.toLocaleString()}원`,
  });

  appendChildrenList(productPriceContainer, [productDiscount, productPrice]);

  appendChildrenList(productDescription, [
    productName,
    productPriceContainer,
    productOriginalPrice,
  ]);

  appendChildrenList(productCard, [productImageCon, productDescription]);

  return productCard;
};
```

<br>

`list.js` 에서 `dom.js`에서 만든 `getProductCard`를 받아와 `section` 태그를 사용하는 노드중 첫 번째 노드를 받아와 마지막 요소로 삽입한다.

```js
const sectionDOM = document.getElementsByTagName("section")[0];

const productCard = getProductCard({
  id: 1,
  imgSrc: "/public/assets/파프리카.jpg",
  name: "파프리카 2입",
  discountPercent: 20,
  price: 2000,
  originalPrice: 2500,
});
const productCard2 = getProductCard({
  id: 2,
  imgSrc: "/public/assets/파스타.jpg",
  name: "롱 파스타 면 2종",
  discountPercent: 5,
  price: 7600,
  originalPrice: 8000,
});
const productCard3 = getProductCard({
  id: 3,
  imgSrc: "/public/assets/당근.jpg",
  name: "친환경 당근 400g",
  discountPercent: 33,
  price: 2000,
  originalPrice: 3000,
});
const productCard4 = getProductCard({
  id: 4,
  imgSrc: "/public/assets/머핀.jpg",
  name: "[홍대 W마켓] 컵케익 (2입)",
  discountPercent: 20,
  price: 4800,
  originalPrice: 6000,
});
const productCard5 = getProductCard({
  id: 5,
  imgSrc: "/public/assets/단호박.jpg",
  name: "단호박 1통",
  discountPercent: 12,
  price: 3520,
  originalPrice: 4000,
});

sectionDOM.appendChild(productCard);
sectionDOM.appendChild(productCard2);
sectionDOM.appendChild(productCard3);
sectionDOM.appendChild(productCard4);
sectionDOM.appendChild(productCard5);
```
