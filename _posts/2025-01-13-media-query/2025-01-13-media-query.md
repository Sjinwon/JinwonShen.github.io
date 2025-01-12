---
layout: post
title: "(css) media query 를 최소화로 잡는 방법"
date: 2025-01-13 01:23:00 +09:00
lastmode: 2025-01-13 01:23:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - css
  - media query
  - grid
  - flexbox
  - rem
  - em
  - mobile first
discription:
---

오늘은 간단히 미디어 쿼리(media query)를 최소화로 잡을 수 있는 방법에 대해 알아보려고 한다.

## media query

**미디어 쿼리(Media Query)** 는 CSS에서 화면의 크기나 해상도, 디바이스의 특징에 따라 스타일을 다르게 적용할 수 있도록 도와주는 기능으로, 이를 통해 동일한 웹 페이지가 다양한 화면 크기(데스크톱, 태블릿, 스마트폰 등)에서 최적화된 디자인과 레이아웃을 제공할 수 있다.

<br>

### 1. 유연한 레이아웃 설계

**`CSS Grid` 또는 `Flexbox` 활용**

`Grid`와 `Flexbox`는 컨테이너 내 요소들을 유연하게 배치할 수 있어 다양한 화면 크기에 대응할 수 있다.

```css
.container {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}
.item {
  flex: 1 1 calc(33.333% - 16px);
}
```

이 방식은 media query 없이도 화면 크기에 따라 요소가 적절히 정렬된다.

<br>

**Fluid Layout 사용**

픽셀 기반 단위 대신 percent, `em`, `rem`, `vw`, `vh` 등 상대 단위를 활용한다.

```css
.container {
  width: 90%;
  max-width: 1200px;
}
```

<br>

### 2. 상대적 타이포그래피와 여백

**CSS 변수와 상대 단위**

텍스트 크기와 여백을 `em`, `rem` 또는 `calc()`를 활용해 설정하면 화면 크기에 따라 자동으로 조정된다.

```css
:root {
  --base-font-size: 1rem;
  --base-spacing: 1.5rem;
}
body {
  font-size: var(--base-font-size);
  padding: var(--base-spacing);
}
```

<br>

### 3. Modern CSS Features 활용

**clamp() 함수 사용**

요소의 크기나 여백을 `clamp()`로 정의하면 최소, 최대, 기본값을 지정하여 유연성을 제공한다.

```css
h1 {
  font-size: clamp(1.5rem, 2.5vw, 3rem);
}
```

<br>

### 4. 모바일 우선(Mobile first)접근 방식

기본 스타일을 모바일 기준으로 작성하고, 화면이 커질수록 필요한 경우에만 media query를 추가합니다.

```css
.header {
  font-size: 1rem; /* 기본 모바일 스타일 */
}
@media (min-width: 768px) {
  .header {
    font-size: 1.5rem; /* 태블릿 이상 */
  }
}
```

그 외에도 javascript를 통해 동적 스타일링을 구현하거나 브레이크 포인트를 구조화해 css변수를 활용하는 방법도 있다.

<br>

간단히 media query를 최소화 하는 방법에 대해 알아보았는데, 유연한 css설계, 최신 기술의 활용, 모바일 퍼스트 등을 적절히 결합해 사용하는 것이 좋을 것 같다. 이를 통해 코드를 간결하게, 유지보수를 용이하게 코드를 작성하는 습관을 들여야 겠다.
