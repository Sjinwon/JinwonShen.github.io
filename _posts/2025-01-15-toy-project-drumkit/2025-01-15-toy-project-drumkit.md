---
layout: post
title: "(toy-project) 드럼 만들기"
date: 2025-01-15 15:16:00 +09:00
lastmode: 2025-01-15 15:16:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - toy project
  - drum kit
  - Array.from
  - keycode
  - TransitionEvent
discription:
---

## drumkit

- 드럼 만들기 구현
- 키보드 입력으로 오디오 재생
- 클릭으로 오디오 재생

### 유사 배열 객체

- 배열이 아닌데 배열인척 하는 객체
- `querySelectorAll`로 dom을 선택하는 경우 Nodelist가 반환되는데, 이게 그 유사배열객체이다.
- 유사배열객체는 `Array.from` 을 사용해 배열로 만들 수 있다.

### keycode

https://keycode.info/

- 키보드 입력으로 keycode를 알 수 있는 사이트

### TransitionEvent

https://developer.mozilla.org/en-US/docs/Web/API/TransitionEvent

- transitionEvent가 끝날 때 발생

<br>

**따라하기**

```js
(function () {
  "use strict";

  const get = function (target) {
    return document.querySelector(target);
  };

  const getAll = function (target) {
    return document.querySelectorAll(target);
  };

  const keys = Array.from(getAll(".key"));

  const soundsRoot = "./assets/sounds/";
  const drumSounds = [
    { key: 81, sound: "clap.wav" },
    { key: 87, sound: "crash.wav" },
    { key: 69, sound: "hihat.wav" },
    { key: 65, sound: "kick.wav" },
    { key: 83, sound: "openhat.wav" },
    { key: 68, sound: "ride.wav" },
    { key: 90, sound: "shaker.wav" },
    { key: 88, sound: "snare.wav" },
    { key: 67, sound: "tom.wav" },
  ];

  const getAudioElement = (index) => {
    const audio = document.createElement("audio");
    audio.dataset.key = drumSounds[index].key;
    audio.src = soundsRoot + drumSounds[index].sound;
    return audio;
  };

  const playSound = (keycode) => {
    const $audio = get(`audio[data-key="${keycode}"]`);
    const $key = get(`div[data-key="${keycode}"]`);
    console.log($key);
    if ($audio && $key) {
      $key.classList.add("playing");
      $audio.currentTime = 0;
      $audio.play();
    }
  };

  const onkeydown = (e) => {
    console.log(e.keyCode);
    playSound(e.keyCode);
  };

  const onMouseDown = (e) => {
    const keycode = e.target.getAttribute("data-key");
    playSound(keycode);
  };

  const onTransitionEnd = (e) => {
    if (e.propertyName === "transform") {
      e.target.classList.remove("playing");
    }
  };

  const init = () => {
    window.addEventListener("keydown", onkeydown);
    keys.forEach((key, index) => {
      const audio = getAudioElement(index);
      key.appendChild(audio);
      key.dataset.key = drumSounds[index].key;
      key.addEventListener("click", onMouseDown);
      key.addEventListener("transitionend", onTransitionEnd);
    });
  };

  init();
})();
```
