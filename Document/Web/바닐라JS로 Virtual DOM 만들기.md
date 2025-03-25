## 개요
바닐라 JavaScript 기반의 SPA 애플리케이션에서 Virtual DOM을 직접 구현했습니다. 
어떻게 구현했는지와 함께, Virtual DOM 도입 전후의 실제 성능 차이를 실험을 통해 비교 합니다.

## Virtual DOM이란?
Virtual DOM은 실제 DOM을 직접 조작하는 대신, 메모리 상의 가벼운 JavaScript 객체 트리를 사용해 변경 사항을 계산한 뒤,  
실제 DOM에는 변경된 부분만 최소한으로 반영하는 렌더링 최적화 기법입니다.

## 구현방식
### 1. VNode
Virtual DOM은 VNode라는 객체의 트리로 구성합니다.
VNode는 아래와 같은 구조로 표현됩니다.
```
{  
	type: "div",
	props: {},  
	children: [ 
		"안녕하세요!",  
		  {  "type": "p", "props": {}, children: ["반갑습니다."]  }
	]
}
```
### 2. DOM 트리 -> VNode 트리
DOM 트리를 VNode 트리로 변환하는 로직입니다.
DOM 노드를 재귀적으로 순회하면서 각 노드를 VNode 형태로 변환해 트리를 구성합니다.
```js
// HTML string을 VNode 트리로 변환  
function htmlToVNode(htmlString) {  
  const template = document.createElement('template');  
  template.innerHTML = htmlString.trim();  
  return domToVNode(template.content.firstChild);  
}  
  
// DOM 노드를 재귀적으로 순회하면서 VNode 트리 생성  
function domToVNode(node) {  
  if (node.nodeType === Node.TEXT_NODE) {  
    // 텍스트 노드는 그대로 text를 반환  
    return node.textContent;  
  }  
  
  const type = node.nodeName.toLowerCase();  
  const props = {};  
  for (const attr of node.attributes) {  
    props[attr.name] = attr.value;  
  }  
  
  // 자식 노드들을 재귀적으로 순회하며 array에 넣음  
  const children = Array.from(node.childNodes).map(domToVNode);  
  return { type, props, children };  
}
```

### 3. diffing 알고리즘
이전 VNode와 새로운 VNode를 비교하여 어떤 변경이 발생했는지를 계산합니다.  
이를 통해 실제 DOM에서 어떤 작업이 필요한지를 파악할 수 있습니다.
```js
function diffing(oldVNode, newVNode) {  
  // 노드 제거 및 추가  
  if (!oldVNode) return { type: "CREATE", newVNode };  
  if (!newVNode) return { type: "REMOVE" };  
  
  // 텍스트 노드 비교  
  if (typeof oldVNode === "string" || typeof newVNode === "string") {  
    if (oldVNode !== newVNode) {  
      return { type: "REPLACE", newVNode };  
    } else {  
      return null; // 텍스트가 같으면 아무 작업 필요 없음  
    }  
  }  
  
  // 타입이 달라지면 교체  
  if (oldVNode.type !== newVNode.type) {  
    return { type: "REPLACE", newVNode };  
  }  
  
  // props가 변경됐다면  
  const props = [];  
  for (const key in { ...oldVNode.props, ...newVNode.props }) {  
    if (oldVNode.props[key] !== newVNode.props[key]) {  
      props.push({ key, value: newVNode.props[key] });  
    }  
  }  
  
  // 자식 노드들도 재귀적으로 비교  
  const children = [];  
  const len = Math.max(oldVNode.children.length, newVNode.children.length);  
  for (let i = 0; i < len; i++) {  
    children.push(diffing(oldVNode.children[i], newVNode.children[i]));  
  }  
    
  // props 변경이 없고, 자식 노드 또한 변경이 없다면 patch X
  if (props.length === 0 && children.every(child => child === null)) {  
    return null;  
  }
  
  return { type: "UPDATE", props, children };  
}
```
### 4. VNode트리 -> DOM 트리
VNode를 실제 DOM 노드로 변환하는 작업을 수행합니다.
```js
function renderVNode(vnode) {  
  // 텍스트 노드는 TextNode로 생성  
  if (typeof vnode === "string") return document.createTextNode(vnode);  
  
  const $element = document.createElement(vnode.type);  
  for (const [key, value] of Object.entries(vnode.props)) {  
    $element.setAttribute(key, value);  
  }  
  
  // 자식들을 재귀적으로 변환  
  vnode.children.forEach((child) => {  
    $element.appendChild(renderVNode(child));  
  });  
  
  return $element;  
}
```

