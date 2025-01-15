---
layout: post
title: "(toy-project) 야구 게임"
date: 2025-01-15 14:49:00 +09:00
lastmode: 2025-01-15 14:49:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - toy project
  - baseball game
  - strict mode
  - calc()
  - object destructuring
  - new Set()
  - parseInt()
discription:
---

## baseball

- 야구 게임 구현
- 10번의 시도로 4자리의 숫자를 맞추는 게임
- 중복된 번호 체크 및 스트라이크와 볼의 개념에 대한 이해

### input type="number"

https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/number

- input type number의 min과 max에 대한 이해

### calc

https://developer.mozilla.org/en-US/docs/Web/CSS/calc()

- 스타일 사용시에 사용할 수 있는 계산식

### Object destructuring

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#object_destructuring

- Object를 다시 각각의 선언으로 사용할 수 있습니다.

### new Set()

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Set

- 새로운 배열을 중복없이 반환하는 Set()의 사용예제
- 하나의 Set 내 값은 한 번만 나타날 수 있습니다. 즉, 어떤 값은 그 Set 콜렉션 내에서 유일합니다.

### parseInt

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/parseInt

- parseInt의 기본값은 10이 아닙니다. 즉, 10진수로 변환되지 않습니다.
- 첫번째 인자를 특정 radix(진수) 값으로 표현한 정수가 반환됩니다.

<br>

**따라하기**

```js
(function () {
"use strict";
// 엄격모드(Strict mode)

const get = (target) => document.querySelector(target);

const init = () => {
get("form").addEventListener("submit", (event) => {
playGame(event);
});
setPassword();
};

const baseball = {
limit: 10,
digit: 4,
trial: 0,
end: false,
$question: get(".ball_question"),
$answer: get(".ball_answer"),
$input: get(".ball_input"),
};

const { limit, digit, $question, $answer, $input } = baseball;

let { trial, end } = baseball;

const setPassword = () => {
// 패스워드를 지정해 준다.
const gameLimit = Array(limit).fill(false);
let password = "";
while (password.length < digit) {
const random = parseInt(Math.random() \* 10, 10);

      if (gameLimit[random]) {
        continue;
      }
      password += random;
      gameLimit[random] = true;
    }
    baseball.password = password;

};

const onPlayed = (number, hint) => {
// 시도를 했을 때 number: 내가 입력한 숫자, hit: 현재 어떤 상황?
return `<em>${trial}차 시도</em>: ${number}, ${hint}`;
};

const isCorrect = (number, answer) => {
// 번호가 같은가 ?
return number === answer;
};

const isDuplicate = (number) => {
// 중복 번호가 있는가 ?
return [...new Set(number.split(""))].length !== digit;
};

const getStrikes = (number, answer) => {
// 스트라이크 카운트는 몇 개 ?
let strike = 0;
const nums = number.split("");
nums.map((digit, index) => {
if (digit === answer[index]) {
strike++;
}
});

    return strike;

};

const getBalls = (number, answer) => {
// 볼 카운트는 몇 개 ?
let ball = 0;
const nums = number.split("");
const gameLimit = Array(limit).fill(false);

    answer.split("").map((num) => {
      gameLimit[num] = true;
    });

    nums.map((num, index) => {
      if (answer[index] !== num && !!gameLimit[num]) {
        ball++;
      }
    });

    return ball;

};

const getResult = (number, answer) => {
// 시도에 따른 결과는 ?
if (isCorrect(number, answer)) {
end = true;
$answer.innerHTML = baseball.password;
return "홈런!!";
}

    const strikes = getStrikes(number, answer);
    const balls = getBalls(number, answer);

    return "STRIKE: " + strikes + " BALL: " + balls;

};

const playGame = (event) => {
// 게임 플레이
event.preventDefault();

    if (!!end) {
      return;
    }

    const inputNumber = $input.value;
    const { password } = baseball;

    if (inputNumber.length !== digit) {
      alert(`${digit}자리 숫자를 입력해주세요.`);
    } else if (isDuplicate(inputNumber)) {
      alert(`중복 숫자가 있습니다.`);
    } else {
      trial++;
      const result = onPlayed(inputNumber, getResult(inputNumber, password));
      $question.innerHTML += `<span>${result}</span>`;

      if (limit <= trial && !isCorrect(inputNumber, password)) {
        alert("쓰리아웃!");
        end = true;
        $answer.innerHTML = password;
      }
    }
    $input.value = "";
    $input.focus();

};

init();
})();
```
