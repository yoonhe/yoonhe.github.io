---
title: < 코드숨 > 리액트 8주차 회고
date: 2021-03-24 10:05:73
category: review
thumbnail: { thumbnailSrc }
draft: false
---

## 한 일
- Redux toolkit을 사용하여 중복 코드 제거하기
- Emotion을 이용하여 꾸미기
- UseCallback을 이용하여 성능 최적화하기
- React.memo를 이용해서 성능 최적화하기

## 느낀것
- css 작성시 그룹핑을 해주니 훨씬 스타일 파악이 쉽다
- 그동안 lookup table 형식으로 reducer를 만들고, action 생성자 함수를 만들고 했던것이 모두 redudx-toolkit의 소중함을 알게하기위한 트레이너분들의 큰그림이였다는것을 알게되었다.. redux-toolkit 최고..!
- 정신없게 하루가 지나간다 일주일이 너무 빨리간다.. ㅎㅎㅎㅎ


## 배운것
- emotions 사용법
  - `Global` 컴포넌트를 사용해 initial css를 사용하는 방법
  - emotions 컴포넌트 안에서 animation을 위한 keyframes 사용법
- 스타일드 컴포넌트를 범용적으로 사용할 경우 하나의 파일로 빼주고 나머지는 각각의 리액트 컴포넌트 파일 상단에서 스타일드 컴포넌트를 관리한다
  - Input을 관리하는 컴포넌트인 경우에 스타일드 컴포넌트의 리렌더링때문에 글자를 입력할때마다 포커스가 잃게된다.
  - 사람은 위에서 아래로 코드를 읽기 때문에 선언부가 구현부 보다 위에 있는게 자연스럽다
- `왜` 이 작업을 했는지 커밋 메시지에 충분히 드러나야한다, 변화된 내용은 `file changes`를 통해 파악할 수 있으므로 메시지에는 적지 않는다.
  - 예시 ) css를 수정하였을 경우, 마진과 패딩을 잡아준 것은 중요하지 않다. 이런것들은 file changes를 통해 확인하게는게 더 빠르다. 중요한건 마진과 패딩을 왜 잡아줬느냐이다. 마진과 패딩을 잡은 이유에는 UI 개선(겹쳐서, 가독성이 안좋아서 등등)이 있다.
- CSS를 속성을 그룹핑해주면 스타일을 파악하기가 쉬워진다 - [참고](https://shylog.com/how-do-you-order-your-CSS-properties/ )
- redix-toolkit에서 제공하는 `configureStore`과 함께 `redux-mock-store`를 사용하는 방법(getDefaultMiddleware 사용)
