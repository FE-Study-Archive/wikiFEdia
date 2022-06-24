# React 컴포넌트 라이프사이클 이벤트



> **모든 React 컴포넌트는 라이프사이클 이벤트가 있다.**
>
> 라이프사이클 이벤트는 컴포넌트가 수행한 작업이나 앞으로 수행할 작업에 따라 특정 시점에 실행된다.



![img](https://i.imgur.com/cNfpEph.png)

## 이벤트 분류

* 마운팅(생성)
  * 한 번만 실행된다.
* 업데이트
  * 여러번 실행된다.
* 언마우팅(제거)
  * 한 번만 실행된다.



## 이벤트의 발생 순서



* **constructor()**

  * 엘리먼트를 생성하여 기본 속성과 상태를 설정할 때 실행된다.

    

#### 마운팅

* **componentWillMount()**

  * DOM에 삽입하기 전에 실행된다.

* **componentDidMount()**

  * DOM에 삽입되어 렌더링이 완료된 후 실행된다.

    

#### 업데이트

* **componentWillReceiveProps(nextProps)**

  * 컴포넌트가 속성을 받기 직전에 실행된다.

* **shouldComponentUpdate(nextProps, nextState)**

  * 컴포넌트가 갱신되는 조건을 정의해서 재렌더링을 최적화할 수 있다.
  * 부울 값을 반환한다.

* **componentWillUpdate(nextProps, nextState)**

  * 컴포넌트가 갱신되기 직전에 실행된다.

* **componentDidUpdate(prevProps, prevState)**

  * 컴포넌트가 갱신된 후에 실행된다.

    

#### 언마운팅

* **componentWillUnmount()**
  * 컴포넌트를 DOM에 제거하기 전에 실행된다.
  * 이벤트를 제거하거나 다른 정리 작업을 수행 할 수 있다.



|                             | 마운팅 | 컴포넌트 속성 갱신 | 컴포넌트 상태 갱신 | forceUpdate()를 이용한 갱신 | 언마운팅 |
| --------------------------- | ------ | ------------------ | ------------------ | --------------------------- | -------- |
| constructor()               | v      |                    |                    |                             |          |
| componentWillMount()        | v      |                    |                    |                             |          |
| componentWillReceiveProps() |        | v                  |                    |                             |          |
| shouldComponentUpdate()     |        | v                  | v                  |                             |          |
| componentWillUpdate()       |        | v                  | v                  | v                           |          |
| render()                    | v      | v                  | v                  | v                           |          |
| componentDidUpdate()        |        | v                  | v                  | v                           |          |
| componentDidMount()         | v      |                    |                    |                             |          |
| componentWillUnmount()      |        |                    |                    |                             | v        |

-> 이벤트의 실행 순서와 속성 또는 상태의 변경에 영향을 받는 경우



#### forceUpdate() 메서드

* 업데이트를 강제로 일으키는 메서드
* 주로 상태, 속성을 갱신해서 재렌더링을 시킬 수 없는데 재렌더링이 필요한 경우 사용
* **React에서 권장하지 않는 렌더링 방식**



## 이벤트 구현

> 라이프사이클 이벤트를 구현하려면 클래스에 메서드를 정의해야 한다.
>
> 이벤트 이름에 해당하는 메서드가 있는지 확인 후, 있을 경우 해당 메서드를 실행한다.
>
> * ex) componentDidMount()를 정의하면, React 컴포넌트 클래스의 엘리먼트가 DOM에 추가되었을 때 componentDidMount()를 호출한다.
>
> **반드시 메서드 이름은 이벤트 이름과 일치해야한다.**



##### 예제

```react
class Content extends React.Component {
    componentWillMount() {
        console.log(ReactDOM.findDOMNode(this)) // DOM 노드에 대한 정보 수집
    }
    componentDidMount() {
        console.dir(ReactDOM.findDOMNode(this))
    }
    render() {
        return (
        
        )
    }
}
```

