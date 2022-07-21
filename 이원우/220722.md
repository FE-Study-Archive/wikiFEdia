## Custom Hook

- 컴포넌트와 비슷하게 관리하며 상태를 관리하는 개별적인 기능을 가진 Hook이다.
  - 실제 `useState`, `useReducer`과 같이 정규 리액트 내장 hook과 같이 사용할 수 있다.

- 기본적으로, 함수 내에서 hook을 선언하지 못하지만, 커스텀 훅에서는 사용이 가능하다.

#### 규칙

- 무조건 커스텀 훅의 이름은 `use`로 시작해야한다.

- 반환하는 값이 있어야한다. 배열, 객체, 숫자 등 모든 것이 가능하다.
  - 객체 등은 실제로 사용할 때, 구조 분해 할당을 통해 사용하듯이 사용이 가능하다.

#### 예시

- 버튼을 누를 때 마다 `count`를 증가 시키거나 감소시키는 기능을 가진 함수를 만들 수 있다.
- 이 함수는 증감을 제외하면 같은 함수지만, 반복적으로 함수를 만들어야한다.
- 이에 재사용성을 높이기 위해 커스텀 훅을 만들 수 있다.

```react
import { useState, useEffect } from 'react'

const useCounter = (bool= true) => {
    const [counter, setCounter] = useState(0);
    
    useEffect(() => {
        const interval = setInterval(() => {
            if (bool) {
               setCounter((prev) => prev + 1); 
            }
            else {
               setCounter((prev) => prev - 1); 
            }            
        }, 1000)
       	
        return () => clearInterval(interval)
    }, [])
    
    return counter //실제 사용할 변수로 활용가능
}
export default useCounter;     


//다른 컴포넌트에서 사용한다면?
const counter = useCounter(false)
```

- 위에서 보듯 값, 함수 등을 받아 자유자재로 활용해 증감을 결정해 줄 수도 있다.





#### 쉽게 요청을 보내는 custom hook, useHttp 만들기

- 재사용이 유용하게 만드는 것이 핵심이다.
  - 한 컴포넌트가 아닌 요청을 보낼 때 항상 겹치는 부분을 재사용하도록 만들어야한다.
- 요청에는 분명 항상 세부 요청의 사항이 다를 것이다. 그러므로 없을 때 default 값을 설정해줘야한다.

```react
const useHttp = () => {
    // const [data, setData] = useState([]); 지엽적으로 사용하면 안된다.
	const [loading, setLoading] = useState([false])
	const [error, setError] = useState(null)

	const sendRequest = useCallback(async (requestConfig, applyData) => { //요청의 세부 내용이 담긴 객체, 받아올 함수
    	setLoading(true);
	    setError(null);
    
    try {
        const response = await fetch(requestConfig.url, {
            method: requestConfig.method ? requestConfig.method : 'GET',
            headers: requestConfig.headers ? requestConfig.headers : {},
            body: JSON.stringify(requestConfig.body) ? JSON.stringify(requestConfig.body) : null,
        })
        
        if (response.ok) {
        	return new Error("Something's wrong")
    	} //axios는 기본적으로 처리가 되어있음.
        
    	const data = await response.json();
		applyData(data)

    } catch(error) {
        setError(error.message);
    	}    
    setLoading(false);
    }, [requestConfig, applyData])
   	return {
        isLoading,
        error,
        sendRequest
    }
}

export default useHttp;
```



- 사용할 때,

```react
const [tasks, setTasks] = useState([])
const { isLoading, error, sendRequest: fetchTasks } = useHttp()

const transformTasks = useCallback((taskObj) => {
    const loadedTasks = [];
    
    for (const taskKey in taskObj) {
        loadedTasks.push({ id: taskKey, text: taskObj[taskKey].text })
    }
    
    setTasks(loadedTasks)
}, [])




//첫 인자는 요청 세부사항이 담긴 객체, 두 번째는 요청에 대한 세부 동작이 있는 함수


useEffect(() => {
    fetchTasks({url: 'https://주소주소'}, transformTasks);
}, [fetchTasks])


```

- 이렇게 사용할 때 주의 할 점은, 컴포넌트의 랜더링 시점을 잘 파악해야 무한루프에 걸리지 않는다는 점이다.

- 과정은 다음과 같다. 

  - useHttp를 호출하는 순간 컴포넌트의 상태가 바뀌므로 재랜더링된다.
  - 그 순간 useHttp도 다시 생성되며 호출된다.
  - 그리고, 커스텀 훅 내부의 sendRequest 함수 또한 재랜더링 되며 같은 함수를 덮어씌우고, 또 다른 객체를 반환한다.
  - 그리고 객체가 달라진 것을 감지한 useEffect는 `fetchTasks` 즉,  `sendRequest`로 인해 재 실행되고 같은 동작이 계속해서 반복된다.

- 해결법은 다음과 같다.

  - `useCallback`으로 커스텀 훅의 `sendRequest`를 감싸주면 메모리에 저장되어 재생성이 일어나지 않는다.

  - 또한, 사용할 때 넘겨주는 함수인 `transformTasks`도 재생성을 방지해야한다.

    - 만약, 이 과정이 싫다면, `useHttp`를 통해 넘겨주는 인자를 `sendRequest`가 받고, 반환받은 `sendRequest`에 인자로써 넘겨주게 만들어도 된다

    - ```react
      const { isLoading, error, sendRequjest: fetchTasks } = useHttp() 
      //첫 인자는 요청 세부사항이 담긴 객체, 두 번째는 요청에 대한 세부 동작이 있는 함수
      
      
      useEffect(() => {
          fetchTasks({url: 'https://주소주소'}, transformTasks);
      }, [fetchTasks])
      
      ```

