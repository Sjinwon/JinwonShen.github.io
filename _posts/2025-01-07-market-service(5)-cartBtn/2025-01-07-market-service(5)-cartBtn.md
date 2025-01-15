---
layout: post
title: "(toy-project) 마켓서비스 만들기 : 장바구니 버튼 구현"
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

## 장바구니 버튼

클릭하면 장바구니로 들어가고, 장바구니 페이지로 이동할지 묻는 장바구니 버튼 구현.

그 외에도 클릭 되어있는 상태에서 다시 클릭했을 때 장바구니에서 상품을 삭제하는 기능

```js
import { makeDOMwithProperties } from "../utils/dom.js";
import { CART_COOKIE_KEY } from "../constants/cart.js";

const isInCart = ({ id }) => {
  // 현재 해당 상품이 장바구니 안에 있는지 판단해 결과를 반환
  const originalCartInfo =
    JSON.parse(localStorage.getItem(CART_COOKIE_KEY)) || [];
  // Array.find
  return !!originalCartInfo.find((cartInfo) => cartInfo.id === id); // 이미 가지고 있는 id값이 있다면 true
};

const addCartInfo = (productInfo) => {
  console.log("addCartInfo");
  const originalCartInfo =
    JSON.parse(localStorage.getItem(CART_COOKIE_KEY)) || [];
  // null undefined || []

  if (
    originalCartInfo.findIndex((cartInfo) => cartInfo.id === productInfo.id) !==
    -1
  )
    return;

  localStorage.setItem(
    CART_COOKIE_KEY,
    JSON.stringify([...originalCartInfo, productInfo])
  );
};

const removeCartInfo = ({ id }) => {
  // 장바구니에서 해당 물품의 정보를 삭제
  const originalCartInfo =
    JSON.parse(localStorage.getItem(CART_COOKIE_KEY)) || [];
  const newCartInfo = originalCartInfo.filter((cartInfo) => cartInfo.id !== id);
  // false);
  // Array.filter()
  localStorage.setItem(CART_COOKIE_KEY, JSON.stringify(newCartInfo));
};

export const getCartToggleButton = (productInfo) => {
  let inCart = isInCart(productInfo);
  const cartToggleBtn = makeDOMwithProperties("button", {
    className: "cart-toggle-btn",
    type: "button",
    onclick: () => {
      if (inCart) {
        // 이미 장바구니에 들어있다면 -> 장바구니에서 삭제
        if (!confirm(`[${productInfo.name}]을 장바구니에서 삭제할까요 ?`))
          return; // early return 조건에 맞지 않으면 빠르게 리턴해주면 된다.
        // true 삭제 false 삭제x
        removeCartInfo(productInfo);
        cartImage.src = "./public/assets/cart.png";
      } else {
        addCartInfo(productInfo);
        cartImage.src = "./public/assets/cartDisabled.png";

        if (confirm("장바구니에 담았습니다. 장바구니 페이지로 이동할까요 ?")) {
          location.href = "./cart.html";
        }
      }
      inCart = !inCart;
    },
  });

  const cartImage = makeDOMwithProperties("img", {
    className: "cart-image",
    src: inCart
      ? "./public/assets/cartDisabled.png"
      : "./public/assets/cart.png",
  });

  cartToggleBtn.appendChild(cartImage);

  return cartToggleBtn;
};
```
