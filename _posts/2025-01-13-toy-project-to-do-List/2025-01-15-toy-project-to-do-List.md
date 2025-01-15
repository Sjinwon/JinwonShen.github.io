---
layout: post
title: "(toy-project) todo List 만들기"
date: 2025-01-13 01:23:00 +09:00
lastmode: 2025-01-15 14:51:00 +09:00
sitemap.changefreq: weekly
sitemap.priority: 0.5
categories: notice
usemathjax: true
tag:
  - toy project
  - todo List
  - strict mode
  - json-server
  - DOMContentLoaded
  - Javascript fetch API
discription:
---

## To do list

- 투두리스트 구현
- json-server를 활용해서 로컬에 REST API 구축 및 연동
- 기본적인 데이터 처리인 생성, 읽기, 수정, 삭제(CRUD)를 구현

### strict mode

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode

- 엄격하게 문법 검사를 실시하여 기존에는 무시되던 오류를 발생시킴

### json-server

https://github.com/typicode/json-server

- 짧은 시간에 REST API를 구축해주는 패키지
- 실제 프로덕션에서는 사용하지 않음
- npm을 통해 설치 가능
- 안정버전 설치 권장 !!

#### 라이브 서버와 충돌

라이브 서버와 충돌 그리고 json-server의 안정버전 미설치로 json-server의 자동증분을 이용 못하게 되는 경우가 있었는데, 기존 json-server 삭제, 안정버전 설치 후 라이브서버에 아래 코드를 추가해 해결할 수 있었습니다 !

**.vscode/settings.json**

라이브 서버가 현재 todo list를 증가시킬 때 충돌이 있는 것으로..

```json
{
  "liveServer.settings.ignoreFiles": ["**/*.json", "*.json"]
}
```

<br>

### DOMContentLoaded

https://developer.mozilla.org/ko/docs/Web/API/Window/DOMContentLoaded_event

- 초기 HTML 문서를 완전히 불러오고 분석했을 때 발생

```js
window.addEventListener("DOMContentLoaded", (event) => {
  console.log("DOM fully loaded and parsed");
});
```

### Javascript fetch API

https://developer.mozilla.org/ko/docs/Web/API/Fetch_API

- AJAX 요청을 하기 위한 기술
- AJAX란 서버에서 추가 정보를 비동기적으로 가져올 수 있게 해주는 포괄적인 기술을 나타내는 용어
- XHR, JQuery, Fetch 등의 선택지가 있지만 이번 강의에서는 최신 기술인 fetch API를 사용

#### fetch API 사용법

https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch

- fetch api의 response는 실제 json 이 아니다.
- 따라서 fetch api에서는 추가 메서드를 호출해 응답 본문을 받을 필요가 있다. (`.json()`)
  - axios는 이 과정을 자동으로 해주기 떄문에 바로 response를 받을 수 있다.
- body 데이터 타입은 헤더의 content-type 헤더와 일치해야 한다.

```js
const url = "https://example.com/profile";
const data = { username: "example" };

fetch(url, {
  method: "POST", // or 'PUT'
  body: JSON.stringify(data), // data can be `string` or {object}!
  headers: {
    "Content-Type": "application/json",
  },
})
  .then((res) => res.json())
  .then((response) => console.log("Success:", JSON.stringify(response)))
  .catch((error) => console.error("Error:", error));
```

### HTML data

https://developer.mozilla.org/ko/docs/Learn/HTML/Howto/Use_data_attributes

- custom attribute를 만들 때 사용
- 표준이 아닌 속성이나 추가적인 DOM 속성

#### javascript에서 접근하기

- dataset 객체를 통해 data 속성을 가져오기 위해서는 속성 이름의 data- 뒷 부분을 사용
- 대시들은 camelCase로 변환되는 것에 주의

```js
const article = document.getElementById("electriccars");

article.dataset.columns; // "3"
article.dataset.indexNumber; // "12314"
article.dataset.parent; // "cars"
```

#### javascript로 할당하기

```js
const article = document.getElementById("electriccars");

article.dataset.columns = 3;
article.dataset.indexNumber = "12314";
```

<br>

## To do list 페이지네이션 구현

- json-server를 활용해서 페이지네이션 구현

### 페이지네이션 이론

페이지네이션을 구현하기 위한 설정값

- currentPage: 현재 페이지
- totalCount: 총 데이터의 갯수
- pageCount: 화면에 나타날 페이지 갯수
- limit: 한 페이지 당 나타낼 데이터의 갯수

