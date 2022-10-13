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