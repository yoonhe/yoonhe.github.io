---
title: 파이어베이스 Storage 사용하기
date: 2021-04-12 09:05:70
category: firebase
thumbnail: { thumbnailSrc }
draft: false
---

## 첫번째로 파일을 가리키는 참조를 만듭니다
파일 업로드, 다운로드, 삭제, 메타데이터 가져오기 또는 업데이트를 수행하려면 작업할 파일을 가리키는 참조를 만들어야 합니다. 참조는 클라우드의 파일을 가리키는 `포인터`라고 생각할 수 있습니다. 참조는 메모리에 부담을 주지 않으므로 얼마든지 많이 만들 수 있으며 여러 작업에서 재사용할 수도 있습니다.

Firebase 앱의 `storage()` 서비스에서 참조를 만듭니다. 이 참조는 `Cloud Storage 버킷의 루트`를 가리킵니다.

```js
// firebase.js
import 'firebase/storage';

const firebaseConfig = {
  ... 생략
};

export const firebaseInstance = firebase.initializeApp(firebaseConfig);

export const storageService = firebase.storage();
```

기존 참조에 `child()` 메서드를 사용하여 `images/space.jpg`와 같이 트리에서 하위 위치를 가리키는 참조를 만들 수 있습니다.

```js
const storageRef = await storageService.ref().child('images/[파일명]');
```

## 두번째로 참조된 경로로 파일을 업로드 합니다
> put()과 putString()은 Promise로 사용하거나 업로드 상태를 관리하고 모니터링하는 데 사용할 수 있는 UploadTask를 반환합니다.

참조된 경로로 파일을 업로드한 후 return 값에서 `getDownloadURL`을 가져올 수 있습니다.

```js
const image = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAgAA…kAABAhQQAAKiQAAAAFfr/GYd5CITTh8YAAAAASUVORK5CYII=";

const response = await storageRef.putString(image, 'data_url');
const getUrl = await response.ref.getDownloadURL();
// getUrl ?  https://firebasestorage.googleapis.com/이하생략
```

만약 여러개의 파일을 업로드한 후 url을 배열로 받으려면 아래와같이 하면 된다.

```js
async function getFileUrl(image) {
  const storageRef = await storageService.ref().child(`images/${uuidv4()}`);
  const response = await storageRef.putString(image, 'data_url');
  const getUrl = await response.ref.getDownloadURL();

  return getUrl;
}

const asyncGetUrls = images.map((image) => getFileUrl(image));
const urls = await Promise.all(asyncGetUrls);
```

## 공식문서 예시
```js
// Data URL string
var message = 'data:text/plain;base64,5b6p5Y+344GX44G+44GX44Gf77yB44GK44KB44Gn44Go44GG77yB';
ref.putString(message, 'data_url').then(function(snapshot) {
  console.log('Uploaded a data_url string!');
});
```

## 참고글
- [웹에서 스토리지 참조 만들기](https://firebase.google.com/docs/storage/web/create-reference)
- [웹에서 파일 업로드](https://firebase.google.com/docs/storage/web/upload-files)
- [웹에서 파일 다운로드](https://firebase.google.com/docs/storage/web/download-files)