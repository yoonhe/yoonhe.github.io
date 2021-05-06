---
title: < apollo > 구독후 데이터를 업데이트하는 방법
date: 2021-02-05 10:05:17
category: apollo
thumbnail: { thumbnailSrc }
draft: false
---

## 코드
```js
useEffect(() => {
  subscribeToMore({
    document: MESSAGES_SUBCRIPTION,
    updateQuery: (prev, { subscriptionData }) => {
      console.log('test ======> ', { prev, subscriptionData });
      if (!subscriptionData.data) return prev;
      const newMessageItem = subscriptionData.data.newMessage;
      return {
        getMessage: {
          ...prev.getMessage,
          messages: [newMessageItem, ...prev.getMessage.messages],
        },
      };
    },
  });
}, []);
```

## 설명
위의 코드에서 `subscribeToMore`에 2가지 옵션을 전달합니다.

- `document` :  실행할 구독을 나타냅니다.
- `updateQuery` Apollo Client에 쿼리의 `현재 캐시 된 결과( prev)`를 subscriptionDataGraphQL 서버에서 `푸시한 결과`와 `결합`하는 방법을 알려주는 함수입니다. 이 함수의 **반환 값은 쿼리에 대해 현재 캐시 된 결과를 완전히 대체합니다.**
- `variables` 구독을 실행할 때 포함 할 변수를 나타냅니다.
  - 이 옵션은 위의 코드에서 사용되진 않았지만 나중에 필요할거같아 적어두었습니다! 



`prev`와 `subscriptionData`에 무엇이 들어있나 확인해보았습니다.
<p style='margin-top:2rem; padding:2rem; border: 2px solid #dcdcdc;'>
  <img src="https://images.velog.io/images/yhe228/post/b4798e0e-6d98-4362-b40c-a452064eb5e3/image.png" />
</p>

이렇게 확인해보니 아래의 코드에서 
```
messages: [newMessageItem, ...prev.getMessage.messages]
```
이렇게 해준 이유를 아시겠죠?..! ☺️

```js
updateQuery: (prev, { subscriptionData }) => {
  console.log('test ======> ', { prev, subscriptionData });
  if (!subscriptionData.data) return prev;
  const newMessageItem = subscriptionData.data.newMessage;
  return {
    getMessage: {
      ...prev.getMessage,
      messages: [newMessageItem, ...prev.getMessage.messages],
    },
  };
```




### 참고글
https://www.apollographql.com/docs/react/data/subscriptions/#subscribing-to-updates-for-a-query