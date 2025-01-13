---
layout: post
title: "(css) animation 최적화 기법에 대해"
date: 2025-01-13 00:02:00 +09:00
lastmode: 2025-01-13 00:02:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - html
  - css
  - reflow
  - repaint
  - will-change
  - animation
  - transform
discription:
---

## animation 최적화 기법

오늘은 간단히 css animation의 최적화 기법에 대해 알아보려고한다. javascript로도 애니메이션 제작이 가능하지만 오늘은 애니메이션을 사용했을 때 성능이 떨어지는 이유와 `reflow`, `repaint`란 무엇인지 알아보자.

<br>

> UX 엔지니어이자 자바스크립트 개발자인 윌 보이드(Will Boyd)는 조금 더 부드러운 애니메이션을 만들 수 있는 방법과 그 예시를 소개하였습니다.(참고 영상, 참고 슬라이드)
> “브라우저에서 하나의 애니메이션 프레임을 처리한다는 것은 애니메이션 구현에 필요한 모든 계산 과정과 계산을 통해 얻어진 픽셀 자리를 업데이트 하는 것까지 포함합니다. 목표는 브라우저가 이 과정에서 할일을 최대로 줄여서 초당 60프레임 정도의 부드러운 애니메이션을 만드는 것입니다. CSS 애니메이션 구현 비용과 직접적으로 연관되어 있는 것은 재조정(reflow)와 재색칠(repaint)를 일으키지 않는 속성들입니다.”
> 즉, 부드러운 애니메이션을 적용하려면 reflow와 repaint를 최소화 시켜야 합니다.

<br>

### reflow란?

생성된 DOM 노드의 레이아웃(너비, 높이 등) 변경 시 영향받는 모든 노드(자식, 부모)의 수치를 다시 계산하여 렌더 트리를 재생성하는 작업입니다.

### repaint란?

reflow 과정이 끝난 후 재생성된 렌더 트리를 다시 그리는 작업으로 수치와 상관없는 background-color, visibility, outline 등의 스타일 변경시에는 reflow 과정이 생략 된 repaint 작업만 수행합니다.

<br>

### reflow와 repaint가 발생하는 속성은?

**reflow가 발생하는 속성**

`width` `height`
`padding` `margin`
`display` `border-width`
`border` `top`
`position` `font-size`
`float` `text-align`
`overflow-y` `font-weight`
`overflow` `left`
`font-family` `line-height`
`vertical-align` `right`
`clear` `white-space`
`bottom` `min-height`

**repaint가 발생하는 속성**

`color` `border-style`
`visibility` `background`
`text-decoration` `background-image`
`background-position` `background-repeat`
`outline-color` `outline`
`outline-style` `border-radius`
`outline-width` `box-shadow`
`background-size`

<br>

### reflow 혹은 repaint를 잡을 수 있는 방법은?

기본적으로 애니메이션 요소에 `position`을 사용하는 것으로도 `reflow`를 줄일 수 있다. `position`을 사용해 요소를 잡아줄 때 다른 요소에 영향을 끼치지 않아 전체 웹 페이자가 `reflow`되는 것이 아니라 해당 요소에만 발생해 결과적으로 `reflow`를 줄일 수 있다.

아래는 그 외의 방법들이다.

<br>

**js를 통해 스타일 변화를 주어야 할 경우, 가급적 한 번에 처리한다.**

```js
// style 객체를 여러번 호출해 적용한 코드
let div = document.getElementsByTagName("div");
for (let i = 0; i < div.length; i++) {
  div[i].style.height = "80px";
  div[i].style.backgroundColor = "#00f";
  div[i].style.display = "inline-block";
  div[i].style.overflow = "hidden";
  div[i].style.fontSize = "40px";
  div[i].style.color = "#fff";
}
```

<br>

```js
// CSS에 정의된 class를 통해 한번에 적용한 코드
let div = document.getElementsByTagName("div");
for (let i = 0; i < div.length; i++) {
  div[i].className = "block";
}
```

<br>

**인라인 스타일을 최대한 배제한다.**

위의 내용과 중복되며, `reflow` 비용을 줄이는 것 이외에 코드 가독성도 높일 수 있다.

<br>

**테이블 레이아웃을 피하는 것이 좋다.**

