# 패스트캠퍼스 김민태의 프론트엔드 아카데미 제 1강

## 221013

**해커 뉴스 클라이언트 앱**

[HNPWA API](https://github.com/tastejs/hacker-news-pwas/blob/master/docs/api.md)을 활용하여서 사이트를 만들었다.

console 창에 계속해서 오류 창이 떠서 검색해 본 결과, 해결 방법이 github에 적혀 있었다. [parcel github](https://github.com/parcel-bundler/parcel/issues/1401) 으로 들어가면 볼 수 있다. 어떤 버전에서 오류가 일어나는지, 어떤 문구가 뜨는지. 해결 방안은 간단했다. `script` 태그에서 `type="module"`만 지우면 됐다.

처음엔 `new XMLHttpRequest();`를 통해서 서버와 상호작용하는 URL을 가지고 올 수 있게 했다.
```javascript
ajax.open('GET', NEWS_URL, false)
ajax.send();
```
`new XMLHttpRequest();` 코드를 `ajax`라는 이름의 변수로 할당하여 URL을 부르는 코드를 작성하였다.

`ul`, `li`, `a` 태그 등을 만들어서 불러온 URL에서 제목들을 뽑아 리스트를 만들었다. 리스트를 만드는 과정에서 for문을 사용하였다.

```javascript
for(let i = 0; i < 10; i += 1) {
  const li = document.createElement('li');
  const a = document.createElement('a');

  a.href = `#${newsFeed[i].id}`;
  a.innerHTML = `${newsFeed[i].title} (${newsFeed[i].comments_count})`;

  li.appendChild(a);
  ul.appendChild(li);
}
```
그 후, 안에 내용을 볼 수 있게 또 다시 URL을 불렀는데, 각 클릭하는 `hash`마다 `id` 값을 다르게 줘야 했다.
```javascript
  const id = location.hash.slice(1);

  ajax.open('GET', CONTENT_URL.replace('@id', id), false);
  ajax.send();
```
강사님은 `slice()` 대신에 `substr()`이라는 메소드를 썼는데 그건 이제 사용되지 않는다고 하여서 `slice()`를 사용했다.

## 221014

어제 작성했던 for문 안에 HTML 구조들을 `.innerHTML` 속성을 사용해서 조금 더 직관적인 코드로 만들어 주었습니다.
```javascript
for(let i = 0; i < 10; i += 1) {
  const div = document.createElement('div');

  div.innerHTML = `
  <li>
    <a href="#${newsFeed[i].id}">
    ${newsFeed[i].title} (${newsFeed[i].comments_count})</a>
  </li>`;

  ul.appendChild(div.firstElementChild);
}
```
여기서 보여지는 `div` 변수는 임시적인 `DOM`이라고 설명을 주셨는데... 사실 이해가 잘 안 돼서... ㅠㅠ 왜냐면 결과값의 HTML을 보면 `div` 태그는 없기 때문이다. 지금은 간단하게 div의 첫 번째 자식 요소를 ul 바꿔 준다는 정도로 이해하면 될 것 같다. ~~이게아닐가능성이굉장히높을듯싶지만~~

++ 팀원들의 도움으로 이해한 것! `div`의 첫 번째 자식 요소를 `ul`태그 안으로 넣는 것으로 이해하면 됨!

```javascript
function getData(url) {
  ajax.open('GET', url, false);
  ajax.send();

  return JSON.parse(ajax.response);
};
```
JS를 공부하면서 꾸준히 보는 게 반복되는 코드를 간단한 변수나 함수로 설정해서 코드의 길이가 너무 길어지지 않게 하는 것이었다. 여기에서는 API 서버를 받아서, 다시 쓸 곳으로 보내는 게 반복이 되는데 변경되어야 하는 `url`을 인수로 받아서 사용하면 반복되는 곳에서 인수만 변경해서 사용하면 된다.

```javascript
const newsFeed = getData(NEWS_URL);
// newsfeed를 받을 때

const newsContent = getData(CONTENT_URL.replace('@id', id))
// newscontent를 받을 때
```

```javascript
const newsList = [];

newsList.push('<ul>');

for(let i = 0; i < 10; i += 1) {
  newsList.push(`
  <li>
    <a href="#${newsFeed[i].id}">
    ${newsFeed[i].title} (${newsFeed[i].comments_count})</a>
  </li>
  `);
}

newsList.push('</ul>');

container.innerHTML = newsList.join('');
```

`newsList`라는 빈 배열을 만들어 주고, 거기에 `ul` 여는 태그와 닫는 태그를 for문이 시작되기 전, 후로 넣어 줍니다. 그리고 마지막에 `container (id = "root"인 div 태그입니다)`에 `join` 속성에 공백을 사용하여 `push`한 내용들이 `,`로 배열이 되지 않게 만들어 줍니다.

**라우터**
라우터의 간단한 설명을 하자면 상위 통신망과 하위 통신망 사이를 중계해 주는 기계라고 한다. 여기서 라우터라고 하면 상위 사이트와 하위 사이트, 즉 메인 페이지와 콘텐츠(하위) 페이지를 이어 주는 용도 정도가 될 것 같다.

```javascript
function router() {
  const routePath = location.hash;

  if (routePath === '') {
    newsFeed();
  } else {
    newsDetail();
  } 
}

window.addEventListener('hashchange', router);

router();
```

`addEventListener` 부분에 router을 넣어서 hash가 빈값이면 목록을 그게 아니라면 콘텐츠를 보여 주게 만들어 주었다. `router`는 함수이기 때문에 마지막에 호출까지 해 주었다.