---
title: reducer 테스트 파일에서 jest coverage 100%가 안나오는 이슈
date: 2021-4-20 17:05:12
category: testing
thumbnail: { thumbnailSrc }
draft: false
---


jest coverage를 돌렸더니 한 파일에서 테스트를 통과를 못하고 있다..

![](https://images.velog.io/images/yhe228/post/9f833bb8-2e8b-4512-948d-5f020c946d7c/image.png)

`Uncovered Line`을 보니 `39`번 라인에서 테스트가 통과되지 않고있는것 같다.

39번째 줄은 `reducer`에서 `action.type`이 없을때 기본 `state`를 반환해주는 부분이다.

```js
export default function reducer(state = initialState, action) {
    // 생략
    if (action.type === 'updateTaskTitle') {
      return {
        ...state,
        taskTitle: action.payload.taskTitle,
      };
    }

    return state;
}
```

`reducer` 테스트코드에 `action.type`이 없을경우에 대한 테스트 코드가 없어서 테스트가 통과가 안되는거같아 테스트코드를 추가해주었다.

```js
describe('noType', () => {
  it('returns initial state', () => {
    const state = reducer();

    expect(state).toBe(initialState);
  });
});
```

테스트 코드를 작성한 후 `reducer`에 아래와같이 추가해주었다.

`reducer()` 이렇게 아무런 인자도 받지 않았을경우 `action.type`을 확인할때 `action`이 `undefined`여서 에러가 나기때문에 `action`의 `default parameter`를 `{type: ''}`으로 설정해주었다. 

```js
export default function reducer(state = initialState, action = { type: '' }) {
  // 생략	
  if (action.type === 'updateTaskTitle') {
    return {
      ...state,
      taskTitle: action.payload.taskTitle,
    };
  }
  
  return state;
}
```

모든 테스트가 통과되었다 🎉
![](https://images.velog.io/images/yhe228/post/51c9c279-b1b3-4cf5-b4d3-d88b035b084d/image.png)


## 참고글
- https://dev.to/stevescruz/awesome-jest-tip-coverage-report-h5j
- https://afrobambacar.github.io/2018/10/code-coverage-with-jest.html


