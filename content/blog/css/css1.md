---
title: ios와 android에 css가 다르게 적용되는 이슈
date: 2021-04-27 10:05:96
category: css
thumbnail: { thumbnailSrc }
draft: false
---

똑같은 css를 적용했지만 안드로이드와 아이폰의 결과물이 다를경우에는 
아래와같이 버튼의 기본 스타일을 제거해줍니다

```css
button, html [type="button"] {
  -webkit-appearance: none;   
}
```

이렇게 해주면 브라우저에서 기본적으로 제공하는 스타일이 제거되고 사용자가 정의한 css가 잘 적용됩니다!


## 참고글 
- [[IOS] 웹 폼버튼 기본 스타일 제거하는 방법](http://blog.freezner.com/archives/1309)