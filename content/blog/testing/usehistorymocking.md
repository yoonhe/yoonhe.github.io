---
title: react-router-dom useHistory 함수 mocking하기
date: 2021-04-13 08:05:23
category: testing
thumbnail: { thumbnailSrc }
draft: false
---

## jest.requireActual
공식문서에는 아래와 같이 설명이 되어있습니다
> 모의 구현을 수신해야하는지 여부에 대한 모든 검사를 무시하고 모의 대신 실제 모듈을 반환합니다.

## 실행
`mocking`한 `react-router-dom`에 `jest.requireActual`을 이용해 실제 모듈(`react-router-dom`)을 반환하고, `useHistory`만 `mocking` 합니다
```js
const mockPush = jest.fn();

jest.mock('react-router-dom', () => ({
  ...jest.requireActual('react-router-dom'),
  useHistory() {
    return { push: mockPush };
  },
}));
```

그럼 이제 `useHistory`를 호출했을때 `return` 값은`jest.fn()`이 됩니다. 이를 사용하여 `push` 함수와 함께 호출되는 인자를 테스트할 수 있습니다

```js
// 컴포넌트
function handleGoToAddRoom() {
  history.push('/addRoom');
}

// 테스트 코드
it('routing to "Add Room" page when click "방을 등록해볼까요?" button', () => {
  const { getByText } = render((
    <MainPage />
  ));

  fireEvent.click(getByText('방을 등록해볼까요?'));

  expect(mockPush).toBeCalledWith('/addRoom');
});
```

## 참고글
- [jest.mock](https://jestjs.io/docs/jest-object#jestmockmodulename-factory-options)
- [jest.requireActual](https://jestjs.io/docs/jest-object#jestrequireactualmodulename)
