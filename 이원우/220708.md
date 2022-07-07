## Redux

- redux는 앱 전역에서 관리하는 상태 관리 툴이다.
- `redux`와 `useContext`는 전역에서 관리한다는 점에서 닮았다. 하지만, `useContext`는 단점이 존재한다.
  - useContext로 사용될 컴포넌트가 대규모 앱에서는 너무 많이 중첩될 수 있다.
    - 심하게 중첩된 JSX
    - 너무 다양한 `ContextProvider`
    - 유지하기 힘든 거대한 `ContextProvider`
  - 너무 자주 상태가 변경되는 작업에는 성능 저하가 심함.
  

![캡처](https://user-images.githubusercontent.com/90893428/177824028-3f6e7ce7-2555-449c-945d-325a0e5d431e.PNG)



## 1) Redux의 작동 방식

- 컴포넌트는 하나의 중앙 데이터 저장소를 `구독`한다.

- 중앙 데이터 저장소는 데이터가 변하면 컴포넌트에 알리고 필요한 데이터를 받는다.

- 컴포넌트는 데이터를 **절대 조작하지 않는다.**

- Redux의 중앙 데이터 저장소는 `State` 즉, 상태로써 존재한다.

-  **비동기**가 아닌 **동기적**으로 작동하므로 비동기 코드가 `reducer`에 들어가면 안된다.

  

### 1. Reducer

- Reducer는 **데이터의 변형(Mutation)**을 담당한다.
- useReducer와는 다른 개념이다.
- ex) 숫자 리스트를 받아 숫자의 합으로 변화시킴.



### 2. Action

- 컴포넌트가 `발송(Dispatch)`하는 동작이다.
  - 버튼을 클릭하면 `Dispatch`된 `Action`이 `Reducer`로 알린다.
  - `Action`을 받은 `Reducer`는 받은 작업을 수행해 `상태(State)`를 변화시켜 그대로 중앙 데이터 저장소에 저장해 관리한다.



### 3. initialState

- 중앙에서 관리하고자하는 초기 상태 및 값이 정의된 객체이다.



### 기본 세팅

1. `npm install redux react-redux`
2. `npm install @reduxjs/toolkit`
3. `index.js`즉, 최상위에 `Provider`로 감싸고 한 파일에서 관리하는 상태들을 담은`store`을 통해 전역에서 저장소를 통해 상태 관리를 할 수 있도록 해준다.

```react
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";

import "./index.css";
import App from "./App";
import store from "./store/index";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);

```



#### store/index.js

- 아래는 리덕스의 기본 기능만을 이용해 구현해 숫자를 세는 counter을 구현한 코드다.
- 아래의 기본 redux는 매우 사용하기 힘들지만 기본적으로 어떻게 `redux`가 동작하는지 알 수 있다.

```react
import { createStore } from 'redux';

//초기 상태
const initialState = { counter: 0, showCounter: true }; //상태를 하나 추가하는 순간 같은 상태의 객체를 반환해야하는 모든 reducer에 새로운 값을 명시해야하므로 비효율적이다.

//함수로 단 하나의
const counterReducer = (state = initialState, action) => {
  if (action.type === 'increment') {
    return {
      counter: state.counter + 1,
      showCounter: state.showCounter
    };
  }

  if (action.type === 'increase') {
    return {
      counter: state.counter + action.amount,
      showCounter: state.showCounter
    };
  }

  if (action.type === 'decrement') {
    return {
      counter: state.counter - 1,
      showCounter: state.showCounter
    };
  }

  if (action.type === 'toggle') {
    return {
      showCounter: !state.showCounter,
      counter: state.counter
    };
  }

  return state;
};

const store = createStore(counterReducer);
export default store;


// react-redux를 이용하지 않는다면 컴포넌트에서도 아래와 같이 구독을 통해 상태를 불러옴
const countSubscriber = () => {
  const latestState = store.getState()
}

store.subscribe(countSubscriber)
```



### Counter.js 컴포넌트

```react
import { useSelector, useDispatch } from 'react-redux';
import store from './index.js'
import classes from './Counter.module.css';

const Counter = () => {
  const dispatch = useDispatch();
    
  //const counter = store.getState()
  //createStore로 생성된 새로운 상태를 가져오는 메소드
  const counter = useSelector((state) => state.counter);
  const show = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    dispatch({ type: 'increment' });
  };

  const increaseHandler = () => {
    dispatch({ type: 'increase', amount: 10 });
  };

  const decrementHandler = () => {
    dispatch({ type: 'decrement' });
  };

  const toggleCounterHandler = () => {
    dispatch({ type: 'toggle' });
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      {show && <div className={classes.value}>{counter}</div>}
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={increaseHandler}>Increase by 10</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};
```