### 5. patch
diffing을 통해 얻은 변경 사항을 기반으로 실제 DOM에 반영하는 단계입니다.  
```js
function patchToDOM($parent, patchNode, index = 0) {  
  // diff에서 달라진게 없다면 리턴  
  if (!patchNode) {  
    return;  
  }  
  
  const $element = $parent.childNodes[index]; // 변경 대상 DOM 노드  
  
  switch (patchNode?.type) {  
    case "CREATE":  
      $parent.appendChild(renderVNode(patchNode.newVNode));  
      break;  
  
    case "REMOVE":  
      $parent.removeChild($element);  
      break;  
  
    case "REPLACE":  
      $parent.replaceChild(renderVNode(patchNode.newVNode), $element);  
      break;  
  
    case "UPDATE":  
      // 속성 변경 적용  
      patchNode.props.forEach(({ key, value }) => {  
        if (value === undefined) $element.removeAttribute(key);  
        else $element.setAttribute(key, value);  
      });  
  
      // 자식 노드들도 재귀적으로 patch 수행  
      patchNode.children.forEach((childPatch, i) => {  
        patchToDOM($element, childPatch, i);  
      });  
      break;  
  }  
}
```


## 성능 테스트
### 1번 테스트 - 극단적인 상황
![[vdom-performance-test-page.png]]

성능 테스트를 위해 극단적인 상황을 가정해봤습니다.
각 아이템의 좋아요 버튼을 누르면 해당 아이템의 count가 1씩 증가하는 테스트 상황입니다.
각 케이스 별 좋아요 버튼을 10회 눌러 테스트를 진행했습니다.

1. Virtual DOM을 사용 안하는 경우
	![[performance-test-without-vdom.png]]
2. Virtual DOM을 사용하는 경우
	![[performance-test-with-vdom.png]]

> 271.1ms -> 143.2ms로 47.2%의 성능 개선이 있었습니다.

### 2번 테스트 - 일반적인 상황
![[vdom-performance-test-page2.png]]
게시글에서 좋아요를 누르면 좋아요 버튼의 배경 색이 바뀌면서 count가 증가하는 상황입니다.
마찬가지로 각 케이스 별 좋아요 버튼을 10회 눌러 테스트를 진행했습니다.
1. Virtual DOM을 사용 안하는 경우
	![[performance-test2-without-vdom.png]]
2. Virtual DOM을 사용하는 경우
	 ![[performance-test2-with-vdom.png]]
> 각 64.5ms, 65.3ms으로 비슷한 성능을 보였습니다.


## 결론
- Virtual DOM을 직접 구현하면서 내부의 동작 원리를 구체적으로 이해할 수 있었습니다.
- 복잡한 컴포넌트 구조에서는 Virtual DOM이 렌더링 및 페인팅 비용을 줄여 전체 성능 향상에 도움이 되었습니다.
- DOM 구조가 단순한 페이지에서는 Virtual DOM의 오버헤드가 발생하여 오히려 성능이 저하될 수 있습니다.
- 렌더링 전략을 컴포넌트 단위로 제어할 수 있도록 `render()` 함수에 Virtual DOM 사용 여부를 옵션으로 지정하는 방식은 유연한 최적화에 유용할 수 있습니다.