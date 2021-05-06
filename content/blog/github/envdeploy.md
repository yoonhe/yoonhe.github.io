---
title: github actions를 이용해 빌드 + 배포된 서버에서 `process.env.[환경변수]`를 사용하려면 어떻게 해야 할까요?
date: 2021-04-02 09:05:21
category: github
thumbnail: { thumbnailSrc }
draft: false
---

## 이슈
먼저 제가 환경변수를 사용해야했던 이야기를 풀어보겠습니다 😊

저는 프론트에 집중하기위해 백서버는 `firebase`를 사용하였는데요, `firebase`를 사용하기 위해서는  
아래와같이 `firebase` api key를 입력해줘야 합니다.
```js
const firebaseConfig = {
  apiKey: process.env.API_KEY,
  authDomain: process.env.AUTH_DOMAIN,
  projectId: process.env.PROJECT_ID,
  storageBucket: process.env.STORAGE_BUCKET,
  messagingSenderId: process.env.MESSAGING_SENDER_ID,
  appId: process.env.APP_ID,
  measurementId: process.env.MEASUREMENT_ID,
};
```

위의 값들은 노출되면 안되므로 `dotenv` 라이브러리를 이용해 환경변수에 접근할 수 있도록 해주었는데요,
`.env` 파일에 firebaseConfig에서 사용할 환경변수를 선언해두었습니다.
```
API_KEY = ???
AUTH_DOMAIN = ???
// 예시입니다
```

그리고 이 `.env` 파일은 깃헙 저장소에 올라가면 안되기때문에 `.gitignore`에 `.evn`를 추가하여 저장소에는 올라가지 않도록 해두었습니다.

그래서 현재 배포서버에서 `.env` 파일이 없는 상태입니다, 

firebase에 접근하기 위해 필요한 환경변수가 배포서버에도 존재해야 하기때문에 
`ci 설정`을 아래와 같이 변경해보았습니다 

```yml
- name: Install and Build 🔧 
    run: |
      npm install
      npm run build
    env:
      API_KEY : ${{ secrets.API_KEY }}
      AUTH_DOMAIN : ${{ secrets.AUTH_DOMAIN }}
      PROJECT_ID : ${{ secrets.PROJECT_ID }}
      STORAGE_BUCKET : ${{ secrets.STORAGE_BUCKET }}
      MESSAGING_SENDER_ID : ${{ secrets.MESSAGING_SENDER_ID }}
      APP_ID : ${{ secrets.APP_ID }}
      MEASUREMENT_ID : ${{ secrets.MEASUREMENT_ID }}
 ```
 
 build 단계에서 env를 설정해주면 `process.evn.[환경변수]`를 사용할 수 있을거라 예상했는데... `process.evn.[환경변수]`가 `undefined`여서 `firebase`에 접근할 수 없는 이슈가 발생하였습니다..
 
## 잘못 알고 있던 부분
저는 단순하게 아래와같이 ci 설정에서 env를 설정해주면 빌드된 파일에서 `precess.env.API_KEY`에 접근이 가능한 줄 알았습니다...
```yml
- name: Install and Build 🔧 
  run: |
    npm install
    npm run build
  env: # 추가한 부분
    API_KEY : ${{ secrets.API_KEY }}
```

그런데 env 설정만으로는 `prcess.env.[환경변수]`에 접근할 수가 없었습니다.

## 해결방법
한가지 더 해줘야할 작업이 있었는데요.

`webpack.config.js` 파일에서 `webpack.DefinePlugin`을 이용해 `process.env.[환경변수]`에 접근할 수 있도록 설정을 한번 더 해줘야합니다.
```js
//webpack.config.js
plugins: [
  new webpack.DefinePlugin({
    'process.env.API_KEY': JSON.stringify(process.env.API_KEY),
    'process.env.AUTH_DOMAIN': JSON.stringify(process.env.AUTH_DOMAIN),
    'process.env.PROJECT_ID': JSON.stringify(process.env.PROJECT_ID),
    'process.env.STORAGE_BUCKET': JSON.stringify(process.env.STORAGE_BUCKET),
    'process.env.MESSAGING_SENDER_ID': JSON.stringify(process.env.MESSAGING_SENDER_ID),
    'process.env.APP_ID': JSON.stringify(process.env.APP_ID),
    'process.env.MEASUREMENT_ID': JSON.stringify(process.env.MEASUREMENT_ID),
    'process.env.TEST': JSON.stringify(process.env.TEST),
  }),
],
```

### DefinePlugin
Webpack은 기본적으로 `process.env.NODE_ENV` 값을 제공하지 않기 때문에 사용자가 값을 등록해 사용할 수 있다고 합니다.
웹팩의 `DefinePlugin`를 이용해 컴파일 시점에서 `글로벌 변수로 사용할 수 있는 데이터를 설정`할 수 있습니다

#### 사용법
webpack.config.js에 plugins 객체의 배열안에 `DefinePlugin`를 설정해주세요. - [참고글](https://devhyun.com/blog/post/26)
```js
// webpack.config.js
const webpack = require('webpack');

module.exports = {
  ...
  plugins: [
    new webpack.DefinePlugin({
      'process.env.API_KEY': JSON.stringify(process.env.API_KEY),
      'process.env.AUTH_DOMAIN': JSON.stringify(process.env.AUTH_DOMAIN),
    }),
  ],
};
```

이렇게 해줄수도 있습니다. - [webpack 러닝 가이드 - 환경 변수 등록](https://yamoo9.gitbook.io/webpack/webpack/webpack-plugins/manage-env-variables)

```js
plugins: [
  // 환경 변수 등록/관리 설정
  new webpack.EnvironmentPlugin({
    NODE_ENV: 'development'
  })
]
```

`EnvironmentPlugin`은 내부적으로 `DefinePlugin`을 사용하기에 위에 작성한 코드는 아래와 동일합니다.
```js
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify('development')
});
```

## 궁금했던 점
### Q) 🤔 - [질문글](https://github.com/CodeSoom/project-react-3-yoonhe/issues/42#issue-849160481)
github actions에서 정의해준 env는 webpack.config.js에서만 사용이 가능한건가요? 그렇기때문에 웹팩에서 한번더 'process.env.API_KEY': JSON.stringify(process.env.API_KEY) 이런식으로 작업하여 빌드된 파일에서 process.env.[환경변수]를 사용할 수 있게 해주는 것인가요???

### A) 😇 
github actions 에서 정의 해준 env 는 CI 돌릴 때 .env 가 없기 때문에 스크립트 상으로 노드 환경변수를 주입해주는 것 과 같아요.
env를 webpack.config.js 에서만 사용 가능한게 아니라 작성하신대로 webpack은 기본적으로 process.env.NODE_ENV 값을 제공하지 않기 때문에 webpack 이 빌드할 때 노드 환경변수를 전역 변수로 변경하여 사용할 수 있도록 하는 플러그인이에요.

## 참고한 글
- [Webpack에서 Node 환경변수 접근하기](https://devhyun.com/blog/post/26)
- [webpack 러닝 가이드 - 환경 변수 등록](https://yamoo9.gitbook.io/webpack/webpack/webpack-plugins/manage-env-variables)
- [참고글 - 1](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#env)
- [참고글 - 2](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsenv)