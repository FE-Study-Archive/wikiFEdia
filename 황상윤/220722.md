# inert

> [공식문서](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/inert)
>
> [참고자료-toast ui 기술 블로그](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/inert)



HTML 요소에 전역으로 넣을 수 있는 속성이다.



### inert의 의미

사전적으로는 **움직이지 않거나 움직일 수 없는** 즉, **비활성 상태의**라는 의미를 가진다. HTML 요소에 `inert`를 속성으로 넣는다면 해당 요소를 비활성 상태로 만들겠다라는 뜻이다.



[크롬 102 버전 업데이트 내용 중 inert 속성의 상세 설명 페이지](https://chromestatus.com/feature/5703266176335872)에서는 inert 속성이 적용된 상태를 다음과 같이 설명한다.

- CSS 속성 `pointer-events: none;`으로 설정된 것처럼  동작한다.

- CSS 속성 `user-select: none;`으로 설정된 것처럼 동작한다

- 편집할 수 있는 노드를 편집할 수 없는 것(*non-editable*)처럼 동작한다
- 사용자 에이전트의 페이지 내 노드 탐색 무시한다



### inert 속성 사용하기

`inert`속성은 **HTMLElement 모든 요소의 속성으로 사용할 수 있다!** 



```html
<div inert>...</div>
<!-- inert: true -->
<div>...</div>
<!-- inert: false -->
```



- 자바스크립트에서도 동적으로 사용할 수 있다

```javascript
isInert = HTMLElement.inert;

HTMLElement.inert = true | false;
// 또는
HTMLElement.setAttribute('inert', ''); // 요소에 inert 속성 적용
HTMLElement.removeAttribute('inert'); // 요소에 inert 속성 제거
```

- 요소에 `inert` 속성을 정의하면 마우스, 입력 이벤트, 포커스 이벤트와 텍스트 선택을 무시한다.

- `inert` 속성은 `disabled`와 달리 **브라우저가 기본으로 가지고 있는 스타일을 따로 변경하지 않는다**. 시각적으로 구별되도록 스타일 속성을 별도로 줄 수 도 있다.

```css
[inert] > * {
  color: red;
}
```





### `inert` 사용 이점

- `inert` 속성을 사용하면 스크린 리더 사용자에게 더 나은 접근성을 제공할 수 있다.
  - 스크린 리더 사용자 시각이 불편해서 화면을 보지않고 스크린 리더로 음서으로만 웹페이지를 탐색 하는 사람들을 의미 한다.
- 이런 사용자들을 위해서는 용적이고 유용한 [포커스 순서](https://www.w3.org/WAI/WCAG22/Understanding/focus-order.html)를 요구한다.
  - 포커스 순서의 목적은 콘텐츠를 순차적으로 탐색할 때 콘텐츠의 의미와 일치하고 키보드에서 조작할 수 있는 순서로 정보를 접하게 하는 것이다. 웹 콘텐츠에서 순차 탐색 순서가 결정되는 방식은 콘텐츠의 기술에 의해 정의된다. 예를 들어, HTML은 탭 순서를 통해 순차 탐색을 정의하고 스크립트에서 `tabindex` 속성을 동적으로 추가해서 탐색 순서를 수정할 수 있다. 스크립트를 수정하거나 `tabindex` 속성을 사용하지 않는 경우 탐색 순서는 구성요소가 콘텐츠에 나타나는 순서가 된다.

- 물론 `aria-hidden="true"` 를 요소에 설정하여 탐새ㅔㄱ 기능을 막을 수는 있지만 상호작용은 제어하기 어렵다.



#### 개발자는 `inert`를 사용하여 요소를 탭 순서와 접근성 트리에서 제거할 수 있다. 이 기능을 통해 탐색 기능과 상호 작용을 모두 제어할 수 있으며, 사용자에게 더 나은 접근성을 제공할 수 있다.



대표적인 두가지 사용 사례로

1. 요소가 DOM 트리에 포함되어있지만, 화면 밖(*offscreen*)에 있거나 숨겨져(*hidden*) 있는 경우
2. 요소가 DOM 트리에 포함되어있지만, 상호 작용할 수 없도록(*non-interactive*) 설정해야하는 경우



### 1) 화면 밖에 있거나 숨겨져 표현되는 요소 다루기

웹 사이트에서 LNB 영역이 보였다가 닫히면 화면에 표시되지 않는 서랍형(*drawer*) UI를 접해본 적이 있다. `inert` 속성을 사용하면 LNB 영역이 화면 밖에 있을 때 사용자가 키보드로 상호작용할 수 없도록 한다.

![171818506-c0e07d78-9c40-41d3-96bd-3d0b73f6c2ba](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/171818506-c0e07d78-9c40-41d3-96bd-3d0b73f6c2ba.gif)



- 서랍형 UI가 닫힌 상태에서는 `inert`속성을 추가해 봤다.

![171819051-c0c1ea9b-2823-4ca0-81af-afa3deb5ebdb](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/171819051-c0c1ea9b-2823-4ca0-81af-afa3deb5ebdb.gif)



### 2) 상호 작용할 수 없는 DOM 요소 다루기

페이지 상에서는 표시되지만, 사용자와 상호 작용할 수 없는 콘텐츠를 다룰 때 활용할 수 있다. 예를 들면 모달 같은 대화상자가 열려있을 때는 배경에 있는 콘텐츠들을 선택할 수 없다.



- 모달, 포커스 트래핑 메뉴, 사이드 네비게이션 메뉴와 같은 요소를 차단하는 경우
- 캐러샐이나 슬라이드에서 비활성(*non-active*) 항목이 있는 경우
- 적용할 수 없는 폼 콘텐츠의 경우 (예: "청구서 수신 주소와 동일"확인 체크박스가 선택된 경우 "배송 주소" 필드가 페이드 아웃(*fade-out*)되거나 비활성화됨)
- 특정 영역의 UI를 전체 비활성화해야하는 경우



![171820464-16223f45-8f25-4260-89da-2cfa31896c2d](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/171820464-16223f45-8f25-4260-89da-2cfa31896c2d.gif)



- 모달 UI가 열린 상태에서는 메인 영역에 `inert`속성을 추가해 주었다.



![171820172-0b64afe0-3166-457b-bba6-b6126b7e7cbe](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/171820172-0b64afe0-3166-457b-bba6-b6126b7e7cbe.gif)



## `inert` vs `disabled` vs `readonly`

추가로, `disabled`와 `readonly`속성과의 차이점을 표로 정리해보았다.

|                  inert                   |    disabled    |                           readonly                           |                                                              |
| :--------------------------------------: | :------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
|                지원 요소                 | 전체 HTML 요소 | `<button>`, `<fieldset>`, `<keygen>`, `<optgroup>`, `<option>`, `<select>`, `<textarea>`, `<input>` | `text, search, url, tel, email, password, date, month, week, time, datetime-local, number` 타입의 `<input>`, `<textarea>` |
|              내용 편집 유무              |       X        |                              X                               | X                                                            |
|             스타일 변경 유무             |       X        |                O - 회색 음영 또는 연하게 표시                | X                                                            |
|             포커스 가능 유무             |       X        |                              X                               | O                                                            |
| `required`와 함께 사용할 때 폼 전송 유무 |       X        |                              O                               | O                                                            |
|             텍스트 선택 유무             |       X        |                              O                               | O                                                            |





# `accent-color`

> [공식문서](https://developer.mozilla.org/en-US/docs/Web/CSS/accent-color)

- input 태그에서  checkbox, radio, range 타입과 progress 태그의 색상을 변경 시켜 준다.



#### 예시

```html
<style>
  :root {
    accent-color: pink;
  }
</style>

<input type="checkbox" checked />
<input type="radio" checked />
<input type="range" />
<progress max="100" value="50">50%</progress>
<input type="text" />
```

![image-20220722020353289](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/image-20220722020353289.png)

```html
<style>
  :root {
    accent-color: blue;
  }
</style>
```

![image-20220722020338732](https://raw.githubusercontent.com/shrewslampe/image_sever/master/img/image-20220722020338732.png)