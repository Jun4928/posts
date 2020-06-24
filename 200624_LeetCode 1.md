# LeetCode 1. Two Sum
## JavaScript, Map DataStructure 

[문제링크](https://leetcode.com/problems/two-sum/)

# 문제분석
```
주어진 인풋: nums = [2, 7, 11, 15], target = 9

2 + 7 = 9 이기 때문에
return [0, 1];
```

# 시행착오
처음에 직관적으로 떠올릴 수 있는 방법은 모든 배열을 처음부터 끝까지 순회하며, 해당하는 값과 그 뒤의 나머지 배열에 대해서 다시 한 번 순회하며 쌍을 생성하고 이 쌍의 합이 인자로 들어온 target 값과 같은지를 확인하는 코드다. 이 방법을 brute force 라고 한다. 

아래는 brute force 로 푼 방법이다. 가능한 모든 쌍에 대해서 합이 인자로 들어온 target value가 되는지를 검사한다.

```javascript
// 속도, 메모리 효율성 둘 다 뒤에서 5% 
// brute force 방법 => 너무 느림 가능한 모든 경우의 수를 다 확인하기 때문이다.
const twoSum = function (nums, target) {
  let result = [];
  nums.forEach((first, firstIndex) => {
    const rest = nums.slice(firstIndex + 1);
    rest.forEach((second, secondIndex) => { // 나머지 배열에 대해서 다시 순회하면서 쌍을 만든다.
      if (first + second === target) result = [firstIndex, firstIndex + secondIndex + 1];
      // if (target - first === second)
    });
  });

  return result;
}
```

# 돌파구
위에서 brute force로 구현한 O(n^2) 의 복잡도를 가지는 알고리즘엔 개선할 사항이 분명히 존재한다.
O(n) 으로, 배열을 한 번만 돌고 원하는 쌍을 리턴할수는 없을까? 

그 돌파구는 바로 합은 **빼기**와 관련이 있다. 배열을 순회하며 두 쌍의 합이 target number가 되는지를 검사해도 되지만, `(target number - 현재 순회하는 값)`이 이미 지나온 배열에 존재한다면, 이 두 쌍이 최종 return 할 쌍이 되는 것이다.

```
numbers: [2, 3, 7, 11], target: 9 가 인풋으로 들어 올 때를 예를들면,

- index=0: 9 - 2 = 7, 살펴 볼 이전 배열이 없다.
- index=1: 9 - 3 = 6, 이전 배열인 [2] 를 살펴본다. 6이 없음.
- index=2: 9 - 7 = 2, 이전 배열인 [2, 3] 을 살펴본다. 2가 있음! 
=> 2의 index인 0과 해당 인덱스 2를 쌍으로 리턴 [0, 2] 이 최종리턴된다.
```

하지만, 이렇게 매 번 이전의 배열을 처음부터 끝까지 순회하는 방법은 brute force 방법과 다를 바 없다. for문이 두 번 중첩되기 때문에 시간 복잡도는 O(n^2) 이다.  

배열의 접근을 빠르게 할 수 있는 방법을 떠올려보자. **바로 Hash Map이다.** 배열을 순회 할 때 해당 값(arrayValue)과 index를 Hash Map 에 저장한다. 
```
map.set(arrayValue, index); 
```

이제 우리는 앞서 배열에 대해서 순회했던 모든 비용을 O(1)로 한 번에 해결할 수 있다. Hash Map에는 이제 순회하는 시점의 앞에 배열의 모든 값과 인덱스가 `key, value`로 Hasp Map에 저장되어 있기 때문이다. 

따라서 `(target number - 현재 순회하는 값)`이 Hasp Map에 key값으로 있다면? 이 때 바로 순회를 종료하고, 해당 index 두개를 리턴한다.

# 구현
```javascript
const twoSum = function (nums, target) {
  // Array.entries() => [index, value] 쌍 2차원 배열 return
  // 바로 destructuring 통해서 값을 가져옴.
  const complements = new Map(); // Hasp Map 생성
  for (const [index, number] of nums.entries()) { 
    const complement = target - number;
    if (complements.has(complement)) return [complements.get(complement), index]; // 뺀 값이 이미 Hasp Map에 있다면, 쌍을 찾은 것. 해당 index 두개를 배열로 리턴

    complements.set(number, index); // 순회하며 Hasp Map에 해당 값과 인덱스를 key, value로 저장한다.
  }

  return 'not found';
}

const result = twoSum([2, 7, 11, 15], 9);
console.log(result); // [0, 1];
```