---
layout: post
title: "(javascript/js) 마켓서비스 만들기 : 장바구니 페이지 목록, 전체 삭제"
date: 2025-01-07 13:48:00 +09:00
lastmode: 2025-01-07 13:48:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - insertBefore()
  - location.reload()
  - localStorage.removeItem()
  - localStorage.clear()
discription:
---

## 마켓 서비스 만들기

## 장바구니 페이지에 상품목록 구현

상품을 장바구니에 담았을 때 상품들이 장바구니 페이지로 이동해 상품 목록을 구성하는 것으로 구현하기

```js
// cart.js
import { CART_COOKIE_KEY } from "./constants/cart.js";
import { getCartInfo } from "./module/cartToggleButton.js";
import { getProductList } from "./module/productList.js";
import { makeDOMwithProperties } from "./utils/dom.js";

// 부모 -> section tag
// 뒤에 있는 요소 -> id : cart-pay-container
// 장바구니 내부에 있는 물품을 가져다가 -> product-list-con

// 1. 장바구니에 있는 물품정보 가져오기
// 2. 장바구니에 있는 물품정보를 productList와 연결하기
// 3. section 아래의 cart-pay-container 앞에 삽입하기

const sectionDOM = document.getElementsByTagName("section")[0];
const cartPayContainerDOM = document.getElementById("cart-pay-container");

const cartInfo = getCartInfo();

// 장바구니에서 상품을 삭제할때 바로 상품이 없어지지 않고 새로고침 해야하는 이슈 해결
const reloadPage = () => location.reload();

if (cartInfo.length < 1) {
  // 장바구니의 상품이 없다는 언지
  const noticeDOM = makeDOMwithProperties("div", {
    innerHTML: "장바구니에 상품이 없습니다.",
    className: "product-list-con",
  });
  sectionDOM.insertBefore(noticeDOM, cartPayContainerDOM);
} else {
  const productListDOM = getProductList(cartInfo, reloadPage);
  sectionDOM.insertBefore(productListDOM, cartPayContainerDOM);

  // A.insertBefore(B, C)
  // B가 A를 부모로 하며 C의 앞에 삽입되는 메서드
}
```

<br>

장바구니 상품 전체 삭제

```js
// cart.js
// 상품 전체 삭제
const cartAllDeleteButtonDOM = document.getElementById("remove-all-button");
cartAllDeleteButtonDOM.onclick = () => {
  // 전체삭제 버튼을 클릭했을 때 localStorage에 있는 물품 목록 정보 전부 삭제
  localStorage.removeItem(CART_COOKIE_KEY); // cartInfo 라는 키를 가진 키-값 쌍이 삭제
  localStorage.clear(); // localStorage 모든 키-값 쌍이 삭제
  location.reload(); // 새로고침
};
```
