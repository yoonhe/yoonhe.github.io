---
title: useSelector 함수를 테스트 코드에서 사용하는 방법
date: 2021-02-16 10:05:67
category: testing
thumbnail: { thumbnailSrc }
draft: false
---

먼저 가짜 함수들을 `react-redux.js` 파일에 export 해준다

```js
// __mocks__/react-redux.js

export const useSelector = jest.fn();
export const useDispatch = jest.fn();
```

테스트 케이스에서 아래와같이 `jest.mock('react-redux')`를 사용하면 위에서 만든 `react-redux`파일을 찾아 가져온다.

```js
import { useSelector } from 'react-redux';

jest.mock('react-redux');
```

다음으로 `useSelector` `mock` 함수를 조작해준다.

```js
  const tasks = [
    { id: 1, title: 'Task-1' },
    { id: 2, title: 'Task-2' },
  ];

  useSelector.mockImplementation((selector) => selector({
    tasks,
  }));
```

이렇게하면 `useSelector` 함수가 조작되고 테스트데이터를 사용해 테스트가 가능해진다.

테스트하려는 `App` 컴포넌트는 아래와같이 생겼는데, 테스트케이스가 실행되면 `useSelector`는 위에서 만들어준 `react-redux` 파일에서 가져오기때문에 `useSelector` 함수의 `state`에는 테스트케이스에서 넣어준 `tasks`가 담기게된다.

```js
// App.js
import { useDispatch, useSelector } from 'react-redux';

const { taskTitle, tasks } = useSelector((state) => ({
  taskTitle: state.taskTitle,
  tasks: state.tasks,
}));
```

```js
// 아래의 테스트는 통과된다.

expect(getByText('Task-1')).not.toBeNull();
```


## 전체 코드
```js
// App.test.jsx
import React from 'react';

import { render } from '@testing-library/react';

import { useSelector } from 'react-redux';

import App from './App';

jest.mock('react-redux');

test('App', () => {
  // TODO: useSelector 조작
  const tasks = [
    { id: 1, title: 'Task-1' },
    { id: 2, title: 'Task-2' },
  ];

  useSelector.mockImplementation((selector) => selector({
    tasks,
  }));

  const { getByText } = render((
    <App />
  ));

  expect(getByText(/추가/)).not.toBeNull();

  expect(getByText('Task-1')).not.toBeNull();

  // TODO: 통합 테스트 코드 작성
  // CodeceptJS => 실제 브라우저에서 사용자 테스트 실행 가능.
});
```