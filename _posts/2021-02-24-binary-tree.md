---
title: "이진 탐색 트리 자료구조에 대한 이해"
layout: post
categories: [자료구조, javascript, binary-tree, binary-search-tree]
---

## summary

> 이진트리(binary-tree) 자료구조에 익숙해진다.

### 트리 구조

> 부모-쟈식 관계를 가진 다수의 노드로 구성된 자료구조.

회사의 조직도, 가계도와 같이 부모-자식 관계를 가진 노드의 다발을 트리구조라고 한다.
각 노드는 부모를 가지며(root 노드를 제외), 자식을 가질 수 있다.

### 이진 트리(binary-tree)와 이진 탐색 트리(binary-search-tree)

* 이진 트리 
  * 트리 구조 중 각 노드가 최대 2개의 자식(왼쪽, 오른쪽에 각각 하나씩)을 가질 수 있는 트리 구조를 의미한다.
* 이진 탐색 트리 
  * 좌측 자식에는 자신보다 작은 값을, 우측 자식에는 자신보다 더 큰 값을 가지는 이진 트리 구조이다.
    
### 이진 탐색 트리(BST) 구현

각 노드가 key 와 left, right 를 갖는 BST를 구현해보자.
해당 클래스에서 필요한 메소드는 다음과 같다.

* insert(key)
  * 새로운 노드를 추가한다.
* inOrderTraverse()
  * 중위 순회 방식(left -> node -> right)으로 모든 노드를 탐색한다.
* preOrderTraverse()
  * 전위 순회 방식(node -> left -> right)으로 모든 노드를 탐색한다.
* postOrderTraverse()
  * 후위 순회 방식(left -> right -> node)으로 모든 노드를 탐색한다.
* search(key)
  * 인자로 전달받은 key에 해당하는 노드가 존재하는지 여부를 반환한다.
* min()
  * 트리의 최소값을 반환한다.
* max()
  * 트리의 최대값을 반환한다.
* remove(key)
  * key 에 해당하는 노드륵 삭제한다.

#### Node 클래스 구현

먼저 BST의 노드를 먼저 구현해보자.
노드는 key, left, right 를 멤버변수로 갖는다.
key 는 생성자를 통해 전달받고, left와 right는 null 로 초기화한다.

```javascript
class Node {
  costructor(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  }
}
```

#### BST 기본 틀

프라이빗한 클래스 멤버변수 root 를 null 로 초기화한다.
root 멤버변수는 다양한 메소드에서 사용, 업데이트된다.

```javascript
class BinarySearchTree {
  #root = null;
}
```

#### 노드 추가

재귀함수를 통해 노드가 올바른 위치에 추가될 수 있도록한다.

```javascript
insert(key) {
  const node = new Node(key); // key를 가지고 Node 인스턴스 생성

  if (!this.#root) { // 루트가 비어있다면(첫 번째 insert 인 경우)
    this.#root = node; // 루트에 노드 할당하고 종료
    return;
  }

  this.#insertNode(this.#root, node); // 루트값을 기준으로 하여 추가 작업 시작
}

#insertNode = (node, newNode) => {
  // early return validation
  if (!node || !node.key) return;
  if (!newNode || !newNode.key) return;
  if (node.key === newNode.key) return;

  if (newNode.key < node.key) { // 추가할 노드가 기준 노드보다 왼쪽에 위치해야 하는 경우 (값이 작은 경우)
    if (!node.left) { // 기준노드에 left 가 비어있는 경우 => 할당하고 종료
      node.left = newNode;
      return;
    }
    this.#insertNode(node.left, newNode); // 기준노드에 left 가 존재하는 경우 => 추가 비교 필요, 기준노드를 변경하여 재귀호출
    return;
  }

  // 추가할 노드가 기준 노드보다 오른쪽에 위치해야 하는 경우 (값이 더 큰 경우)
  if (!node.right) { // 기준노드에 right 가 비어있는 경우 => 할당하고 종료
    node.right = newNode;
    return;
  }
  this.#insertNode(node.right, newNode); // 기준노드에 right 가 존재하는 경우 => 추가 비교 필요, 기준노드를 변경하여 재귀호출
}
```

#### 트리 탐색

트리 탐색은 세 가지 방식으로 진행한다.
* 중위 순회
  * 왼쪽 -> 자기자신 -> 오른쪽
  * 즉, 작은것 부터 큰 순으로(오름차순)
* 전위 순회
  * 자기자신 -> 왼쪽 -> 오른쪽
  * 자신을 먼저 탐색 후, 왼쪽과 오른쪽을 탐색
  * 트리 구조 그대로 탐색, 구조화된 문서를 출력할 때 사용하는 방식
* 후위 순회
  * 왼쪽 -> 오른쪽 -> 자기자신
  * 자식들을 먼저 탐색 후 자기 자신을 탐색
  * 파일의 용량을 계산하는 등의 작업을 할 때 사용하는 방식
  
