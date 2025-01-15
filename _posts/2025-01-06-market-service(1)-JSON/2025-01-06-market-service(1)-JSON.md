---
layout: post
title: "(toy-project) 마켓서비스 만들기 : JSON구성"
date: 2025-01-06 23:44:00 +09:00
lastmode: 2025-01-06 23:44:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - Market Service
  - JSON
discription:
---

## 마켓 서비스 만들기

마켓 서비스 사이트를 제로베이스 강의에 따라 같이 제작해보고 배운 것들을 정리해보려고 한다. 배열 메서드, location 객체, 이벤트 핸들링, localStorage, DOM 다루기, 네트워크 통신, 데이터 저장(JSON)등을 다룰 예정.

## JSON 구성

`api` 데이터가 구축이 덜 되었을 때, 미리 클라이언트 구현을 해놓기 위해 `api`에서 이러한 데이터가 내려올 것이라 가정하고 `mock data`라는 느낌으로 `json`파일을 만들어서 미리 구현한다.

```json
// sectionListData.json
{
  "sectionInfoList": [
    // section의 정보를 담고있는 section이 여러개가 나타날 수 있기 때문에
    // section여러개를 다룰 수 있는 배열로 나타낼 것.
    {
      "sectionTitle": "인기 상품",
      // 아래로 상품 정보를 담아준다.
      "productList": [
        {
          "id": 1,
          "imgSrc": "/public/assets/파프리카.jpg",
          "name": "파프리카 2입",
          "discountPercent": 20,
          "price": 2000,
          "originalPrice": 2500
        },
        {
          "id": 2,
          "imgSrc": "/public/assets/파스타.jpg",
          "name": "롱 파스타 면 2종",
          "discountPercent": 5,
          "price": 7600,
          "originalPrice": 8000
        },
        {
          "id": 3,
          "imgSrc": "/public/assets/당근.jpg",
          "name": "친환경 당근 400g",
          "discountPercent": 33,
          "price": 2000,
          "originalPrice": 3000
        },
        {
          "id": 4,
          "imgSrc": "/public/assets/머핀.jpg",
          "name": "[홍대 W마켓] 컵케익 (2입)",
          "discountPercent": 20,
          "price": 4800,
          "originalPrice": 6000
        },
        {
          "id": 5,
          "imgSrc": "/public/assets/단호박.jpg",
          "name": "단호박 1통",
          "discountPercent": 12,
          "price": 3520,
          "originalPrice": 4000
        }
      ]
    }
  ]
}
```

<br>
