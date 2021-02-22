---
title: "javascript set, map 자료구조"
layout: post
categories: [자료구조, javascript, set, map]
---

## summary

> 자바스크립트의 자료구조 set, map 에 대해서 익숙해진다.
> 
> 해시테이블을 구현한다.

지료구조 및 알고리즘 공부를 시작했다. 비전공자이기에 약한 분야라 생각했다.
이번 기회에 정리해 보도록 하겠다. 가장 먼저 ES6에 추가된 Set(집합), Map(딕셔너리) 에 대해 정리해보겠다.

## Set(집합)

> 중복된 원소가 없는 값 콜렉션, 삽입 순서대로 요소를 순환할 수 있다.

*[value, value] 의 구조라 생각하면 이해가 빠르다.* 
Set 자료구조가 가진 프로퍼티와 메소드를 살펴보고, 수학에서 사용하는 집합을 Set 을 통해 구현해보자.
(합집합, 차집합, 교집합)

### 프로퍼티

* size
    * Set 객체의 길이를 반환한다.
    
### 메서드

* has(value)
  * Set 의 요소 중에 value 가 존재하는지 여부를 반환한다.
* add(value)
  * Set 객체에 새로운 요소를 추가한다.
  * value 가 기존 Set 의 요소중에 존재했다면 중복으로 추가되지 않는다. (추가하지 않는 것과 동일)
  * Set 객체를 반환한다.
* delete(value)
  * Set 의 요소 중 value 를 제거한다.
  * 제거에 성공하면 true, 실패하면 false 를 반환한다.
* clear()
  * Set 객체의 모든 요소를 제거한다.
* keys()
  * values() 와 동일 (set 은 key 만으로 이루어짐)
* values()
  * 삽입순으로 Set 객체의 모든 요소를 iterator 객체로 반환한다.
* entries()
  * [value, value] 배열을 포함하는 새로운 iterator 객체를 반환한다.
* forEach(callback[, thisArg])
  * 삽입 순으로 callback 함수를 호출한다.
  * thisArg 가 전달될 경우 callback 함수의 this 가 thisArg 로 바인딩 된다.

### 기본 집합 연산 구현

```javascript
// 합집합
Set.prototype.union = function(setB) {
    var union = new Set(this);
    for (var elem of setB) {
        union.add(elem);
    }
    return union;
}

// 교집합
Set.prototype.intersection = function(setB) {
    var intersection = new Set();
    for (var elem of setB) {
        if (this.has(elem)) {
            intersection.add(elem);
        }
    }
    return intersection;
}

// 차집합
Set.prototype.difference = function(setB) {
    var difference = new Set(this);
    for (var elem of setB) {
        difference.delete(elem);
    }
    return difference;
}

// 상위집합
Set.prototype.isSuperset = function(subset) {
  for (var elem of subset) {
    if (!this.has(elem)) {
      return false;
    }
  }
  return true;
}

// 부분집합
Set.prototype.isSubset = function(superSet) {
  for (var elem of this) {
    if (!superSet.has(elem)) {
      return false;
    }
  }
  return true;
}

// Examples
const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 3]);
const setC = new Set([3, 4, 5, 6]);

setA.isSuperset(setB); // => true
setB.isSubset(setA); // => true
setA.union(setC); // => Set [1, 2, 3, 4, 5, 6]
setA.intersection(setC); // => Set [3, 4]
setA.difference(setC); // => Set [1, 2]
```

[reference](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Set)

## Map(딕셔너리)

> Map 객체는 키-값 쌍을 저장하며 각 쌍의 삽입 순서도 기억하는 콜렉션입니다.
> 아무 값(객체와 원시 값)이라도 키와 값으로 사용할 수 있습니다.

*[key, value] 의 구조라 생각하면 이해가 빠르다.*
삽입 순서대로 요소를 조회할 수 있다.
Map 객체의 프로퍼티와 메소드를 살펴보고, hashmap 을 구현해보자.

### 프로퍼티

* size
  * Map 객체의 key/value 페어의 길이를 반환한다.
  
### 메서드

* has(key)
  * Map 객체 안에 key 에 해당하는 key/value 쌍이 있는지 여부를 반환한다.
* set(key, value)
  * Map 객체에 key/value 쌍을 추가한다.
  * 주어진 key 에 기존 value 가 존재한다면 새로 전달받은 value 로 업데이트된다.
  * Map 객체를 반환한다.
* delete(key)
  * Map 객체에서 key 에 해당하는 key/value 쌍을 제거한다.
  * 제거에 성공하면 true, 실패하면 false 를 반환한다.
* clear()
  * Map 객체의 모든 key/value 쌍을 제거한다.
* keys()
  * Map 객체의 삽입순으로 key 로 이루어진 iterator 객체를 반환한다.
* values()
  * Map 객체의 삽입순으로 value 로 이루어진 iterator 객체를 반환한다.
* entries()
  * [key, value] 배열을 포함하는 새로운 iterator 객체를 반환한다.
* forEach(callback[, thisArg])
  * 삽입 순으로 callback 함수를 호출한다.
  * thisArg 가 전달될 경우 callback 함수의 this 가 thisArg 로 바인딩 된다.
  
### 해시맵 구현

```javascript
class HashMap {
  constructor() {
    this._map = new Map();
  }
  
  hashCode(key) {
    var hash = 5381;
    for (var i = 0; i< key.length; i++) {
      hash = hash * 33 + key.charCodeAt(i);
    }
    return hash % 1013;
  }
  
  has(key) {
    const newKey = this.hashCode(key);
    return this_map.has(newKey);
  }
  
  set(key, value) {
    const newKey = this.hashCode(key);
    return this._map.set(newKey, value);
  }
  
  delete(key) {
    const newKey = this.hashCode(key);
    return this._map.delete(newKey);
  }

  clear() {
    this._map.clear();
  }
  
  entries() {
    return this._map.entries();
  }
}

// Examples
const password = new HashMap();
password.set('eloy', '1234');
password.set('hanee', 'qwer');
console.log([...password.entries()]); // [[697, "1234"], [963, "qwer"]]
```

## End

집합과 딕셔너리 자료구조에 대해서 정리해 보았다.
익숙하지 않아 잘 사용하지 않았던 자료구조지만 이번 정리를 통해 적재적소에 사용할 수 있을 것 같다.
