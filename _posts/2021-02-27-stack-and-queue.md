---
title: "스택, 큐 자료 구조에 대한 이해"
layout: post
categories: [자료구조, 스택, 큐]
---

## summary

> 선형 자료 구조인 스택과 큐 자료 구조를 이해하고, 직접 구현해 본다.

## 스택(Stack)

> 마지막에 추가한 요소를 제일 처음으로 꺼내는 자료구조

쌓여있는 책더미나, 아래가 막혀있는 사탕 통 등이 이에 해당한다.
제일 나중에 추가한 요소를 제일 처음으로 꺼낸다 하여 **후입선출**데이터 구조라고 한다.

스택에 요소를 추가하는 작업을 push 라고 하고, 꺼내는 작업을 pop 이라고 한다.
이를 기반으로 Stack 클래스를 구현해 보자.
```javascript
class Stack {
  #items = [];

  push(elements) {
    this.#items.push(elements);
  }

  pop() {
    return this.#items.pop();
  }

  peek() {
    return this.#items[this.size - 1];
  }

  clear() {
    this.#items = [];
  }

  print() {
    console.log(this.#items.join(', '));
  }

  get size() {
    return this.#items.length;
  }

  get isEmpty() {
    return this.size === 0;
  }
}
```

구현한 클래스가 정상적으로 동작하는지 테스트 해 보자.
```javascript
const stack = new Stack();
stack.push('A');
stack.push('B');
stack.push('C');
stack.push('D');
stack.print();
console.log('size', stack.size);

const removedItem = stack.pop();
console.log('removedItem', removedItem);
console.log('(after remove) size', stack.size);
console.log('(after remove) isEmpty', stack.isEmpty);

const lastItem = stack.peek();
console.log('lastItem', lastItem);

stack.clear();
console.log('(after clear) size', stack.size);
console.log('(after clear) isEmpty', stack.isEmpty);
```
결과는 다음과 같다.
![stack test result](/assets/images/stack-test-result.png)

*전체 코드는 [링크](https://github.com/2nnovate/DSA/blob/master/dataStructures/linear/stack/index.js)에서 확인할 수 있다.*

## 큐(Queue)

> 먼저 추가된 순서대로 꺼내는 자료구조

화장실 앞에 서 있는 대기줄을 생각하면 이해가 쉽다.
먼전 온 사람이 나중에 온 사람보다 먼저 들어가야 한다.
그렇지 않으면 혈투가 일어날 수 있다.

큐에 요소를 추가하는 것을 enqueue 라고 하고, 요소를 꺼내는 것을 dequeue 라고 한다.
먼저 들어간 요소가 먼저 꺼내진다 하여 **선입선출**자료 구조라고도 한다.

Queue 클래스를 코드로 구현해보자.
```javascript
class Queue {
  #items = [];

  enqueue(elements) {
    this.#items.push(elements);
  }

  dequeue() {
    return this.#items.shift();
  }

  front() {
    return this.#items[0];
  }

  clear() {
    this.#items = [];
  }

  print() {
    console.log(this.#items.join(', '));
  }

  get size() {
    return this.#items.length;
  }

  get isEmpty() {
    return this.size === 0;
  }
}
```
Queue 클래스가 정상 동작하는지 테스트해 보자.
```javascript
const queue = new Queue();
queue.enqueue('A');
queue.enqueue('B');
queue.enqueue('C');
queue.enqueue('D');
queue.print();

const removedItem = queue.dequeue();
console.log('removedItem', removedItem);
console.log('(after remove) size', queue.size);
console.log('(after remove) isEmpty', queue.isEmpty);

const firstItem = queue.front();
console.log('firstItem', firstItem);

queue.clear();
console.log('(after clear) size', queue.size);
console.log('(after clear) isEmpty', queue.isEmpty);
```
테스트의 결과는 다음과 같다.
![queue test result](/assets/images/queue-test-result.png)

*전체 코드는 [링크](https://github.com/2nnovate/DSA/blob/master/dataStructures/linear/queue/index.js)에서 확인할 수 있다.*

## end

선형 자료 구조인 스택과 큐에 대해서 알아보았다.
스택과 큐의 차이점을 이해하면 DFS, BFS 알고리즘을 이해하는데 큰 도움이 되니 숙지하길 바란다.


