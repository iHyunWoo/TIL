# Reflow와 Repaint

## 브라우저의 렌더링
브라우저의 렌더링 과정은 아래와 같다.
1. DOM & CSSOM 트리를 생성
2. 두 트리를 결합하여 Render Tree 생성
3. Render Tree를 기반으로 요소의 위치, 크기 등을 계산. Layout 과정
4. 요소에 스타일을 적용. Paint 과정

## Reflow와 Repaint
- reflow와 repaint는 요소가 시각적으로 변경되었을 때, 변화를 계산하여 화면에 그려주는 작업
- reflow: 요소의 너비, 높이, 위치 등이 변경되어 렌더 트리를 재성성하는 작업
	- 브라우저 사이즈 변경시에도 발생
	- JS에서 DOM관련 메소드를 실행하거나, DOM의 속성에 접근할 때도 발생(요소의 최신값을 알기 위해)
	- 특정 요소에서 reflow가 발생하면 주변 요소에도 영향을 주기 때문에 비용이 큰 작업
- repaint: 변경된 요소를 화면에 그려주는 작업
	- 리플로우가 발생했을 때
	- 요소의 스타일이 변경되었을 때
	- visibility가 변경될 때
		> - visibility는 화면에서 숨기는 것에 관한 속성. 위치는 차지하고 있기에 값이 변경되면 리페인트만 발생
		> - display는 화면 구성에서 아예 사라지게하는 속성. 렌더트리에도 포함안됨. 값이 변경되면 리플로우, 리페인트 모두 발생

## Reflow와 Repaint 줄이기
- 이러한 리플로우, 리페인트 작업은 비용이 드는 작업으로 줄일 수 있다면 줄여야 함
1. 요소의 position을 absolute로 두기
	- 애니메이션으로 요소의 위치를 변경할 때, 주변 요소의 위치도 변경되어 리플로우가 여러 번 발생
	- position을 absolute로 하여 주변 요소에 영향 없도록
```css
.some-element {
  position: absolute;
  animation: moveTo 2s;
}
```
1. 리플로우 유발 함수의 호출 최적화
```js
  const el1 = document.querySelector('#first');
  const el2 = document.querySelector('#second');
  // bad
  for(let i=1; i<10; i++){
	el1.style.width = (el2.getBoundingClientRect().left + i) + 'px';
  }

  // good
  let {left} = el2.getBoundingClientRect();
  for(let i=1; i<10; i++){
	  el1.style.width = (left + i) + 'px';
	  left += i;
  }
```
- 요소의 크기, 위치 정보를 리턴하는 getBoundingClientRect()는 리플로우를 유발함
	- 해당 함수를 한 번만 호출하니 리플로우가 한 번만 발생
2. 리플로우 유발 요소 모으기
```js
// Bad
const h1 = el1.clientHeight;
el1.style.width = '10px';

const h2 = el2.clientHeight;
el2.style.width = '10px';

const h3 = el3.clientHeight;    
el3.style.width = '10px';

// Good
const h1 = el1.clientHeight;
const h2 = el2.clientHeight;
const h3 = el3.clientHeight;    

el1.style.width = '10px';
el2.style.width = '10px';
el3.style.width = '10px';
```
- clientHeight는 항상 최신 값을 가져오기 때문에, 큐에 쌓인 작업을 모두 처리하고(리플로우 발생) 값을 계산

## "리페인트/리플로우를 줄인다"고 알려져 있으나, 의미없던 것
3. CSS로 스타일 한 번에 변경하기
```js
const el1 = document.querySelector('#first');
el1.style.width = '200px';
el1.style.backgroundColor = 'blue';
el1.style.borderRadius = '10px';

//===============

#first.active {
  width: 200px;
  height: 100px;
  background: blue;
}

const el1 = document.querySelector('#first');
el1.className ='active'
```
- 브라우저는 리플로우를 줄이기 위해 매번 렌더링을 하는게 아닌, 큐에 저장해놨다 일정 시간이 지나거나 처리할 작업이 쌓인 경우에 수행하기 때문에 