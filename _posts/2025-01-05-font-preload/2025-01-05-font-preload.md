---
layout: post
title: "(javascript/js) Font Preload"
date: 2025-01-05 19:16:00 +09:00
lastmode: 2025-01-05 19:16:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - javascript
  - js
  - font
  - font preload
  - web font
discription:
---

## FOIT와 FOUT

`font preload`를 알아보기 전에 `FOIT`와 `FOUT`라는 용어에 대해 먼저 알아보자.

### FOIT(Flash of Invisible Text)란?

브라우저가 웹페이지가 로드될 때, 웹 폰트가 아직 다운로드 되지 않았기 때문에, 일시적으로 텍스트가 화면에 표시되지 않는 현상을 말한다.

특히 대체 폰트가 지정되지 않은 경우 브라우저가 폰트를 렌더링하기 위해 다운로드를 기다리는 동안 발생할 수 있다.

<br>

### FOUT(Flash of Unstyled Text)란?

웹 페이지에서 원하는 글꼴이 로드되기 전에 브라우저에서 기본 글꼴로 표시되는 현상을 의미한다.

<br>

```
이와같은 폰트 이슈가 생기는 이유는 브라우저 동작에 있어 폰트 리소스 요청의 경우 다른 리소스 요청보다 늦게 처리되기 때문이라고 한다.
따라서 웹 사이트를 구성하는 사이에 폰트 리소스 응답이 늦어버리게 되면 해당 폰트 이슈가 발생하게 되는 것.
```

<br>

## 그럼 Font Preload란 무엇일까 ?

_**먼저, preload란 웹 페이지에서 사용될 리소스를 미리 가져오는 기술이다.**_
즉, 웹 페이지가 로드되기 전에 필요한 리소스를 미리 다운로드하여, 사용자가 페이지를 더 빠르게 볼 수 있도록 한다.

예를 들어, css파일이 페이지가 로드될 때 필요하다면, Preload를 사용해 css 파일을 미리 가져와 페이지 로드 시간을 줄일 수 있다.

- 대용량 이미지, 비디오, 폰트 등 웹 페이지 로딩 속도에 큰 영향을 미치는 리소스를 Preload를 사용해 미리 가져오면, 페이지 로딩 시간을 크게 단축할 수 있다.
- 예를 들어, 웹 페이지에서 많은 양의 데이터를 보여줄 떄, 이를 비동기적으로 불러오기 위해 라이브러리를 사용하는 경우가 있다. 이 떄, 필요한 라이브러리 파일을 Preload를 사용해 미리 가져와, 불필요한 로딩 시간을 줄일 수 있다.

<br>

```
Font Preload란 웹 사이트에서 사용되고 있는 폰트 파일을 브라우저에 미리 로드하는 방법(기술)이다.
```

<br>

### Font를 Preload를 사용해 미리 다운로드하면 얻을 수 있는 8가지 장점

1. 빠른 로딩 : Font Preload 를 사용하면 폰트를 미리 로드할 수 있으므로 웹 사이트의 로딩 속도가 빨라진다.
2. 경험 개선 : 빠른 로딩은 사용자 경험을 향상시키고, 웹 사이트의 이탈률을 줄일 수 있다.
3. 캐싱 : Font Preload를 사용하면 폰트 파일이 캐시된다. 따라서 다음 방문시에는 더 빠르게 로드된다.
4. 독립성 : Font Preload는 웹 폰트 서비스에 독립적이다. 따라서 웹 폰트 서비스를 사용하지 않고 자체적으로 제작한 폰트를 사용하는 경우에도 사용할 수 있다.
5. 유연성 : Font Preload는 여러가지 파일 형식을 지원한다. 이를테면, TrueType, OpenType, WOFF, WOFF2 등이 있다.
6. 지연 시간 감소 : Font Preload를 사용하면 서버와의 지연 시간을 줄일 수 있다.
7. 선저장(Pre-fetching) : Font Preload는 폰트 파일을 미리 로드하는 것이므로, 필요하지 않은 폰트 파일을 다운로드할 필요가 없어진다. 이는 대역폭 절약에 도움이 된다.
8. 권장 : Font Preload는 웹사이트의 성능 개선을 권장하는 기술이다. 브라우저의 크로스 브라우저 지원도 잘 되어 있으므로, 대부분의 웹사이트에서 사용할 수 있다.

<br>

## 로컬 폰트만 있을 때 로딩하는 방법

로컬엡 설치된 폰트를 사용할 경우, 폰트를 다운로드할 필요 없이 로컬에서 빠르게 로딩할 수 있다. 이를 이용하기 위해 css @font-face를 사용해 로컬 폰트를 불러올 수 있다.

local font를 적용하려면 다음과 같은 방법을 사용할 수 있다.

<br>

**@font-face 를 사용하면 된다.**

기본적인 문법은 아래와 같다.

