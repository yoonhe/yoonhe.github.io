---
title: < apollo > writeQuery 사용하여 mutation후 캐시 업데이트하기
date: 2021-02-05 10:05:17
category: apollo
thumbnail: { thumbnailSrc }
draft: false
---

## 사용중인 쿼리
### GET_MESSAGES 쿼리
```js
export const GET_MESSAGES = gql`
  query getMessages($roomId: Float!) {
    getMessage(input: { roomId: $roomId }) {
      error
      success
      messages {
        createdAt
        receiver {
          id
          firstname
          lastname
          images {
            src
          }
        }
        sender {
          id
          firstname
          lastname
          images {
            src
          }
        }
        text
      }
    }
  }
`;
```

### SEND_MESSAGE 뮤테이션
```js
export const SEND_MESSAGES = gql`
  mutation sendMessage($message: String!, $roomID: Float, $receiverId: Float!) {
    sendMessage(input: { text: $message, roomId: $roomID, receiverId: $receiverId }) {
      success
      error
      sendMessage {
        createdAt
        receiver {
          id
          firstname
          lastname
          images {
            src
          }
        }
        sender {
          id
          firstname
          lastname
          images {
            src
          }
        }
        text
      }
    }
  }
`;
```

`sendMessage`를 이용해 `mutation`을 한 후 다시 쿼리를 실행해 서버에 데이터를 요청하고 요청받은 데이터를 클라이언트에 받아 렌더링해주면 사용자는 `왜이렇게 느리지?`하고 불편함을 호소할 수 있다. 그러므로 아래와같이 저장된 캐시를 조작해주자,


매개변수를 받는 함수들은 꼭 잊지말고 `variables`에 넣어주자.. 안넣어서 데이터를 못받아 몇시간동안 삽질을 했던 기억이.... 에러도 안난다. 왜 대체 데이터가 업데이트가 안되는지 한참 의문을 품다. 매개변수를 깜박한것을 인지하고 얼른 넣어주었더니 캐시 업데이트가 잘되었다... 그러므로 매개변수가 있다면 잊지말고 꼭 넣어주자!! 필!수!

```js
const { subscribeToMore, data: messageDatas, loading: messageDatasLoading } = useQuery(
  GET_MESSAGES,
  {
    variables: { roomId: localRoomId }, // GET_MESSAGES 쿼리는 매개변수로 roomID를 받는다 꼭 설정해주자
  },
);

const [sendMessage] = useMutation(SEND_MESSAGES, {
  update(cache, { data }) {
    // 1. 보낸 메시지를 가져온다
    const newMessage = data?.sendMessage?.sendMessage;

    const existingMessages = cache.readQuery({
      // 2. 현재 캐시에 저장되어있는 데이터를 가져온다.
      query: GET_MESSAGES,
      variables: {
        roomId: localRoomId, // GET_MESSAGES 쿼리는 매개변수로 roomID를 받는다 꼭 설정해주자! 
      },
    });

    if (newMessage && existingMessages) {
      cache.writeQuery({ // 캐시 업데이트!
        query: GET_MESSAGES,
        variables: {
          roomId: localRoomId, // GET_MESSAGES 쿼리는 매개변수로 roomID를 받는다 꼭 설정해주자! 꼭!!!
        },
        data: {
          getMessage: {
            ...existingMessages.getMessage,
            messages: [newMessage, ...existingMessages.getMessage.messages],
            // 3. 현재 캐시에 저장되어있는 messages에 새로운 message를 추가해준다.
          },
        },
      });
    }
  },
});
```


## 참고글
- [Apollo Client(react) 알아보기](https://medium.com/humanscape-tech/apollo-client-react-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-cb019131f769)
- [When To Use Refetch Queries in Apollo Client](https://www.apollographql.com/blog/when-to-use-refetch-queries-in-apollo-client/)
- [writeQuery](https://www.apollographql.com/docs/react/caching/cache-interaction/)
- [Apollo client의 fetchPolicy 옵션](https://velog.io/@ashnamuh/Graphql-Apollo-client%EC%9D%98-fetchPolicy-%EC%98%B5%EC%85%98)
- [Apollo client cache](https://velog.io/@zansol/-Apollo-client-cache-wgk3pev5on)