##### 실행 결과

```html
null
div
```



## 모든 이벤트 실행

* 모든 이벤트들이 실행되는 예제이다.

```react
class Logger extends React.Component {
  constructor(props) {
    super(props)
    console.log('constructor')
  }
  componentWillMount() {
    console.log('componentWillMount is triggered')
  }
  componentDidMount(e) {
    console.log('componentDidMount is triggered')
    console.log('DOM node: ', ReactDOM.findDOMNode(this))
  }
  componentWillReceiveProps(newProps) {
    console.log('componentWillReceiveProps is triggered')
    console.log('new props: ', newProps)
  }
  shouldComponentUpdate(newProps, newState) {
    console.log('shouldComponentUpdate is triggered')
    console.log('new props: ', newProps)
    console.log('new state: ', newState)
    return true
  }
  componentWillUpdate(newProps, newState) {
    console.log('componentWillUpdate is triggered')
    console.log('new props: ', newProps)
    console.log('new state: ', newState)
  }
  componentDidUpdate(oldProps, oldState) {
    console.log('componentDidUpdate is triggered')
    console.log('new props: ', oldProps)
    console.log('old props: ', oldState)
  }
  componentWillUnmount() {
    console.log('componentWillUnmount')
  }
  render() {
    // console.log('rendering... Display')
    return (
      <div>{this.props.time}</div>
    )
  }
}
```



## 마운팅 이벤트

> DOM에 컴포넌트를 추가하는 이벤트
>
> React엘리먼트가 DOM에 노출되는 것

#### componentWillMount()

* React 엘리먼트가 실제 DOM에 곧 추가될 것을 알려준다.

  

#### componentDidMount()

* React 엘리먼트를 실제 DOM에 추가한 시점. 이 시점의 React엘리먼트는 DOM 노드다.

* 브라우저에서만 한 번 실행되며, 서버 렌더링에서는 실행되지 않는다.
  * 브라우저에서만 실행해야 하는 코드를 구현할 때 사용
  * JSON 요청하기 좋은 위치



> JSON요청을 WillMount에서 안하고 DidMount에서 하는 이유?
>
> * WillMount의 경우, 서버에서도 실행되는 메서드 이며, 렌더링 되는것 보다 더 빨리 서버에서 응답이 오는 경우, 의도대로 렌더링 되지 않을 수 있다.



## 업데이트 이벤트



#### componentWillReceiveProps(newProps)

* 컴포넌트가 새로운 속성을 전달받을 때 실행된다.
* render()를 호출하기 전에 일부 로직 추가 가능
* 속성 값의 변경과 상관없이 **재렌더링이 이뤄질 때마다 실행**된다.



#### shouldComponentUpdate()

* 렌더링 직전에 실행된다.
* 속성이나 상태가 전달 된 후 렌더링이 이루어진다.
* 초기 렌더링시점이나 forceUpdate() 호출 시에는 실행되지 않는다.
* 부울 값을 반환한다.



#### componentWillUpdate()

* 새로운 속성이나 상태를 받은 후 렌더링 직전에 호출된다.
* 초기 렌더링 시에는 호출되지 않는다.

* **shouldComponentUpdate()가 false를 반환하면, 실행되지 않는다.**



#### componentDidUpdate()

* 컴포넌트 갱신 결과가 실제 DOM에 반영된 직후에 실핸된다.
* 초기 렌더링 시에는 호출되지 않는다.
* DOM이나 그 외의 요소를 다루는 코드를 작성할 때 유용



## 언마운팅 이벤트

> React에서 **언마운팅**은 DOM에서 요소를 분리하거나 제거하는 것을 의미한다.
>
> componentWillUnmount() 한가지로 유일하다.



#### componentWillUnmount()

* DOM에서 컴포넌트가 제거되기 직전에 호출된다.
* 정리하기위한 코드를 이 메서드에 추가한다.