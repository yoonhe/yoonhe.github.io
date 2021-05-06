---
title: asyncTesting
date: 2021-04-12 09:05:78
category: testing
thumbnail: { thumbnailSrc }
draft: false
---

제가 테스트하려는 코드는 아래와 같습니다
```js
async function handleFileChange(e) {
    const { files } = e.target;

    const getImagePromises = [].map.call(files, async (file) => {
      const result = await readFile(file);
      return result;
    });

    const uploadImages = await Promise.all(getImagePromises);

    dispatch(setAddRoomImagesField(uploadImages));
  }
```

`promise.All` 함수가 실행된 후 `dispatch`가 실행되는 것을 확인하려면
`waitFor`를 사용해야합니다. `waitFor`는 비동기적으로 확인해야하는 항목을 기다려줍니다

```js
const { getByLabelText } = renderAddRoomContainer();

await waitFor(() => {
  fireEvent.change(getByLabelText('방 사진 등록하기'), {
    target: {
      files: {
        0: {
          name: 'test.png',
        },
      },
    },
  });
});

expect(dispatch).toBeCalled();
```

## 참고글
- [Simulating uploading file](https://github.com/testing-library/react-testing-library/issues/93#issuecomment-405111391)
- [testing-library-waitFor](https://testing-library.com/docs/dom-testing-library/api-async/)

