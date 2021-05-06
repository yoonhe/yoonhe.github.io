---
title: 파일 업로드시 이미지 미리보기 구현하기
date: 2021-04-12 09:05:25
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

업로드 파일 미리보기 기능 구현을 테스트하는 과정에 대해 기록한 글입니다.

## input file
아래와같이 file 타입의 인풋에 onChange 이벤트를 사용하고 `e.target.files`를 콘솔에 찍어보면 
```js
function handleFileChange(e) {
  const { files } = e.target;
  console.log('files ? ', files);
}

<input
  type="file"
  accept="image/*"
  onChange={handleFileChange}
  multiple
/>
```
## FileReader
아래와같이 선택한 파일 리스트를 확인할 수 있습니다.
![](https://images.velog.io/images/heyoon/post/68a1a987-f08f-4610-9956-5958fe064b6e/image.png)

이 파일들과 [FileReader](https://developer.mozilla.org/ko/docs/Web/API/FileReader)를 사용해 미리보기 이미지를 만들어보겠습니다.

> FileReader
FileReader읽기는 웹 애플리케이션이 파일로 데이터를 읽기 위하여 읽을 파일을 작성 File하거나 Blob(혹은 원시 데이터 버퍼로) 사용자의 컴퓨터에 저장하는 것을 가능하게합니다.

## FileReader 메서드
### `FileReader.readAsDataURL()`
지정된의 콘텐츠 읽기를 시작하고 완료되면 result 속성에 파일의 데이터를 나타내는 URL을 포함시킵니다

### `FileReader.readAsDataURL()`
이벤트는 읽기 동작이 끝났을 때마다 발생하는데 이때 `finishedEvent` 이벤트를 받아 `result` 속성안에 url을 가져와 사용할 수 있습니다.

### 사용
`readAsDataURL`의 인자로 file 타입의 인풋의 이벤트 객체에서 받은 files 중 하나를 넘기면 `FilesReader`가 파일을 읽고 파일을 읽는게 끝나면 `onloadend` 함수가 실행됩니다. 이때 `finishedEvent`의 `result` 속성에는 읽은 파일의 `URL`이 담기고 그 `URL`을 `img` 태그의 `src`에 넣어주면 미리보기 기능 구현이 완성됩니다

```jsx
const reader = new FileReader();

reader.onloadend = (finishedEvent) => {
  // 읽는게 끝나면(로딩이 끝나면) finishedEvent를 받음
  const { currentTarget: { result } } = finishedEvent;

  setImages((prev) => [...prev, result]);
};

reader.readAsDataURL(file); // 파일을 읽는다

return (
  <ul>
    {images.map((image, index) => (
      <li key={`uploadImage${index}`}>
        <img src={image} width="50px" height="50px" />
      </li>
    ))}
  </ul>
)
```

![](https://images.velog.io/images/heyoon/post/f5ba2196-3daf-41fc-9c40-ad9ffd962b1f/image.png)

![](https://images.velog.io/images/heyoon/post/9cbb909c-84fa-4656-9353-d84bc6c2f6d2/image.png)





## 전체 코드
```js
// utils.js
export function readFile({ file, setFiles }) {
  const reader = new FileReader();

  reader.onloadend = (finishedEvent) => {
    // 읽는게 끝나면(로딩이 끝나면) finishedEvent를 받음
    const { currentTarget: { result } } = finishedEvent;
    setFiles((prev) => [...prev, result]);
  };

  reader.readAsDataURL(file); // 파일을 읽는다
}
```
```jsx
// 컴포넌트
const [images, setImages] = useState([]);

function handleFileChange(e) {
  const { files } = e.target;

  // files에 유사 배열이 담기므로 
  // 배열의 forEach를 빌려와 사용한다
  [].forEach.call(files, (file) => {
    readFile({ file, setFiles: setImages });
  });
}

return (
  <div>
    <p>
      <label htmlFor="input-upload">이미지 등록하기</label>
      <input
        type="file"
        accept="image/*"
        onChange={handleFileChange}
        multiple
      />
    </p>
    <ul>
      {images.map((image, index) => (
        <li key={`uploadImage${index}`}>
          <img src={image} width="50px" height="50px" />
        </li>
      ))}
    </ul>
  </div>
)
```