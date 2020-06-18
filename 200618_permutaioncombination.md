# JavaScript로 순열과 조합 알고리즘 구현하기
## 재귀, JavaScript Array Methods

# 순열과 조합(Permutaions and Combinations)
고등학교 수학과정 중 확률과 통계에서 우리가 마주했던 단어들이다. 알고리즘 문제들을 풀다보면 완전탐색으로 해결해야 하는 문제들이 있다. 완전탐색이란 단어 그대로, 생길 수 있는 모든 경우의 수에 대해서 조건이 성립하는지를 알아보는 행위 그 자체다. 프로그래밍으로 완전탐색을 구현할 때에는 조건이 성립하는지 판별여부를 가리는 로직 이전에, 완전탐색을 할 대상에 대해서 자료형을 구축해야 한다. 이 때, 우리가 고등학교때 배웠던 순열과 조합이 빛을 발휘하는 순간이다.

[순열 정의](https://terms.naver.com/entry.nhn?docId=945545&cid=47324&categoryId=47324)
[조합 정의](https://terms.naver.com/entry.nhn?docId=945548&cid=47324&categoryId=47324)

# 조합
먼저 조합에 대해서 살펴보자. 조합을 먼저 꺼내든 이유는, 코드로 구현할 때, 순열보다 쉬웠기 때문이고, 조합의 코드에서 한 줄만 변경하면 순열을 구하는 코드를 작성할 수 있기 때문이다. 이 이유는 바로 조합과 순열의 근본적인 차이점 때문인데, 그것은 바로 **순서**의 여부다.

바로 예를 살펴보도록 하자. 4Combination3 = 4C3을 코드로 구현한다면 다음과 같은 인풋과 아웃풋을 가지게 된다.
```
Input: [1, 2, 3, 4] 
Output: [ [1, 2, 3], [1, 2, 4], [1, 3, 4], [2, 3, 4] ]
```
4C3은 4개중에 3개씩 선택할 때 나올 수 있는 모든 조합을 구한다는 말이다. 이 때, 조합의 순서는 상관이 없다. 
즉 `[1, 2, 3] = [3, 2, 1]` 이렇게 순서가 바뀌어도 같은 구성이기 때문에 하나의 조합으로 취급한다는 이야기다. 

## 돌파구
조합 알고리즘을 구현하는데 있어서 개인적으로 Ah-ha 모먼트가 왔던 순간은 내가 조합을 구할 때 어떻게 구하는지를 관찰 할 때였다. 
```
시작
  1을 선택(고정)하고 -> 나머지 [2, 3, 4] 중에서 2개씩 조합을 구한다.
  [1, 2, 3] [1, 2, 4] [1, 3, 4]
  2를 선택(고정)하고 -> 나머지 [3, 4] 중에서 2개씩 조합을 구한다.
  [2, 3, 4]
  3을 선택(고정)하고 -> 나머지 [4] 중에서 2개씩 조합을 구한다. 
  [] 
  4을 선택(고정)하고 -> 나머지 [] 중에서 2개씩 조합을 구한다.
  []
종료
```
아무리 들어오는 인풋의 배열이 길어진다고 해도, 위의 과정은 변하지 않는다.
배열의 처음부터 하나씩 선택(고정)하면서 뒤의 나머지의 배열에 대해서 조합을 구해서 붙이면 되는 것이다. 
이렇게 나머지에 대해서 일을 위임할 때에는 재귀(Recursion)함수를 사용하는 것이 좋다!
왜냐하면 계속해서 반복 될 일(조합을 구하는 코드)에 대해서 한번만 명시 해 놓고, 들어가는 인자(나를 뺀 나머지)를 바꾸어 주기만 하면 되기 때문이다.


## 구현
- 재귀 종료 조건: 하나를 선택할 때에는, 모든 배열의 원소를 선택해서 리턴한다.
- forEach 문으로, 배열의 모든 원소를 처음부터 끝까지 한 번씩 고정(fixed) 되도록 한다.
- 고정(fixed)된 값의 나머지 배열에 대해서 조합을 구하도록 한다. 이 때, 선택하는 개수를 하나 줄인다.
- 조합을 만들어온 결과에 fixed 고정된 값을 앞에 붙여서 리턴할 배열에 spread syntax 로 배열화 한 후에 리턴한다.
- 2C3, 1C2 같이 선택하려는 개수가 많으면 빈 배열이 return 되기 때문에 위의 예에서 3과 4를 선택할 때에는 빈 배열이 돌와아서 최종 결과값에 포함되지 않는다.

```javascript
const getCombinations = function (arr, selectNumber) {
  const results = [];
  if (selectNumber === 1) return arr.map((value) => [value]); // 1개씩 택할 때, 바로 모든 배열의 원소 return

  arr.forEach((fixed, index, origin) => {
    const rest = origin.slice(index + 1); // 해당하는 fixed를 제외한 나머지 뒤
    const combinations = getCombinations(rest, selectNumber - 1); // 나머지에 대해서 조합을 구한다.
    const attached = combinations.map((combination) => [fixed, ...combination]); //  돌아온 조합에 떼 놓은(fixed) 값 붙이기
    results.push(...attached); // 배열 spread syntax 로 모두다 push
  });

  return results; // 결과 담긴 results return
}

const example = [1,2,3,4];
const result = getCombinations(example, 3);
console.log(result);
// => [ [ 1, 2, 3 ], [ 1, 2, 4 ], [ 1, 3, 4 ], [ 2, 3, 4 ] ]
```

# 순열
순열은, 조합을 이해하고 나면 쉬워진다. 왜냐하면 조합에서 확장된 개념이기 때문이다. 조합은 **순서**에 상관없이 선택한 것이라면, 순열은 **순서**가 중요하다. 실제로 순열을 구하는 공식도 조합으로부터 도출 가능하다. 
```
nPr = nCr × r!
조합을 구한 후, 선택하려는 수의 factorial 을 곱하면 순열을 구하는 공식이 탄생한다.
```

위의 조합의 예에서 확장시켜보면, `[1, 2, 3, 4]` 에서 3개씩 뽑아 순열을 구한다고 한다면,
```
[1, 2, 3] => 이 안에서 순서를 바꾸는 경우의 수 => 3! 
[1, 2, 4] => 이 안에서 순서를 바꾸는 경우의 수 => 3!
[1, 3. 4] => 이 안에서 순서를 바꾸는 경우의 수 => 3!
[2, 3, 4] => 이 안에서 순서를 바꾸는 경우의 수 => 3!
```
즉, 각각 조합에서 순서를 바꾸는 경우의 수 `3!` 이 곱해지기 때문에 결합법칙으로 인해서 조합을 구하는 공식에 `3!`을 곱할 수 있게 되는 것이다. 

## 돌파구
위에서 설명한 공식으로 부터 아이디어를 얻었다. 그러니까, 조합을 구하는 코드와 별반 다르지 않겠구나! 라는 생각이 들었고, 조합을 구하는 코드에서 순열을 구하는 재귀함수의 로직을 구현하면 되겠구나 라는 생각으로 확장했다. 

먼저, 재귀의 종료 조건은 조합을 구하는 함수와 동일하다. 왜냐하면, 한 개씩 선택한다고 하면 순서가 의미가 없어지기 때문이다. 

`[1,2,3,4]` 에서 3개를 선택해서 순열을 만드는 코드의 내부를 의사코드로 적어보면 다음과 같다.
```
1(fixed) => permutation([2, 3, 4]) => 2(fixed) => permutation([3, 4]) => ...
2(fixed) => permutation([1, 3, 4])
3(fixed) => permutation([1, 2, 4])
4(fixed) => permutation([1, 2, 3])
```
이번에는 나를 제외한 배열의 모든 원소에 대해서 순열을 구하는 함수에 일을 위임하면 된다.
아래의 코드를 살펴보면 정확히 다른 코드는 동일하고 배열의 나머지를 구하는 코드만 바뀌었음을 볼 수 있다. 
`const rest = [...origin.slice(0, index), ...origin.slice(index+1)] // 해당하는 fixed를 제외한 나머지 배열`

## 구현
```javascript
const getPermutations= function (arr, selectNumber) {
  const results = [];
  if (selectNumber === 1) return arr.map((value) => [value]); // 1개씩 택할 때, 바로 모든 배열의 원소 return

  arr.forEach((fixed, index, origin) => {
    const rest = [...origin.slice(0, index), ...origin.slice(index+1)] // 해당하는 fixed를 제외한 나머지 배열 
    const permutations = getPermutations(rest, selectNumber - 1); // 나머지에 대해 순열을 구한다.
    const attached = permutations.map((permutation) => [fixed, ...permutation]); // 돌아온 순열에 대해 떼 놓은(fixed) 값 붙이기
    results.push(...attached); // 배열 spread syntax 로 모두다 push
  });

  return results; // 결과 담긴 results return
};

const example = [1,2,3,4];
const result = getPermutations(example, 3);
console.log(result);
// => [
//   [ 1, 2, 3 ], [ 1, 2, 4 ],
//   [ 1, 3, 2 ], [ 1, 3, 4 ],
//   [ 1, 4, 2 ], [ 1, 4, 3 ],
//   [ 2, 1, 3 ], [ 2, 1, 4 ],
//   [ 2, 3, 1 ], [ 2, 3, 4 ],
//   [ 2, 4, 1 ], [ 2, 4, 3 ],
//   [ 3, 1, 2 ], [ 3, 1, 4 ],
//   [ 3, 2, 1 ], [ 3, 2, 4 ],
//   [ 3, 4, 1 ], [ 3, 4, 2 ],
//   [ 4, 1, 2 ], [ 4, 1, 3 ],
//   [ 4, 2, 1 ], [ 4, 2, 3 ],
//   [ 4, 3, 1 ], [ 4, 3, 2 ]
// ]
```