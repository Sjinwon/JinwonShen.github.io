---
layout: post
title: "(toy-project) 마켓서비스 만들기 : 장바구니 페이지 결제"
date: 2025-01-07 14:34:00 +09:00
lastmode: 2025-01-07 14:34:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - 삼항연산자
discription:
---

## 마켓 서비스 만들기

## 장바구니 페이지 결제

상품을 장바구니에 담았을 때의 상품 금액과 할인금액, 2만원 미만일 떄의 배송비와 최종 결제금액 구현

```js
import { getCartInfo } from "./cartToggleButton.js";

// -이상 무료 배송
const DELIVERY_FREE_PRICE = 20000;

// 배송비 3000
const DELIVERY_PRICE = 3000;

// 결제
const originalPriceDOM = document.getElementById("original-price");
const discountPriceDOM = document.getElementById("discount-price");
const deliveryPriceDOM = document.getElementById("delivery-price");
const totalPriceDOM = document.getElementById("total-price");

/* 
{
  "id": 1,
  "imgSrc": "/public/assets/파프리카.jpg",
  "name": "파프리카 2입",
  "discountPercent": 20,
  "price": 2000,
  "originalPrice": 2500
},
*/
export const setPayInfo = () => {
  // 1. 장바구니에서 물품 정보 얻어오기
  // 2. 물품 정보들을 순회하면서 총 가격, 할인된 가격, 배송비, 결제 금액을 계산하기
  // 3. 2번에서 계산된 금액들을 미리 받아둔 DOM에 innerHTML로 할당

  const cartInfoList = getCartInfo();

  // 총 가격은 cartInfo 안에있는 price
  // let originalPrice = 0; // 총 가격 초기화
  // let discountPrice = 0; // 할인된 가격
  let deliveryPrice = 0; // 2만원 미만 구매시 배송비 3000 <-> 2만원 이상은 0
  let totalPrice = 0; // 결제 금액

  const { originalPrice, discountPrice } = cartInfoList.reduce(
    (prev, curr) => ({
      originalPrice: prev.originalPrice + curr.originalPrice,
      discountPrice: prev.discountPrice + (curr.originalPrice - curr.price),
    }),
    {
      originalPrice: 0,
      discountPrice: 0,
    }
  );

  /* cartInfoList.forEach((cartInfo) => {
    originalPrice += cartInfo.originalPrice; // 복합할당연산자
    // 할인된 가격 -> 원래 가격(original price) - 판매가격(price)
    discountPrice += cartInfo.originalPrice - cartInfo.price;
    // 실제 총 상품 금액은 = 원래 가격들의 합 - 할인된 가격들의 합
  }); */

  // 배열.reduce()

  const payPrice = originalPrice - discountPrice;
  if (payPrice >= DELIVERY_FREE_PRICE) {
    deliveryPrice = 0;
  } else {
    deliveryPrice = DELIVERY_PRICE;
  }
  totalPrice = payPrice + deliveryPrice;

  originalPriceDOM.innerHTML = `${originalPrice.toLocaleString()}원`;
  discountPriceDOM.innerHTML = discountPrice
    ? `-${discountPrice.toLocaleString()}원`
    : "0원";
  deliveryPriceDOM.innerHTML = deliveryPrice
    ? `+${deliveryPrice.toLocaleString()}원`
    : "0원";
  totalPriceDOM.innerHTML = `${totalPrice.toLocaleString()}원`;
};
```
