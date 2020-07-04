# JavaScript로 Set(집합), 관련 함수(합집합, 교집합, 차집합)들 구현하기
## JavaScript ES6+ Set class, Array Methods

# Set 자료구조
Set(집합) 자료구조란, 값들의 집합을 이야기한다. 우리가 수학 시간에 배웠던 그 집합이라고 보면된다. 집합 자료구조의 가장 큰 특징은 **하나의 값은 Set(집합) 안에 오로지 한 개만 존재한다** 이다.

즉, 하나의 값들이 한 번만 나오는다는 특징(unique)은 Set의 성질이다. 
JavaScript 에서는 `Set` 클래스가 내장되어 있다. 잘 사용하기만 하면 끝.

심지어 python 에서는 `set` 클래스에 대해 합집합, 교집합, 차집합을 너무나 쉽게 구할 수 있다. 정리 및 비교를 위해 잠깐 짚고 넘어가보면, 바로 직관적으로 떠오르는 `& (교집합), | (합집합), - (차집합)` 을 사용하면 된다.
- **|**: 합집합 union
- **&**: 교집합 intersect
- **-**: 차집합 complement
```python
setA = set([1, 2, 3, 4, 5, 6, 1, 2, 3]) # {1, 2, 3, 4, 5, 6}
setB = set([1,2,3,7,7,3,3,3,3,3,3,3,2,2,2,2,2]) # {1, 2, 3, 7}

union = setA | setB # {1, 2, 3, 4, 5, 6, 7} 합집합도 고유한 값들만 가지고 있다.
intersect = setA & setB # {1, 2, 3} 두 집합에 모두 있는 값들
complement = setA - setB # {4, 5, 6}  setB에도 있는 값들을 setA 에서 뺀 것 (setA - (setA&setB)): setA - 교집합
```

JavaScript 에서는 위와 같은 기능을 Set클래스가 있지만, python 처럼 집합 연산자를 제공하지는 않는다. 알고리즘 문제를 풀 때 친구는 집합 연산자를 사용해서 쉽게 푸는 모습에 충격을 받고 나도 나만의 집합 메소드를 구현해 봐야겠다고 생각했다. 

JavaSciprt ES6+ 에 추가된 다양한 array 메소드들은 정말 코딩을 우아하고 즐겁게 해준다.

# 구현하기
## JavaScript Set 자료구조 사용해서 구현
```javascript
const setA = new Set([1,2,3,4,5,6,7,8]); // array => set 으로 변환 (알아서 중복제거 됨)
const setB = new Set([3,4,5,6,7]);

const union = new Set([...setA, ...setB]); // set => array spread syntax 사용
const intersection = new Set([...setA].filter(x => setB.has(x))); // 둘 다 있는 것들을 솎아낸다.
const difference1 = new Set([...setA].filter(x => !setB.has(x))); // set1 - set2
const difference2 = new Set([...setB].filter(x => !setA.has(x))); // set2 - set1
const symmetricDifference = new Set([...difference1, ...difference2]); // union - intersection

const isSuperSet = function (superset, subset) { // check if left set(superset) is a superset of right set(subset)
  for (let element of subset) if (!superset.has(element)) false; // 한 번이라도 superset으로 들어온 집합이 subset으로 들어온 집합의 값을 가지고 있지 않다면 => false 
  return true;
}
```

## JavaScript Array로 구현 (Set 자료구조 사용 X)
```javascript
const arrayA = [1,2,3,4,5,6,7,8]
const arrayB = [3,4,5,6,7]

const arrayToSet = (array) => { // get unique values
  return array.filter((value, index) => array.indexOf(value) === index);
}

const union = (first, second) => { // first: set, second: set
  const union = [...first]; 
  second.forEach((value) => { if (!union.includes(value)) union.push(value); }) 
  // 없을 때에만 추가 해준다. (합집합 중복 방지)
  return union;
}

const intersect = function(first, second) { // first: set, second: set
  return first.filter(value => second.includes(value)); // 둘 다 있으면 교집합
}

const complement = function(first, second) { // first: set, second: set
  return first.filter(value => !second.includes(value)); // 중복되는 것 제거하면 차집합
}


const setA = arrayToSet(arrayA);
const setB = arrayToSet(arrayB);

const unionOfTwoSets = union(setA, setB);
const intersection = intersect(setA, setB);
const difference1 = complement(setA, setB);
const difference2 = complement(setB, setA);
const symmetricDifference = union(difference1, difference2);

const isSuperSet = function (superset, subset) { // check if left set(superset) is a superset of right set(subset)
  for (let element of subset) if (!superset.includes(element)) false; // 한 번이라도 superset으로 들어온 집합이 subset으로 들어온 집합의 값을 가지고 있지 않다면 => false 
  // 배열이기 때문에 includes 메소드 사용
  return true; 
}
```