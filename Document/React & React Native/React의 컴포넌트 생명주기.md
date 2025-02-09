# React의 컴포넌트 생명주기

## 생명주기의 유형

크게 3가지로 나눠짐

- Mount: 컴포넌트 내에서 DOM이 생성되고 브라우저에서 나타나는 과정
- Update: 컴포넌트 내에서 변화가 발생했을 때
- Unmount: 컴포넌트 내에서 DOM이 제거되고 브라우저에서 사라지는 과정

## 클래스 컴포넌트의 생명주기
##### (1) Mount
1. 컴포넌트가 호출되어 로드
2. constructor(): 데이터의 바인딩, 초기화 수행
3. getDerivedStateFromProps(): props가 변경될 때 실행.
4. render() 함수 실행: 화면의 렌더링 수행
5. componentDidMount(): 화면이 렌더링 된 후 해당 컴포넌트를 DOM 트리에 삽입 후 호출
##### (2) Update
1. (props 변경 시) getDerivedStateFromProps(): 새로운 props가 전달될 때 state 업데이트 가능
2. shouldComponentUpdate(): 렌더링 진행 여부 설정 가능
3. render()
4. getSnapshotBeforeUpdate(): render 이후 실행. 잘 쓰이진 않지만, 렌더링 되는 동안 스크롤 위치를 유지하는 등의 경우에 사용
5. componentDidUpdate(): 컴포넌트의 변화가 발생된 후 호출
##### (3) Unmount
1. componentWillUnmount(): 컴포넌트가 DOM에서 제거되기 직전에 호출


![[react-class-component-life-cycle.jpg]]
사실 이것 외에 여러 생명주기가 있지만, 잘 사용되지 않아 밑에 따로 정리.

## 함수형 컴포넌트의 생명주기
1. . 컴포넌트가 호출되어 로드
2. 컴포넌트 내부 함수 수행: 데이터 초기화
3. return(): 화면의 렌더링
4. useEffect(): 의존성 배열에 따라 mount, update, unmount모두 호출 가능
![[react-func-component-life-cycle.png]]

## 클래스 컴포넌트의 생명주기(deprecated)
React v16.3 이전의 라이프 사이클
##### (1) Mount
- `constructor()`: 컴포넌트가 생성될 때 호출
- `componentWillMount()`: 컴포넌트가 마운트되기 직전에 실행 (16.3 이후 사용 중단됨)
- `render()`: UI를 렌더링하는 메서드로 JSX를 반환
- `componentDidMount()`: 컴포넌트가 DOM에 삽입된 후 실행
##### (2) Update
- `componentWillReceiveProps(nextProps)`: 새로운 props가 전달될 때 호출 (16.3 이후 사용 중단됨)
- `shouldComponentUpdate(nextProps, nextState)`: 컴포넌트 업데이트 직전에 호출되는 메소드. 성능 최적화를 위해 렌더링 여부를 결정할 수 있음
- `componentWillUpdate(nextProps, nextState)`: 업데이트가 일어나기 직전에 실행 (16.3 이후 사용 중단됨)
- `render()`: 변경된 상태를 반영하여 UI를 다시 렌더링
- `componentDidUpdate(prevProps, prevState)`: 업데이트 완료 후 실행
##### (3) Unmount
- `componentWillUnmount()`: 이벤트 리스너 해제, 네트워크 요청 취소 등의 정리 작업을 수행

## React v16.3 이후에 변경된 부분
- `componentWillMount`, `componentWillReceiveProps`, `componentWillUpdate` 사용불가
- `componentWillReceiveProps` -> `getDerivedStateFromProps`
- `componentWillUpdate` -> `getSnapshotBeforeUpdate`
- `componentDidCatch` 컴포넌트 에러 핸들링 API 추가