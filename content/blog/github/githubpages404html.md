---
title: github pages에서 라우팅된 페이지에서 새로고침시 404페이지를 반환합니다
date: 2021-04-15 08:05:00
category: github
thumbnail: { thumbnailSrc }
draft: false
---

## 문제
github pages에 배포 후 라우팅된 페이지에서 새로고침시 404 페이지가 보여지는 문제

## 원인
github pages는 SPA를 지원하지 않습니다. 그래서 URL이 `https://[도메인]`에서는 새로고침해도 페이지가 잘 보이지만 `https://[도메인]/main`에서 새로고침할 경우 깃허브 페이지 서버는 `/main`를 모르기 때문에 `404` 에러와 404.html 페이지를 리턴합니다.

## 해결방법
> GitHub Pages 를 사용하는 경우에는 편법이지만 404.html 을 많이 사용해요.
자체적으로 제공해주기 때문이에요.
CI 에서는 그래서 index.html 을 404.html 로 복사하는 스크립트를 추가해주면 될거에요 ;)

> GitHub Pages 에서는 라우팅을 지원하지 않기 때문에
CI 에서 빌드할 때 index.html -> 404.html 로 옮겨서 처리해줘야 해요.
그래서 404 페이지를 index 페이지로 잡아줘서 라우팅을 시켜주는방법이에요.

CI에서 빌드할 때 `index.html`을 `404.html`로 복사하는 스크립트를 추가해주면 된다!

```yml
# CI 설정파일
- name: Install and Build 🔧 
        run: |
          npm install
          npm run build
          cp dist/index.html dist/404.html
```

## 정리
github pages에서 라우팅을 지원하지 않아 루트 경로 이외의 경로는
`404.html`을 반환합니다. 이를 해결하기 위해 빌드 단계에서
 `index.html`과 내용이 같은 `404.html`을 만들어서 `404.html`이 
반환되었을때 `index.html`이 보이는 것 처럼 눈속임이 가능하다


## 참고글
- [Github Page로 React SPA 배포하기 (ft. React Router)](https://velog.io/@ausg/gh-pages-react-router)
- [[React] GitHub Pages에 React App(SPA) 호스팅 하기](https://iamsjy17.github.io/react/2018/11/04/githubpage-SPA.html)

## 참고 리뷰글
- https://github.com/CodeSoom/project-react-3-datalater/issues/12#issuecomment-814219023
- https://github.com/CodeSoom/project-react-3-yoonhe/issues/69
