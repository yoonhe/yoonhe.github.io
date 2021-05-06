---
title: < 미해결 > NextJs 사용중 SSR에서 styled-component가 깨지는 문제
date: 2021-04-27 10:05:57
category: react
thumbnail: { thumbnailSrc }
draft: false
---

`nextjs`를 사용하여 서비스를 구축하였고 `styled-component`를 사용하여 스타일을 입혀주고있습니다.
그런데 프로필 페이지에서 `SSR`인 경우에 `css`를 가져오지 못해 `css`가 적용되지 않은 기본 `html`을 보여줍니다.. 그래서 유저 경험이 매우 좋지 않습니다..

```tsx
function Profile() { 

  return (
    <>
      <ProfileContainer />
    </>
  );
}
```
문제를 해결하기위해 구글링을 하던중..

`SSR`인 경우에 `nextjs`에서 스타일 정보(`styled-components`)를 포함하지 않은채 렌더링되기 때문이라는 글을 발견하였고,
공식문서를 찾아보니 아래와 같은 글을 발견하였습니다.
> Basically you need to add a custom pages/_document.js (if you don't have one). **Then [copy the logic](https://github.com/vercel/next.js/blob/master/examples/with-styled-components/pages/_document.js) for styled-components to inject the server side rendered styles into the <head>.**
Refer to [our example](https://github.com/vercel/next.js/tree/master/examples/with-styled-components) in the Next.js repo for an up-to-date usage example.

nextjs 저장소에 있는 [documnet.js](https://github.com/vercel/next.js/blob/master/examples/with-styled-components/pages/_document.js)를 따라서 해보았지만
```js
static async getInitalProps(ctx) {
    const sheet = new ServerStyleSheet();
    const originalRenderPage = ctx.renderPage;
    try {
      ctx.renderPage = () =>
        originalRenderPage({
          enhanceApp: App => props => sheet.collectStyles(<App {...props} />),
        });
      const initialProps = await Document.getInitialProps(ctx);
      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        ),
      };
    } catch (error) {
      console.error(error);
    } finally {
      sheet.seal();
    }
  }
```
여전히.. `SSR`에서 `styled-component` 로 만든 컴포넌트에 `css`가 적용되지 않습니다...
  
그래서 `dynamic import`를 사용하여 문제를 해결해보았습니다.

```tsx
const ProfileContainer = dynamic(() => import('../features/user/profile/ProfileContainer'), {
  loading: () => <ModalLoadingComponent />,
  ssr: false,
});
  
function Profile() { 

  return (
    <>
      <ProfileContainer />
    </>
  );
}
```
  
`SSR`일때는 로딩컴포넌트를 보여주고 `SSR`이 끝나면 프로필 페이지에 보여야하는 컴포넌트를 보여주는 방식으로 변경하니.. 일단 크게크게 깨지는 것을 사용자에게 보여주는 문제는 해결하였지만... 로딩바 역시 스타일드 컴포넌트이기때문에 `SSR`일때 잠깐 깨집니다.. 여러 컴포넌트가 깨지는 것보다 하나의 로딩 컴포넌트만 꺠지는 것이 그래도 더 나을것이라 생각하여 이렇게 문제를 해결했(?)지만...
  
![](https://images.velog.io/images/yhe228/post/8581f23c-e851-4e49-b8ff-cc249f240153/image.png)
아주 정말 잠깐.. 0.03초?.. 정도...? 보였다가 아래처럼 예쁘게 나옵니다...
  
저 로딩 아이콘은 `antd`라는 라이브러리에서 제공해주는 것인데.. 
`antd`에서 가져온 컴포넌트들은 `SSR`에서 잘 나오더라구요... 😅
![](https://images.velog.io/images/yhe228/post/05d5ac2d-acad-44cc-b118-428b5c2f2705/image.png)
  
이렇게 해결하는게 맞는건가 싶네요... ㅎㅎ...
SSR인 경우에 nextjs에서 스타일 정보(styled-components) 포함한채로 렌더링이 되려면 어떻게 해야하는지 계속해서 찾아봐야겠습니다...

혹시 방법을 아시는분은 댓글을 꼬옥.... 남겨주시길 바랍니다 🥲
도와주세요 🥲

## 참고글
- [Next.js - next/dynamic 이용해서, 특정 컴포넌트 SSR 안하고, 클라이언트에서만 렌더링하기.](https://kokohapps.tistory.com/entry/Nextjs-nextdynamic-%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C-%ED%8A%B9%EC%A0%95-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-SSR-%EC%95%88%ED%95%98%EA%B3%A0-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%EB%A7%8C-%EB%A0%8C%EB%8D%94%EB%A7%81%ED%95%98%EA%B8%B0)
- [Dynamic Import](https://nextjs.org/docs/advanced-features/dynamic-import)