```css
@font-face {
  font-family: "FontName";
  src: url("fontname.eot"); /* IE9 Compat Modes */
  src: url("fontname.eot?#iefix") format("embedded-opentype"), /* IE6-IE8 */
      url("fontname.woff2") format("woff2"),
    /* Super Modern Browsers */ url("fontname.woff") format("woff"), /* Modern Browsers */
      url("fontname.ttf") format("truetype"),
    /* Safari, Android, iOS */ url("fontname.svg#svgFontName") format("svg"); /* Legacy iOS */
  font-weight: normal;
  font-style: normal;
}
```

위 코드에서는 폰트 이름, 폰트 파일의 경로 및 형식, 폰트 굵기 및 스타일을 지정한다.
폰트 파일은 로컬 또는 원격 서버에 위치할 수 있다.

**그리고 다음과 같이 사용이 가능하다.**

```css
body {
  font-family: "FontName", sans-serif;
}
```

정의된 폰트 이름과 대체폰트를 지정해준다.

<br>

## 웹 폰트로만 로딩해야 하는 상황에서 최적화 기법

### Preload를 이용한 폰트 리소스 우선적으로 로드하기

브라우저 동작에 있어 폰트 리소스 요청의 경우에는 다른 리소스 요청보다 늦게 실행된다. 이러한 이유로 폰트 이슈가 생기기에 폰트 리소스를 앞당기는 폰트 최적화 방법이 존재한다.

```html
<link rel="preload" href="url" as="font" type="font/woff2" crossorigin />
```

<br>

다음 형식의 `<link>`태그를 `html`의 `<head>`안에 추가한다. 해당 코드를 적용하면 브라우저는 가장 마지막이었던 폰트 리소스를 가장 우선적으로 로드하게 된다.

하지만 preload를 통한 폰트 최적화가 가장 좋은 방법은 아니다. 만약 많은 양의 폰트 리소스를 하게 되면 preload적용 전과 같이 로딩 시간이 다시 길어지기 때문에 해당 방식을 사용한 이유가 없어지게 된다.

**상황에 맞게 올바른 폰트 최적화를 사용해야 한다.**

<br>

### 압축률이 좋은 웹 폰트 확장자 사용하기

_**폰트 용량을 줄여, 폰트 로딩시간을 줄이는 방법.**_

웹 폰트 확장자 중에서 압축률이 좋은 폰트를 사용하는 것으로, 현재는

1. WOFF2 (Web Open Font Format 2) : WOFF는 현재 가장 인기있는 웹 폰트 형식 중 하나이다. WOFF2는 이전 버전인 WOFF1보다 약 30-50% 정도 압축률이 좋다. 또한 대부분의 브라우저에서 지원된다. 따라서 WOFF2 확장자를 사용해 웹 폰트를 배포하는 것이 좋다.
2. WOFF (Web Open Font Format) : WOFF는 WOFF2보다 덜 압축되지만 여전히 압축률이 좋은 웹 폰트 형식이다. 대부분의 브라우저에서 지원되며 WOFF2를 사용할 수 없는 경우 WOFF를 대안으로 사용할 수 있다.
3. TTF/OTF(TrueType/OpenType Font) : TTF및 OTF는 웹 폰트로 사용할 수 있지만, WOFF2또는 WOFF보다 압축률이 낮다. 또한 모든 브라우저에서 지원되지 않을 수 있다. 그러나 TTF또는 OTF확장자를 사용해야 하는 경우, Gzip또는 Brotli와 같은 압축 기술을 사용해 압축률을 높일 수 있다.

<br>

> 따라서 WOFF2를 사용하는 것이 가장 좋다.
> WOFF2가 지원되지 않는 브라우저의 경우, WOFF를 대안으로 사용할 수 있다. 어쩔 수 없이 TTF또는 OTF확장자를 사용해야 하는 경우라면 압축 기술을 사용해 압축률을 높이는 것이 좋다.

<br>

### 서브셋 폰트 사용

**_서브셋 폰트는 전체 글꼴에서 필요한 문자만 선택하여 사용하는 것을 말한다._**

서브셋 폰트를 사용하면 전체 글꼴의 용량을 줄일 수 있으며, 웹 사이트나 앱 등에서 더 빠른 로딩 속도와 더 적은 대역폭 사용을 가능하게 한다.

서브셋 폰트를 만들기 위해서는 서브셋 폰트를 만들어주는 사이트를 이용하면 된다.

<br>

> Font를 preload하는 방법을 알아보았는데, 폰트의 용량을 줄여야 하는 이유 혹은 압축률이 좋은 폰트 확장자 들을 알아갈 수 있어 좋았던 것 같다. 유익한 시간이었고, 서브셋 폰트를 만들 수 있는 사이트정도는 몇가지 알아두는 것이 좋을 것 같다.

<br>

[출처링크 운동과개발blog](https://adeveloperstory.tistory.com/entry/%EC%9B%B9%EA%B0%9C%EB%B0%9C%EA%B8%B0%EC%88%A0-Font%EB%A5%BC-Preload-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)

<br>