```javascript
inOderTraverse() {
    const traverseKeys = [];
    const printTraverseKeys = (key) => {
      traverseKeys.push(key);
    };

    this.#inOrderTraverseNode(this.#root, printTraverseKeys);
    console.log(traverseKeys);
  }

#inOrderTraverseNode = (node, callback) => {
  if (!node) return;

  this.#inOrderTraverseNode(node.left, callback);
  callback(node.key);
  this.#inOrderTraverseNode(node.right, callback);
}

preOrderTraverse() {
  const traverseKeys = [];
  const printTraverseKeys = (key) => {
    traverseKeys.push(key);
  };

  this.#preOrderTraverseNode(this.#root, printTraverseKeys);
  console.log(traverseKeys);
}

#preOrderTraverseNode = (node, callback) => {
  if (!node) return;
  callback(node.key);
  this.#preOrderTraverseNode(node.left, callback);
  this.#preOrderTraverseNode(node.right, callback);
}

postOrderTraverse() {
  const traverseKeys = [];
  const printTraverseKeys = (key) => {
    traverseKeys.push(key);
  };

  this.#postOrderTraverseNode(this.#root, printTraverseKeys);
  console.log(traverseKeys);
}

#postOrderTraverseNode = (node, callback) => {
  if (!node) return;
  this.#postOrderTraverseNode(node.left, callback);
  this.#postOrderTraverseNode(node.right, callback);
  callback(node.key);
}
```

#### 노드 검색

특정노드를 찾거나, 최소값, 최대값을 구하는 메소드를 구현한다.

```javascript
search(key) {
  return this.#searchNode(this.#root, key);
}

#searchNode = (node, key) => {
  if (!node) return false;
  if (key < node.key) { // 검색 대상이 왼쪽에 위치하는 경우
    return this.#searchNode(node.left, key); // 왼쪽 서브트리에서 다시 검색한다.
  }
  if (key > node.key) { // 검색 대상이 오른쪽에 위치하는 경우
    return this.#searchNode(node.right, key); // 오른쪽 서브트리에서 다시 검색한다.
  }
  if (key === node.key) return true; // 값이 존재하면 true 반환
  return false; // 존재하지 않으면 false 반환
}

min() {
  const minNode = this.#minNode(this.#root);
  return minNode && minNode.key;
}

#minNode = (node) => {
  if (!node) return;
  while (node && node.left) { // 왼쪽에 노드가 있을때(더 작은 값이 존재) 까지 반복
    node = node.left;
  }
  return node;
}

max() {
  const maxNode = this.#maxNode(this.#root);
  return maxNode && maxNode.key;
}

#maxNode = (node) => {
  if (!node) return;
  while (node && node.right) { // 오른쪽에 노드가 있을때(더 큰 값이 존재) 까지 반복
    node = node.right;
  }
  return node;
}
```

#### 노드 삭제

노드의 삭제는 세 가지 경우에 따라서 삭제 작업이 달라진다.
* 삭제할 노드가 말단 노드(leaf) 인 경우
  * 해당 노드만 삭제하고 추가 작업 필요 없음
* 삭제할 노드의 자식이 하나인 경우
  * 노드를 삭제하고, 자식을 끌어 올린다. (자식을 할아버지의 자식으로 링크시킨다)
* 삭제할 노드의 자식이 둘 인 경우
  * 노드를 삭제하고, 오른쪽 서브트리에 있는 최소값을 삭제된 노드 자리에 위치시킨다.
  * 이진 탐색 트리는 오른쪽은 무조건 자신보다 큰 값, 왼쪽은 작은 값이므로 삭제된 노드의 오른쪽 서브 트리의 최소값은 곧 삭제된 노드의 다음 값이다.

```javascript
remove(key) {
    this.#root = this.#removeNode(this.#root, key);
  }

#removeNode = (node, key) => {
  if (!node) return null;

  if (key < node.key) { // 삭제할 노드가 기준 노드보다 왼쪽에 존재하는 경우 (값이 작은 경우)
    node.left = this.#removeNode(node.left, key); // node 의 왼쪽 서브트리를 기존 서브트리에서 삭제 대상노드를 삭제한 서브트리로 재할당
    return node; // node 를 리턴 (첫 실행시 node는 root)
  }

  if (key > node.key) { // 삭제할 노드가 기준 노드보다 오른쪽에 존재하는 경우 (값이 큰 경우)
    node.right = this.#removeNode(node.right, key); // node 의 오른쪽 서브트리를 기존 서브트리에서 삭제 대상노드를 삭제한 서브트리로 재할당
    return node; // node 를 리턴 (첫 실행시 node는 root)
  }

  if (key === node.key) { // 삭제할 대상 노드를 찾은 경우
    // case1 - 삭제할 노드가 마지막 말단 노드일 경우
    if (!node.left && !node.right) return null;

    // case2 - 삭제할 노드의 자식이 1개 있을 경우
    if (!node.left && node.right) return node.right;
    if (node.left && !node.right) return node.left;

    // case3 - 삭제할 노드의 자식이 2개 있을 경우
    // 우측 서브트리의 최소값을 찾는다
    const replaceNode = this.#minNode(node.right);
    // 최소값으로 대체
    node.key = replaceNode.key;
    // 우측 서브 트리에서 최소값을 제거
    node.right = this.#removeNode(node.right, replaceNode.key);
    return node;
  }

  return node; // 삭제 대상이 없는 경우 노드를 그대로 반환
}
```

노드를 리턴한다는 것은 해당 서브트리 전체를 리턴하는 것이라고 인식하고 살펴본다면 좀 더 쉽게 이해가 가능하다.

*전체 코드는 [링크](https://github.com/2nnovate/DSA/blob/master/dataStructures/nonLinear/tree/BinarySearchTree.js)를 참고하자.*

## end

트리 자료 구조 중 왼쪽에 더 작은 값을, 오른쪽에 더 큰 값을 가지는 이진 탐색 트리에 대해서 알아보았다.
탐색이 필요한 로직에 적절히 구현해보자.