### 유의할 점!

- redux에서 `reducer`내부에서는 상태를 직접적으로 건들이면 **절대** 안된다. 작동은 똑같이 하는 것으로 느껴질 수 있지만, 객체의 참조값인 기존의`counter`을 변형시킨다. 이는 원본을 훼손하는 행위이고, `state`자체를 중간에 바꿔버리므로, 예기치 않은 결과를 낳아 디버깅에 혼선을 줄 수 있다.

  - 바뀐 상태를 `initialState`의 객체의 틀 그대로 반환하는 옳은 코드

    ```react
    if (action.type === 'increment') {
        return {
          counter: state.counter + 1,
          showCounter: state.showCounter
        };
     }
    ```

  - 직접 상태를 바꾸는 나쁜 코드

    ```react
    if (action.type === 'increment') {
    	state.counter++; //객체 및 배열에 직접 참조하는 행위이므로 리액트의 상태 관리에 심각히 위배되는 행위임.
        return state
     }
    ```

    

## 2. 최신 redux toolkit을 활용하는 방법

- `redux toolkit`은 `react-redux`에서 더욱 편리성을 높이고 상태를 자유롭게 추가할 수 있는 최신 `redux`이다.

- 달라진 점
  - `createStore`가 아닌 여러개의 `reducer`들을 관리하기 위해서 `configureStore`을 이용한다. 이는 쉽게 `slice`들을 묶고 컴포넌트 어디서든 참조해 사용할 수 있게 해준다.
  - 기존에는 **초기의 state**를 추가하면, 모든 반환되는 객체에 값을 추가해줘야해서 번거로웠지만, 이제는 반환하지 않고 직접 상태를 참조하듯 바꿔줘도 자동으로 위의 코드와 같이 변환해주므로 코드가 매우 간편해졌다.
  - `type`을 이용한 함수 호출이 필요없다. 각 모듈화된 `slice`에서 `Actions` 즉, 동작들을 `export`해줘 매우 편하다.



### store/index.js

- 모든 `slice` 즉, `redux`의 각자 관리되는 상태를 하나로 묶어주는 중앙 저장소 역할을 한다.

```react
import { configureStore } from "@reduxjs/toolkit";

import counterSlice from './counter';
import authSlice from './auth';

//counter, auth 각각의 slice.reducer(변화시키는것)을 저장소에 적용
const store = configureStore({
  reducer: { counter: counterSlice.reducer, auth: authSlice.reducer },
});

export default store;
```



### counter-slice.js

- 복잡하게 `index.js`에서 모든 코드들을 관리하면 난잡하고 바꾸기 쉽지 않으니 모듈화시켜 로직을 분리시키는 작업이다.
- 기존의 `createReducer`이 존재하지만, `createSlice`를 일반적으로 사용한다. 말 그대로 잘라서 `redux`상태의 조각으로 만들 수 있다. 인자는 객체를 받으며, 객체에서 `name`, `initialState`, `reducers`를 정의할 수 있다.

```react
import { createSlice } from "@reduxjs/toolkit";

const initialCounterState = { counter: 0, showCounter: true };

const counterSlice = createSlice({
  name: "counter",
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    },
    //손쉽게 인자를 action으로 받을 수 있고, payload를 참조해 값을 가져올 수 있다.
    increase(state, action) {
      state.counter += action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export const counterActions = counterSlice.actions
export default counterSlice;
```



### Counter.js 컴포넌트

```react
import classes from './Counter.module.css';
import { useSelector, useDispatch } from 'react-redux'
import { counterActions } from '../store/counter'

const Counter = () => {
  const dispatch =  useDispatch()
  const counter = useSelector(state => state.counter.counter)
  const show = useSelector(state => state.counter.showCounter)
  const incrementHandler = () => {
    // dispatch({ type: 'increment' }) 원래 redux 기능
    dispatch(counterActions.increment())
  }

  const increaseHandler = () => {
    dispatch(counterActions.increase(10))
  }

  const decrementHandler = () => {
    dispatch(counterActions.decrement());
  }

  const toggleCounterHandler = () => {
    dispatch(counterActions.toggleCounter())
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      {show && <div className={classes.value}>{counter}</div>}
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={increaseHandler}>Increment by 5</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```