![](https://user-images.githubusercontent.com/16531837/145595161-ceb09871-bfe2-4a33-9e65-b53b0bd9d89f.png)

따라서...

- currentPage: 1
- totalCount: 53 (totalCount는 임의 설정)
- pageCount: 5
- limit: 5

#### 총 페이지 갯수 계산하기

```js
let totalPage = Math.ceil(totalCount / limit);
```

예를 들어보면,

```js
const totalCount = 53;
const limit = 5;

let totalPage = Math.ceil(totalCount / limit); // 11
```

#### 현재 페이지의 그룹 계산하기

![](https://user-images.githubusercontent.com/16531837/145596540-7c1ff5e6-60f8-40fc-884b-c10f4f4716a2.png)

현재 페이지가 몇번째 그룹에 속해있는지를 알아야
현제 페이지 그룹 상의 첫번째 숫자와 마지막 숫자를 구할 수 있다.

```js
const currentPage = 1;
const pageCount = 5;

let pageGroup = Math.ceil(currentPage / pageCount); // 1
```

```js
const currentPage = 7;
const pageCount = 5;

let pageGroup = Math.ceil(currentPage / pageCount); // 2
```

#### 현재 페이지 그룹의 첫번째/마지막 숫자 구하기

이걸 알아야 현재 페이지 그룹의 첫번째~마지막 숫자만큼 페이지를 화면에 표시해줄 수 있다.
그리고 페이지네이션의 이전, 다음을 구현할 수 있다
이전을 누르면 이전 그룹으로 넘겨주고 다음을 누르면 다음 그룹으로 넘겨준다.

```js
const pageGroup = 1;
const pageCount = 5;
const totalPage = 11;

let lastNumber = pageGroup * pageCount; // 5
if (lastNumber > totalPage) {
  lastNumber = totalPage;
}
let firstNumber = lastNumber - (pageCount - 1); // 1

const next = lastNumber + 1; // 6
const prev = firstNumber - 1; // 0

// 1~5만큼 페이지네이션 그려줌
for (let i = firstNumber; i <= lastNumber; i++) {
  html += `<button class="pageNumber" id="page_${i}">${i}</button>`;
}
```

```js
const pageGroup = 2;
const pageCount = 5;
const totalPage = 11;

let lastNumber = pageGroup * pageCount; // 10
if (lastNumber > totalPage) {
  lastNumber = totalPage;
}
let firstNumber = lastNumber - (pageCount - 1); // 6

const next = lastNumber + 1; // 11
const prev = firstNumber - 1; // 5
```

```js
const pageGroup = 3;
const pageCount = 5;
const totalPage = 11;

let lastNumber = pageGroup * pageCount; // 15
if (lastNumber > totalPage) {
  lastNumber = totalPage; // 11
}
let firstNumber = lastNumber - (pageCount - 1); // 7

const next = lastNumber + 1; // 12
const prev = firstNumber - 1; // 6
```

### json-server

https://github.com/typicode/json-server

<br>

**따라하기**

```js
(function () {
  "use strict";

  const get = (target) => {
    return document.querySelector(target);
  };

  const $todos = get(".todos");
  const $form = get(".todo_form");
  const $todoInput = get(".todo_input");
  const $pagination = get(".pagination");
  const API_URL = `http://localhost:3000/todos`;

  // 한 번에 보여질 리스트 갯수
  const limit = 5;
  // 첫 번째 페이지 default / 현재 페이지
  let currentPage = 1;

  // 최대 페이지 수
  const totalCount = 51;
  // 한 번에 보여질 페이지 수
  const pageCount = 5;

  const pagination = () => {
    let totalPage = Math.ceil(totalCount / limit);
    let pageGroup = Math.ceil(currentPage / pageCount);

    let lastNumber = pageGroup * pageCount;
    if (lastNumber > totalPage) {
      lastNumber = totalPage;
    }
    let firstNumber = lastNumber - (pageCount - 1);

    const next = lastNumber + 1;
    const prev = firstNumber - 1;

    let html = "";

    // 이전 버튼 노출
    if (prev > 0) {
      html += `<button class="prev" data-fn="prev">이전</button>`;
    }

    // 페이지 넘버 노출
    for (let i = firstNumber; i <= lastNumber; i++) {
      html += `<button class="page-number" id="page_${i}">${i}</button>`;
    }

    // 다음 버튼 노출
    if (lastNumber < totalPage) {
      html += `<button class="next" data-fn="next">다음</button>`;
    }

    $pagination.innerHTML = html;

    // 현재 페이지
    const $currentPageNumber = get(`.page-number#page_${currentPage}`);
    $currentPageNumber.style.color = "#9dc0e8";

    const $currentPageNumbers = document.querySelectorAll(".pagination button");
    $currentPageNumbers.forEach((button) => {
      button.addEventListener("click", () => {
        if (button.dataset.fn === "prev") {
          currentPage = prev;
        } else if (button.dataset.fn === "next") {
          currentPage = next;
        } else {
          currentPage = button.innerText;
        }
        pagination();
        getTodos();
      });
    });
  };

  const createTodoElement = (item) => {
    const { id, content, completed } = item;
    const isChecked = completed ? "checked" : "";
    const $todoItem = document.createElement("div");
    $todoItem.classList.add("item");
    $todoItem.dataset.id = id;
    $todoItem.innerHTML = `
            <div class="content">
              <input
                type="checkbox"
                class='todo_checkbox' 
                ${isChecked}
              />
              <label>${content}</label>
              <input type="text" value="${content}" />
            </div>
            <div class="item_buttons content_buttons">
              <button class="todo_edit_button">
                <i class="far fa-edit"></i>
              </button>
              <button class="todo_remove_button">
                <i class="far fa-trash-alt"></i>
              </button>
            </div>
            <div class="item_buttons edit_buttons">
              <button class="todo_edit_confirm_button">
                <i class="fas fa-check"></i>
              </button>
              <button class="todo_edit_cancel_button">
                <i class="fas fa-times"></i>
              </button>
            </div>
      `;
    return $todoItem;
  };

  const renderAllTodos = (todos) => {
    $todos.innerHTML = "";
    todos.forEach((item) => {
      const todoElement = createTodoElement(item);
      $todos.appendChild(todoElement);
    });
  };

  const getTodos = () => {
    fetch(`${API_URL}?_page=${currentPage}&_limit=${limit}`)
      .then((response) => response.json())
      .then((todos) => {
        renderAllTodos(todos);
      })
      .catch((error) => console.error(error.message));
  };

  const addTodo = (e) => {
    e.preventDefault();
    const content = $todoInput.value;
    if (!content) return;

    const todo = {
      content,
      completed: false,
    };
    fetch(API_URL, {
      method: "POST",
      headers: { "Content-type": "application/json" },
      body: JSON.stringify(todo),
    })
      .then((response) => response.json())
      .then(getTodos)
      .then(() => {
        $todoInput.value = "";
        $todoInput.focus();
      })
      .catch((error) => console.error(error.message));
  };

  const toggleTodo = (e) => {
    if (e.target.className !== "todo_checkbox") return;
    const $item = e.target.closest(".item");
    const id = $item.dataset.id;
    const completed = e.target.checked;
    fetch(`${API_URL}/${id}`, {
      method: "PATCH",
      headers: { "Content-type": "application/json" },
      body: JSON.stringify({ completed }),
    })
      .then((response) => response.json())
      .then(getTodos)
      .catch((error) => console.error(error.message));
  };

  const changeEditMode = (e) => {
    const $item = e.target.closest(".item");
    const $label = $item.querySelector("label");
    const $editInput = $item.querySelector('input[type="text"]');
    const $contentButtons = $item.querySelector(".content_buttons");
    const $editButtons = $item.querySelector(".edit_buttons");
    const value = $editInput.value;

    if (e.target.className === "todo_edit_button") {
      $label.style.display = "none";
      $editInput.style.display = "block";
      $contentButtons.style.display = "none";
      $editButtons.style.display = "block";
      $editInput.focus();
      $editInput.value = "";
      $editInput.value = value;
    }

    if (e.target.className === "todo_edit_cancel_button") {
      $label.style.display = "block";
      $editInput.style.display = "none";
      $contentButtons.style.display = "block";
      $editButtons.style.display = "none";
      $editInput.value = $label.innerText;
    }
    ``;
  };

  const editTodo = (e) => {
    if (e.target.className !== "todo_edit_confirm_button") return;
    const $item = e.target.closest(".item");
    const id = $item.dataset.id;
    const $editInput = $item.querySelector('input[type="text"]');
    const content = $editInput.value;

    fetch(`${API_URL}/${id}`, {
      method: "PATCH",
      headers: { "Content-type": "application/json" },
      body: JSON.stringify({ content }),
    })
      .then((response) => response.json())
      .then(getTodos)
      .catch((error) => console.error(error.message));
  };

  const removeTodo = (e) => {
    if (e.target.className !== "todo_remove_button") return;
    const $item = e.target.closest(".item");
    const id = $item.dataset.id;

    fetch(`${API_URL}/${id}`, {
      method: "DELETE",
    })
      .then((response) => response.json())
      .then(getTodos)
      .catch((error) => console.error(error.message));
  };

  const init = () => {
    window.addEventListener("DOMContentLoaded", () => {
      getTodos();
      pagination();
    });

    $form.addEventListener("submit", addTodo);
    $todos.addEventListener("click", toggleTodo);
    $todos.addEventListener("click", changeEditMode);
    $todos.addEventListener("click", editTodo);
    $todos.addEventListener("click", removeTodo);
  };

  init();
})();
```
