---
title: < 코드숨 > 리액트 6주차 회고
date: 2021-03-08 10:05:73
category: review
thumbnail: { thumbnailSrc }
draft: false
---

## 한것
- About 페이지 구현
- Not found 페이지 구현
- 레스토랑 상세페이지 구현
- 페이지 라우팅 구현(`react-router-dom` 라이브러리 사용)

## 느낀것
- PR 날리기 전에 셀프리뷰를 꼭 합시다!
- 급하게 작업하다보니 테스트코드가 없이 만들어진 코드들이 있었습니다..! 다행히 리뷰어님께서 브레이크를 걸어주셔서 다시 마크업부터 TDD를 적용하였습니다. ㅎㅎㅎ 테스트 코드 없이 코드 작성은 절대 금지..!
- 다른 분들의 코드 리뷰를 보니 질문이 엄청 많던데.. 나는 왜 질문이 적을까 라는 생각을 했습니다.. 그래서 리뷰어님께 고민을 털어놓으면서 수강생한테 바라는 점도 함께 질문하였는데 놀랍게도 리뷰어님의 답변에서 제가 질문이 적었던 이유를 찾게되었습니다. 바로...! 저는 주어진 과제만, 시키는 것만 했기때문에 질문도 적었던 것이였습니다.. ㅎ 반성합니다.... 
앞으로 강의나 과제에서 챙기지 않는 부분들을 스스로 고민해보고 테스트 케이스도 추가해보고, 어떻게 하면 더 나은 방향으로 개선할 수 있을까에 대해 항상 고민해보면서 적용시키도록 하겠습니다! 


## 배운것
- 페이지 컴포넌트의 관심사는 router
- react-router-dom 라이브러리 사용 방법 
  - 라우팅 되었을때 페이지가 알맞게 나오는 것을 확인하고 싶다면 MemoryRouter를 사용한다(`<MemoryRouter initialEntries={['/about']}`>
  - useParams를 사용하면 param을 얻어올 수 있다.
- 링크 기능을 테스트하는 방법
- 라우팅 테스트는 유저 사이드에서 이루어지는 부분이므로 E2E 테스트로 대체할 수 있다.