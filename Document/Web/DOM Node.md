# DOM Node

### Node
- DOM은 Node를 정의하고, 그 관계를 트리구조를 통해 계층적으로 나타낸 모델
- 문서, 태그, 텍스트, 주석 등 모든 구조는 Node로 표현됨
```
{
	type: "div"
	props: {class: 'comment-item'}
	children: [
		  {type: 'div', props: {…}, children: Array(5)},
		  {type: 'p', props: {…}, children: Array(1)},
	]
}
```



### Node의 종류
node.nodeType으로 값을 가져올 수 있음
- ELEMENT_NODE(1): HTML 요소(div, p 등)
- TEXT_NODE(3): 요소의 텍스트 콘텐츠. 순수 텍스트로 자식 노드를 가질 수 없음
- COMMENT_NODE(8): 주석
- DOCUMENT_NODE(9): document 객체로 DOM 트리 최상위에 존재

### Node의 프로퍼티와 메서드
##### 주요 프로퍼티
- `nodeType`: 노드의 타입 (상수 숫자 값)
- `nodeName`: 노드 이름 (`DIV`, `#text` 등)
- `nodeValue`: 텍스트나 주석 노드의 값
- `childNodes`: 자식 노드 리스트 (NodeList)
- `parentNode`: 부모 노드
- `firstChild`, `lastChild`, `nextSibling`, `previousSibling`
##### 주요 메서드
- `appendChild(node)`: 자식 노드로 추가
- `removeChild(node)`: 자식 노드를 제거
- `cloneNode(deep?)`: 노드를 복제 (deep을 넣으면 자식까지 복제)
- `contains(node)`: 해당 노드가 포함되어 있는지 여부
- `hasChildNodes()`: 자식 노드 존재 여부 확인

### Element
- node의 특정 타입인 ELEMENT_NODE
- `<html>`, `<div>`와 같은 태그들이 element
- 주석이나 Text Node는 elemet가 아님
- HTML과 밀접한 프로퍼티/메서드를 제공
```
<div class="comment-item">
	<div>
		...
	</div>
	<p>...</p>
</div>
```


## Reference
<!-- 사용하지 않는 레퍼런스 종류는 삭제 후 업로드 -->
- [DOM Node란 무엇인가 그리고 DOM Element와의 차이점은?](https://lifeandit.tistory.com/70)
- [(MDN) Node](https://developer.mozilla.org/en-US/docs/Web/API/Node)
- [(MDN) Element](https://developer.mozilla.org/en-US/docs/Web/API/Element)