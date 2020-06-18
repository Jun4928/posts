# Programmers Level2 숫자야구
## 순열, 재귀, 완전탐색

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42841)

# 돌파구
완전탐색을 이용해야 하는 문제다. 가능한 모든 경우의 수에 대해서 필터링을 거치는 대표적인 문제이다. 
주어진 인풋을 다 통과하면 최종 답안이 되는 것이다. 앞서 포스팅 했던 [후보키](https://medium.com/@jun.choi.4928/programmers-level2-후보키-54065e578b4f) 문제와 동일한 방식이다.

이번에도 필터링을 거치기 전에, 가능한 모든 조합을 만드는 것이 오히려 더 중요하고 어려웠던 문제였다. 그래서 [순열과 조합](https://medium.com/@jun.choi.4928/javascript로-순열과-조합-알고리즘-구현하기-21df4b536349)에 대한 글을 따로 할애했다.

학창시절 야구게임을 친구들과 해보았다면 알겠지만, 선택할 수 있는 숫자의 경우의 수는 1부터 9까지의 수 중에서 3개를 선택하는 것이고 **순서**에 의해서 숫자가 구분된다. => 즉 순열 알고리즘을 사용해야한다. 공식으로 나타내면 9P3 이다.
`[1, 2, 3, 4, 5, 6, 7, 8, 9]` 의 배열에서 3개씩 선택해서 순열을 구한 후, 이 모든 경우의 수에 대해서 완전탐색을 실행하면 된다.

이 솔루션 함수 내에서의 로직을 다시 한 번 설명하면,

1. 순열 알고리즘으로 모든 숫자야구에 사용될 수 있는 모든 수의 순열을 구하기
2. 배열에 담긴 순열에 대해 완전탐색하기, 이 때 주어진 문제의 조건(strike, ball) 만 잘 비교 해 주면 된다.
3. 주어진 입력에 대해서 모든 조건을 만족하면 최종 답안에 필터링 된다.

# 구현
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

const solution = function baseball (baseballInputs) {
  const oneToNine = new Array(9).fill(1).map((x, index) => x + index);
  const candidates = getPermutations(oneToNine, 3); // 1-9 까지 3개를 택한 후 모든 순열을 구한다.

  const isPassed = function (candidate, baseballInput) {
    const [numbers, inputStrikes, inputBalls] = baseballInput;
    const numbersToArray = [...numbers.toString()].map(x => parseInt(x));

    let strikes = 0;
    let balls = 0;
    numbersToArray.forEach((number, index) => {
      if (candidate[index] === number) strikes++; // 같은 자리 strike +1
      else if (candidate.includes(number)) balls++; // 숫자 포함 할 때 ball +1
    });

    return strikes === inputStrikes && balls === inputBalls; // strike, ball 이 같아야 통과
  }

  const filteredCandidates = candidates.filter((candidate) => { // 모든 가능한 순열에 대해서 filter 를 거친다.
    for (let i = 0; i < baseballInputs.length; i++) { // 모든 입력에 대해서 검사한다.
      if (!isPassed(candidate, baseballInputs[i])) return false; //한 번이라도 통과 못하면 false
    }

    return true; // for 문에서 모두 다 조건에 대해 통과하면 true
  });

  return filteredCandidates.length;
}

solution([[123, 1, 1], [356, 1, 0], [327, 2, 0], [489, 0, 1]]);
```