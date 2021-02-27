---
title: "버블 정렬과 선택 정렬"
layout: post
categories: [알고리즘, 버블 정렬, 선택 정렬]
---

## summary

> 정렬 방법 중 버블 정렬과 선택 정렬에 대해 이해한다.

## 버블 정렬

> 리스트를 순회하며 인접한 두 요소를 직접 비교 후 정렬한다.

기본 컨셉은 아래와 같다.
1. 리스트의 첫 번째 두 번째 요소를 비교하여 값이 작은 것은 왼쪽으로, 큰 것은 오른쪽으로 위치시킨다.
2. 같은 방법으로 리스트의 끝까지 이동하면서 두 요소를 비교, 위치 변경을 반복한다.
3. 맨 끝에 위치한 요소는 제일 큰 값으로 정렬이 완료된 상태다.
4. 맨 끝을 제외한 리스트에서 1~3 과정을 반복한다.

시간 복잡도는 모든 배열을 배열의 길이만큼 반복하므로 O(n²) 이다.

버블정렬 함수를 구현해보자.
위 컨셉에서 4번 과정은 재귀함수로 구현한다.
첫 번째 두 번째 요소를 비교하여 값을 바꿔야한다면 swap 함수 호출 해 원본 배열의 두 요소에 대해 순서를 변경한다.
```javascript
const swap = (list, sourceIndex, targetIndex) => {
  const source = list[sourceIndex];
  const target = list[targetIndex];
  list[sourceIndex] = target;
  list[targetIndex] = source;
}

const bubbleSortRecursive = (list, lastIndex) => {
  if (lastIndex === 0) {
    return;
  }

  for (let i = 0; i <= lastIndex; i++) {
    const current = list[i];
    const next = list[i + 1];
    if (current > next) {
      swap(list, i, i + 1);
    }
  }
  bubbleSortRecursive(list, lastIndex - 1);
}

const bubbleSort = (list) => {
  bubbleSortRecursive(list, list.length - 1);
};
```

구현한 함수가 정상적으로 동작하는지 확인해 보자.
```javascript
const list = [3, 5, 1, 6, 8, 11];
console.log('before', list);
bubbleSort(list);
console.log('after', list);
```
테스트 결과는 다음과 같다.

![bubble sort test result](/assets/images/bubble-sort-test-result.png)

*전체 코드는 [링크](https://github.com/2nnovate/DSA/blob/master/algorithms/sort/bubbleSort.js)를 참고하자.*

## 선택 정렬

> 선형 탐색을 통해 가장 작은 값을 찾아 맨 앞으로 이동시키는 작업을 반복하여 정렬한다.

선택 정렬의 기본 컨셉은 다음과 같다.
1. 시작점(리스트[0])에서 부터 전체 리스트를 돌며(선형 탐색) 가장 작은 값을 찾는다.
2. 끝까지 이동하였을 때 가작 작은 값을 맨 앞의 요소와 위치를 바꾼다.(맨 앞으로 이동시킨다)
3. 맨 처음 요소는 정렬이 완료되었다.
4. 정렬이 완료된 요소를 제외한 리스트를 대상을 1~3 과정을 반복한다.

버블 정렬과 마찬가지로 전체 리스트를 조회하기 때문에 시간 복잡도는 O(n²) 이다.

선택 정렬을 코드로 구현해보자.
버블정렬과 마찬가지로 재귀호출을 통해 작업을 반복시킨다.
```javascript
const swap = (list, sourceIndex, targetIndex) => {
  const source = list[sourceIndex];
  const target = list[targetIndex];
  list[sourceIndex] = target;
  list[targetIndex] = source;
}

const selectionSortRecursive = (list, startIndex) => {
  const lastIndex = list.length - 1;
  if (startIndex === lastIndex) {
    return;
  }

  for (let i = startIndex; i < lastIndex; i++) {
    let min = list[startIndex];
    const next = list[i + 1];
    if (min > next) {
      swap(list, i + 1, startIndex);
    }
  }
  selectionSortRecursive(list, startIndex + 1);
};

const selectionSort = (list) => {
  selectionSortRecursive(list, 0);
}
```

구현한 함수가 정상적으로 동작하는지 확인해 보자.
```javascript
const list = [3, 5, 1, 6, 8, 11];
console.log('before', list);
selectionSort(list);
console.log('after', list);
```
테스트 결과는 다음과 같다.

![selection sort test result](/assets/images/selection-sort-test-result.png)

*전체 코드는 [링크](https://github.com/2nnovate/DSA/blob/master/algorithms/sort/selectionSort.js)를 참고하자.*

## end

정렬 알고리즘 중 버블 정렬, 선택 정렬에 대해서 알아보았다.
시간 복잡도는 두 알고리즘 모두 O(n²) 이며, 이보다 더 시간 효율이 좋은 정렬방법에 대해서도 알아보도록 하자.

