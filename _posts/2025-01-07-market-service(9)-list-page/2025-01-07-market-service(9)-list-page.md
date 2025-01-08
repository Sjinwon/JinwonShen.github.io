---
layout: post
title: "(javascript/js) 마켓서비스 만들기 : 리스트 페이지 필터링"
date: 2025-01-07 16:20:00 +09:00
lastmode: 2025-01-07 16:20:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - reduce()
  - try
  - catch
discription:
---

## 마켓 서비스 만들기

## 리스트 페이지 목록

상품 전체 리스트 제공 및 해당 필터들의 조건에 맞는 상품들이 표시되는 리스트 페이지

```js
// main.js
import { getProductSection } from "./module/productSection.js";
import { fetchSectionListData } from "./module/fetch.js";

// 네트워크 요청
// fetch의 첫 번쨰 인자는 url ( 자원의 주소, 옵션(생략) )
// async await 구문 사용
// fetch에서 에러가 날 수 있으므로 try catch 를 통해 에러 핸들링
try {
  const sectionInfoList = await fetchSectionListData(); // 모듈화

  sectionInfoList.forEach((sectionInfo) => {
    console.log(sectionInfo);
    const { sectionTitle, productList } = sectionInfo;
    const productSectionDOM = getProductSection(sectionTitle, productList);
    document.body.appendChild(productSectionDOM);
  });
} catch (error) {
  console.log(error);
}

// document.body.appendChild(productSection);
```

<br>

```js
// 모듈화
// fetch.js
export const fetchSectionListData = async () => {
  try {
    const response = await fetch("./public/mock/sectionListData.json");
    const data = await response.json();
    return data?.sectionInfoList || [];
    // null undefined || defaultValue
  } catch (error) {
    console.log(error);
  }
};
```

<br>

모듈화한 `fetchSectionListData`의 데이터를 `reduce()` 함수를 활용해 `productList`에 할당해주고 할당한 요소를 `section`에 `append`해 화면에 뿌려주는

```js
// list.js
import { getProductList } from "./module/productList.js";
import { fetchSectionListData } from "./module/fetch.js";
// 이전 작업 삭제 ---
// 물품 목록 모두 불러오기 페이지에 출력 -> productList.js -> getProductList 함수사용

const sectionInfoList = await fetchSectionListData();

const productList = sectionInfoList.reduce(
  // prev + curr.productList 두 가지를 합한 새로운 배열 리턴
  (prev, curr) => [...prev, ...curr.productList],
  // ([...], )
  []
);

const section = document.getElementsByTagName("section")[0];
const productListDOM = getProductList(productList);
section.appendChild(productListDOM);
```

<br>

## 리스트 페이지 필터

상품 리스트 페이지에서 검색 버튼을 눌렀을 때 `min`, `max`, `discount` 값을 받아와 그 값을 이용해 해당하는 상품을 새롭게 추출하고 기존의 존재하던 리스트를 삭제해 다시 화면에 표시하는 코드를 구현

```js
// productFilter.js
import { makeDOMwithProperties } from "../utils/dom.js";
import { getProductList } from "./productList.js";

const MAX_PRICE = Number.MAX_VALUE;

const minPriceFilter = document.getElementById("price-min-filter");
const maxPriceFilter = document.getElementById("price-max-filter");
const discountFilter = document.getElementById("discount-filter");
const filterButton =
  document.getElementsByClassName("product-filter-con")[0]?.lastElementChild;
// button 요소 접근
// filter 버튼을 누름 -> min, max, discount 값을 받아옴 -> 값을 이용해 물품을 추출 -> 다시 화면에 나타냄

// 검색 버튼 클릭시
export const setButtonEvent = (productList) => {
  filterButton.onclick = () => {
    const maxPrice = maxPriceFilter.value || MAX_PRICE;
    const minPrice = minPriceFilter.value || 0;
    const discountRate = discountFilter.value || 0;

    // 조건에 통과하는 요소들만
    const newProductList = productList.filter((productInfo) => {
      const { price, discountPercent } = productInfo;
      return (
        price > minPrice && price <= maxPrice && discountRate <= discountPercent
      );
    });

    // section의 마지막 요소에 삽입
    const sectionDOM = document.getElementsByTagName("section")[0];
    const originalProductListDOM =
      document.getElementsByClassName("product-list-con")[0];
    sectionDOM.removeChild(originalProductListDOM);
    if (newProductList.length > 0) {
      // 화면에 표시될 물품이 있다는 것
      const productListDOM = getProductList(newProductList);
      sectionDOM.appendChild(productListDOM);
    } else {
      const emptyProductListDOM = makeDOMwithProperties("div", {
        className: "product-list-con empty",
        innerHTML: "조건에 해당하는 상품이 없습니다.",
      });
      sectionDOM.appendChild(emptyProductListDOM); // Remove
    }
  };
};
```

<br>
