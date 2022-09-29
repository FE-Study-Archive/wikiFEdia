# Three.js

- 웹페이지에 3D 객체를 쉽게 렌더링하도록 도와주는 자바스크립트 3D 라이브러리
- WebGL 기반의 라이브러리로써, WebGL은 점, 선, 삼각형 만을 그릴 수 있으므로 공간을 만들려면 상당히 힘듬.



### 1. 기본 구성 요소

#### Scene 생성하기

- 구성 요소

  - Scene - 공간

  - Camera - 카메라

  - Meshes - 피사체(부피, 형태, 질감 등의 정의 필요)

  - Animation - 애니메이션

  - Light - 빛, 조명을 담당
    - Three.js의 구조도


  ![image](https://user-images.githubusercontent.com/90893428/191796389-637a294c-5215-431a-b93e-9bafe822464b.png)



#### 1) 렌더링 요소

> Renderer란? `Scene`과 `Camera` 객체를 넘겨 받아 카메라의 **절두체**안 3D 씬의 일부를 2차원 이미지로 렌더링해주는 객체
>
> 아래는 절두체의 예시.

![image](https://user-images.githubusercontent.com/90893428/191798179-3bef0dd4-297a-48fb-a4d0-7c356cb222fb.png)

- 안티엘리어싱

- 색상 결정

- 투명도 설정

- 예시

  - ```js
    const render = new THREE.WebGLRenderer({
        alpha: true,
        antialias: true
    })
    ```

  - 위는 Scene을 생성했다는 가정 하에, 랜더링할 수 있도록 Renderer 오브젝트를 생성한 것이다. 인자로는 배경을 투명하게 설정하는 `alpha`와 계단 현상 방지를 위한`antialias`가 포함되어있다.

### 2) Camera

> `Camera`는 다른 객체와 달리 Scene에 렌더링되는 `Scene Graph`에 포함되지 않아도 되는 요소이다.
>
> `Camera`가 `Scene Graph`에 포함되는 경우에는 부모 객체에 따라 `Camera`도 귀속되어 움직인다.

- 카메라 중 대표적인 원근 카메라를 설치해보자

```js
const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
const camera = new THREE.PerspectiveCamera(75, 2, 0.1, 5);
//fov: 시야각
//aspect: canvas의 가로 세로 비율(종횡비)
//near: 카메라에 담길 최소 거리
//far: 카메라에 담길 최대 거리
```



![image](https://user-images.githubusercontent.com/90893428/191799205-ce3e31e4-1cec-4f5f-b4d0-29eccdd5a7a6.png)

### 2) Mesh

> `Material`로 하나의 `Geometry`를 그리는 객체
>
> `Material`: 기하학 객체를 그리는데 사용하는 표면 속성(표면의 색, 밝기, 재질 등)
>
> `Geometry`: 기하학 객체의 정점 데이터. ex) 구, 면, 정육면체, 사람, 나무 등

![image](https://user-images.githubusercontent.com/90893428/191800971-daef33a1-ecd0-4402-92eb-be09b430b565.png)



- 면은 최소 3개의 꼭지점을 이어 만들 수 있다.

  - ```js
    const geometry = new THREE.Geometry();
    geometry.vertices.push(
    	new THREE.Vector3( -10,  10, 0 ),
    	new THREE.Vector3( -10, -10, 0 ),
    	new THREE.Vector3(  10, -10, 0 )
    ); //삼각형을 만들 수 있다.
    ```