테이블 레이아웃을 사용하게 되면 테이블 값에 따라 넓이를 계산하므로 랜더링이 느려진다. 그러므로 꼭 필요한 경우를 제외하고는 테이블 레이아웃을 사용하지 않는 것이 좋다. 만약 사용한다면 `CSS` 속성 `table-layout:fixed`를 사용하면 랜더링을 조금 더 빠르게 할 수 있다.

<br>

**CSS 하위선택자는 필요한 만큼 정리하는 것이 좋다.**

`reflow` 자체보다는 `reflow`가 유발시키는 `CSS Recalculation`에 필요한 내용이다. `CSS` 규칙은 오른쪽에서 왼쪽으로 이동한다. 이 과정에서는 더 이상 일치하는 규칙이 없거나 잘못된 규칙이 나올 때 까지 계속된다. 그러므로 불필요한 선택자를 사용하는 것은 성능을 저하시킬 수 있다.

<br>

```html
<div class="reflow_box">
  <ul class="reflow_list">
    <li>
      <button type="button" class="btn">버튼</button>
    </li>
    <li></li>
    <li>
      <button type="button" class="btn">버튼</button>
    </li>
    <li></li>
  </ul>
</div>
```

<br>

```css
/* 잘못된 예 */
.reflow_box .reflow_list li .btn {
  display: block;
}
/* 올바른 예 */
.reflow_list .btn {
  display: block;
}
```

<br>

**transform에서 3d 사용하기**

`transform`은 부모 요소의 영향을 받지 않으면서 독자적으로 요소의 위치 및 확대, 축소, 회전이 가능한 css 요소이다. 그렇기 때문에 `transform`을 사용하면 브라우저에서 GPU(그래픽 처리 장치)를 사용하며 이것은 CPU에 부담을 덜어줄 수 있기 때문에 성능을 저하하지 않게 된다. 또 `transform` 3d의 경우에는 x,y,z 축으로 3차원상에서 이동할 수 있기 때문에 자연스러운 움직임을 구현해낼 수 있다.

<br>

### will-change 사용하기

`will-change`는 `css`의 속성이다. 이름에서 예측할 수 있듯이 미래에 값에 변화가 있을 것을 미리 알려줘서 최적화할 수 있도록 도와준다. 변화가 일어날 요소의 속성으로 넣어주면 된다. 값으로는 변화가 일어날 속성을 넣어줄 수 있다.

**will-change의 속성 4가지**

```css
will-change: auto;
will-change: scroll-position;
will-change: contents;
will-change: transform;
will-change: top, left;
```

<br>

- auto : 기본값으로 브라우저는 별다른 최적화를 실시하지 않습니다.
- scroll-position : 스크롤 할 때 엘리먼트의 위치가 변경될 것을 알려줍니다. 이 값을 설정하면 브라우저는 스크롤 가능한 엘리먼트를 미리 최적화 하여 랜더링 합니다. 한 번에 많은 양을 스크롤하거나 빠른 스크롤이 필요한 경우에 사용합니다.
- contents : 엘리먼트의 컨텐츠가 변경될 것을 알려줍니다. 브라우저는 보통 엘리먼트의 랜더링 결과를 캐싱합니다. 대부분의 엘리먼트가 변경되지 않고 변경되어도 위치가 바뀌는 정도의 미미한 변경만 발생하기 때문입니다. 하지만 엘리먼트가 계속해서 변경되는 경우 브라우저 캐시는 무의미하게 됩니다. 이 속성을 사용하게 되면 캐시를 하지 않고 변경될 때마다 처음부터 랜더링하게 됩니다.
- custom-ident : 변경하고 싶은 속성을 사용할 수 있습니다. 쉼표(,)를 이용하여 두 개 이상의 속성을 사용할 수 있습니다. 크롬에서는 현재 6가지 속성(opacity, transform, top, left, right, bottom)만 적용됩니다.

will-change을 사용할 때에도 너무 많은 속성과 요소에 will-change속성을 사용하는 것은 좋지 않다. 브라우저는 모든 요소에 대해 이미 최적화를 시키려고 시도하고 있고, 최적화를 하기 위해 많은 자원을 소모하기 때문

<br>

[블로그 참고](https://wit.nts-corp.com/2017/06/05/4571)

<br>
