---
title: 역방향 무한스크롤 구현
date: 2021-04-19 10:05:05
category: react
thumbnail: { thumbnailSrc }
draft: false
---

## 의사코드(pseudo-code)
1. scrollTop이 0이 되면
2. 현재 scrollHeight를 저장한다
3. 새로운 데이터를 불러온다
4. 새로운 데이터를 불러온 후의 scrollHeight에서 2번에서 저장한 과거 scrollHeight를 뺀 값을 scrollTop에 넣어 원래 보고있던 스크롤위치를 유지시킨다


## 코드에 적용
무한스크롤을 위해 `Intersection Observer API`를 사용하였습니다.
스크롤이 최상단으로 와서 `setTarget`이 화면에 노출되면, 데이터를 불러오는 함수를 실행시킵니다
```jsx
<StyledChatting ref={scrollBarRef} onScroll={handleScroll} id="scroll-test">
  {/* // 페이지 끝 감지 */}
  <div ref={setTarget} />
  {messages.reverse().map(message => (
    <dl key={message.date}>
      <dt>
        <span>{message.date}</span>
      </dt>
      {message.messages.reverse().map(chat => (
        <ChattingItem key={chat.createdAt} chat={chat} onClickProfile={onClickProfile} />
      ))}
    </dl>
  ))}
</StyledChatting>
```

데이터 요청 함수가 실행되면,
현재 `scrollHeight`를 `prevScrollHeight`에 저장하고
새로운 데이터를 불러옵니다(`onFetchMessageMore(lastMessageId)`)
```js
const handleFetchMessageMore = useCallback(() => {
/**
 * 1. 스크롤이 최상단에 닿여서 데이터를 불러오기전에 현재 scrollHeight를 저장한다
 * 2. 새로운 데이터를 불러왔을때 scrollHeight에서 저장해둔
     scrollHeight를 뺀값이 현재 스크롤 위치
 */
  setPrevScrollHeight(scrollBarRef.current?.scrollHeight);
  onFetchMessageMore(lastMessageId);
}, [lastMessageId]);
```
그리고 `useEffect`를 이용해 `messages`의 변화를 관찰합니다. 변화가 있다는 것은 새로운 데이터를 불러왔다는 의미이므로 그때 스크롤의 위치를 `현재 scrollHeight - 과거 scrollHeight(prevScrollHeight)`로 이동시키면 새로운 데이터를 불러오기 전 마지막 채팅이 있던 곳에 스크롤 위치를 유지하게됩니다

스크롤 위치 이동 후 과거 스크롤 위치는 `null`로 바꿔줍니다
`setPrevScrollHeight(null);`

### why? 🤔
`prevScrollHeight`이 있을경우(무한스크롤을 통해 새로운 데이터를 가져오는 경우)에는 스크롤 위치를 `현재 scrollHeight - 과거 scrollHeight(prevScrollHeight)`로 유지하고 없을경우(새로운 채팅을 보내거나 첫 렌더링시)에는 에는 스크롤을 맨아래로 이동시켜주기 위해서입니다

```js
useEffect(() => {
  if (prevScrollHeight) {
    onScrollTo(scrollBarRef.current?.scrollHeight - prevScrollHeight);
    return setPrevScrollHeight(null);
  }

  onScrollTo(scrollBarRef.current?.scrollHeight - scrollBarRef.current?.clientHeight);
}, [messages]);
```

### 참고글 
- [[JS] JQuery 를 이용한 반대반향 무한스크롤 (Reversed Infinite Scroll) 예제](https://velopert.com/2163)